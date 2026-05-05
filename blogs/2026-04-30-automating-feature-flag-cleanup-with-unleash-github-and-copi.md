---
title: "Automating feature flag cleanup with Unleash, GitHub, and Copilot"
url: "https://www.getunleash.io/blog/automating-feature-flag-cleanup-github-copilot"
date: "Thu, 30 Apr 2026 10:33:08 +0000"
author: "Alex Casalboni"
feed_url: "https://www.getunleash.io/feed"
---
<p>Most feature flags do their job and then quietly stick around. The rollout finished six months ago, the variant has been chosen, the metrics are stable, and yet the if blocks, helper functions, test fixtures, and dead branches are still in the repo. Every developer who reads that code pays a small tax to figure out which path is now real, and the people who shipped the feature have already moved on to the next thing.</p>
<p>This post shows how to close that loop automatically. You mark a flag as Completed in the Unleash dashboard, and a few seconds later GitHub Copilot opens a pull request that removes the flag from your codebase. Behind the scenes there is a webhook, a labeled GitHub issue, a small GitHub Actions workflow, and the <a href="https://docs.getunleash.io/integrate/github-copilot.md">Unleash MCP server</a> that gives Copilot framework-aware cleanup instructions.</p>
<p>Let’s build the whole thing from scratch.</p>
<h2>Why automate flag cleanup</h2>
<p>Stale flags carry real cost. They split simple functions into two paths that no one will ever toggle again, they make code reviews harder because reviewers have to mentally evaluate dead branches, and they create a small but non-zero risk that someone re-enables a feature that the team had already decided to drop. Worse, the longer a flag sits unused, the more the surrounding code drifts. A helper that only existed to support the discarded variant gets imported somewhere else, a test that asserts behavior under both branches becomes a flaky reminder of a decision no one remembers making.</p>
<p>The completion moment is the right time to act on all of this. When you mark a flag complete in Unleash, you already record the outcome the team chose: kept, kept with a specific variant value, or discarded. That is the same information a cleanup task needs. We just have to deliver it to the right place, in a format an automated agent can act on.</p>
<h2>The end-to-end workflow at a glance</h2>
<p>The workflow has five steps. Each one is small, and each artifact is a few lines of configuration or YAML.</p>
<ol>
<li>You mark a feature flag as completed in the Unleash UI.</li>
<li>Unleash fires a <strong>feature-completed</strong> webhook to the GitHub REST API and creates an issue tagged with the <strong>unleash-flag-completed</strong> label.</li>
<li>The issue contains the flag name, the chosen outcome, and step-by-step cleanup guidance.</li>
<li>A GitHub Actions workflow listens for that label and assigns the issue to @copilot.</li>
</ol>
<p>Copilot picks up the task, queries the <a href="https://docs.getunleash.io/integrate/github-copilot.md">Unleash MCP server</a> for language-aware cleanup instructions, removes the flag, and opens a PR for human review.</p>
<a href="https://cdn.getunleash.io/uploads/2026/04/cleanup-workflow.svg"><img alt="" class="alignnone size-large wp-image-14063" height="1024" src="https://cdn.getunleash.io/uploads/2026/04/cleanup-workflow.svg" width="1024" /></a>
<p>The rest of the post walks through each step with the actual config you need.</p>
<h2>Step 1: Mark a feature flag as completed in Unleash</h2>
<p>Open the flag in your Unleash project, find the lifecycle section, and click <strong>Mark ready for cleanup</strong> under the flag’s lifecycle menu. Unleash asks you to record the outcome you reached. You can choose <strong>Kept</strong> if the feature is staying in the product, <strong>Kept with variant</strong> if the rollout used variants and you want to keep one specific value, or <strong>Discarded</strong> if the feature is being removed entirely.</p>
<p>Marking a flag as complete does not change its configuration. The flag stays where it is, traffic continues to flow, and Unleash keeps recording usage metrics. That last part matters because production metrics tell you whether any callers are still hitting the flag after you thought you were done with it. The Completed stage is a signal that the rollout decision has been made and that cleanup work should start, not a switch that turns the feature off.</p>
<img alt="" class="alignnone size-full wp-image-14064" height="729" src="https://cdn.getunleash.io/uploads/2026/04/mark-as-complete-with-variant.png" width="678" />
<p>&nbsp;</p>
<p>The interesting side effect for our automation is that this action emits a feature-completed event with the chosen outcome attached as event.data.status and, when relevant, a event.data.statusValue field with the variant name. We will template both of those into the GitHub issue.</p>
<h2>Step 2: Send a webhook on the feature-completed event</h2>
<p>In the Unleash dashboard, head to <strong>Configure &gt; Integrations</strong> and add a new <strong>Webhook</strong> integration. The full reference for this integration is in the <a href="https://docs.getunleash.io/integrate/webhook">Unleash webhook docs</a>, but we only need a few fields.</p>
<p>Set the <strong>Webhook URL</strong> to the GitHub REST API endpoint that creates an issue:</p>
<pre><code class="language-none">
https://api.github.com/repos/&lt;OWNER&gt;/&lt;REPO&gt;/issues
</code></pre>
<p>Replace <strong>&lt;OWNER&gt;</strong> and <strong>&lt;REPO&gt;</strong> with your target. This is the standard <a href="https://docs.github.com/en/rest/issues/issues?apiVersion=2022-11-28#create-an-issue">create-an-issue endpoint</a>, so we just need to send a token and a body.</p>
<p>Unleash webhooks have a dedicated Authorization field, so paste your GitHub token there as &#8220;<strong>Bearer &lt;GITHUB_TOKEN&gt;</strong>&#8221; and you are done. It’s very important that you prepend “<strong>Bearer</strong> “ to the GitHub token for the API call to work.</p>
<p>The token can be a <a href="https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens">fine-grained personal access token</a> or a GitHub App token with <strong>Issues: Read and write</strong> permission on the target repository. Keep its scope narrow. There is no reason for this token to see anything other than the one repo that should receive cleanup issues.</p>
<p>Subscribe the integration only to the feature-completed event. Unleash supports many other lifecycle events, and you can wire some of them into different workflows later, but for cleanup we want one event and one path.</p>
<p>The last piece is the <strong>Body Template</strong>. Unleash uses <a href="https://mustache.github.io/">Mustache</a> to render the body, with the full event available under event. Drop in this template:</p>
<pre><code class="language-json">
{
  "labels": ["unleash-flag-completed"],
  "title": "&#x1f9f9; Flag {{ event.featureName }} marked as completed",
  "body": "The feature flag `{{ event.featureName }}` has been marked as **completed** in Unleash.\n\n### &#x1f4dd; Details\n- &#x1f3f7; Flag name: `{{ event.featureName }}`\n- &#x1f4c8; Feature outcome: **{{ event.data.status }}**{{#event.data.statusValue}}\n- &#x1f3af; Variant to keep: **{{ event.data.statusValue }}**{{/event.data.statusValue}}\n\n### &#x2705; Suggested cleanup\n1. &#x1f50d; Search the codebase for all references to this flag.\n2. &#x2702; Remove conditional logic and retain only the code for the intended outcome.\n3. &#x1f9fc; Clean up related tests, unused helpers, imports, and configuration entries. Use the Unleash MCP and the cleanup_flag tool. Remember to fix any lint issues.\n\n_This issue was automatically generated by an Unleash integration._"
}
</code></pre>
<p>A couple of details are worth calling out. The <strong>{{#event.data.statusValue}} &#8230; {{/event.data.statusValue}}</strong> block is a Mustache section, which only renders when the field is present. That keeps the variant line out of the issue when the team simply chose to keep or discard the feature without picking a variant.</p>
<p>All those <strong>\n</strong> escapes are hard to read in JSON form, so here is what the rendered issue body looks like in plain markdown:</p>
<pre><code class="language-json">
The feature flag `{{ event.featureName }}` has been marked as **completed** in Unleash.

### &#x1f4dd; Details
- &#x1f3f7;  Flag name: `{{ event.featureName }}`
- &#x1f4c8; Feature outcome: **{{ event.data.status }}**
- &#x1f3af; Variant to keep: **{{ event.data.statusValue }}**

### &#x2705; Suggested cleanup
1. &#x1f50d; Search the codebase for all references to this flag.
2. &#x2702;  Remove conditional logic and retain only the code for the intended outcome.
3. &#x1f9fc; Clean up related tests, unused helpers, imports, and configuration entries. Use the Unleash MCP and the cleanup_flag tool. Remember to fix any lint issues.

_This issue was automatically generated by an Unleash integration._
</code></pre>
<p>The cleanup instructions explicitly mention the cleanup_flag MCP tool, which is the hook that Copilot will use later.</p>
<h2>Step 3: A labeled GitHub issue is born</h2>
<p>When the webhook fires, GitHub returns a <strong>201 Created</strong> response and you get an issue labeled as <strong>unleash-flag-completed</strong> with all the details to start cleaning up.</p>
<p>And the label is doing real work. It decouples Unleash from the workflow that triggers the agent. You can also use it to filter cleanup work in your project boards, route different label patterns to different repositories, or pause automation by simply removing the label. GitHub creates the label on first use if it does not already exist, so you do not need to seed it manually.</p>
<h2>Step 4: Trigger a GitHub Actions workflow on the label</h2>
<p>Save this file as <strong>.github/workflows/cleanup-workflow.yml</strong> in the repo that should receive cleanup PRs:</p>
<pre><code class="language-yaml">
name: Trigger Copilot on Label
on:
  issues:
    types: [labeled]

jobs:
  assign-copilot:
    if: github.event_name == 'issues' &amp;&amp; github.event.label.name == 'unleash-flag-completed'
    runs-on: ubuntu-latest
    permissions:
      issues: write
    steps:
      - name: Assign Copilot Agent
        run: |
          gh issue edit ${{ github.event.issue.number }} --repo ${{ github.repository }} --add-assignee "@copilot"
        env:
          GH_TOKEN: ${{ secrets.COPILOT_TRIGGER_TOKEN }}
</code></pre>
<p>The <strong>on: issues, types: [labeled]</strong> trigger fires for every label change on every issue, so the if: guard in the job is the part that keeps the workflow focused. Only issues that carry the <strong>unleash-flag-completed</strong> label proceed to the assignment step.</p>
<p>The actual work is the <strong>gh issue edit</strong> call that adds <strong>@copilot</strong> as an assignee. That single line is what kicks off the Copilot coding agent. The workflow uses a separate <strong>COPILOT_TRIGGER_TOKEN</strong> secret rather than the default <strong>GITHUB_TOKEN</strong> because the default workflow token cannot assign Copilot. You can mint a fine-grained PAT or a GitHub App token with <strong>Issues: Read and write</strong> access on the repo, store it under <strong>Settings &gt; Secrets and variables &gt; Actions</strong>, and you are done.</p>
<p>Two prerequisites to check before you ship this.</p>
<ol>
<li>The repository (or the org that owns it) needs an active Copilot plan that includes the coding agent, which today means <a href="https://docs.github.com/en/copilot/concepts/about-assigning-tasks-to-copilot">Copilot Pro, Pro+, Business, or Enterprise</a>. For Business and Enterprise, an admin has to enable the policy that allows Copilot to be assigned to issues.</li>
<li>The user identity attached to your token needs permission to assign issues in the repo, which is true by default for repository collaborators.</li>
</ol>
<h2>Step 5: Copilot cleans up the flag using the Unleash MCP</h2>
<p>This is where the workflow earns its keep. As soon as <strong>@copilot</strong> lands on the issue, GitHub provisions an ephemeral environment for the agent. Copilot reads the issue body, sees the flag name, the chosen outcome, the optional variant, and the explicit instruction to use the Unleash MCP, and starts working on a branch.</p>
<p>To make the Unleash MCP server available to Copilot in the cloud, you configure it in the repository settings on GitHub. Go to <strong>Settings &gt; Copilot &gt; Cloud agent</strong>, scroll to the <strong>MCP configuration</strong> section, and paste in the JSON below. The full reference is in the GitHub docs on <a href="https://docs.github.com/en/copilot/how-tos/agents/copilot-coding-agent/extending-copilot-coding-agent-with-mcp">extending Copilot coding agent with MCP</a>.</p>
<pre><code class="language-json">
{
  "mcpServers": {
    "unleash-mcp": {
      "type": "local",
      "command": "npx",
      "args": ["-y", "@unleash/mcp@latest", "--log-level", "error"],
      "env": {
        "UNLEASH_BASE_URL": "$COPILOT_MCP_UNLEASH_BASE_URL",
        "UNLEASH_PAT": "$COPILOT_MCP_UNLEASH_PAT",
        "UNLEASH_DEFAULT_PROJECT": "default"
      },
      "tools": ["*"]
    }
  }
}
</code></pre>
<p>The Copilot coding agent only exposes secrets and variables whose names start with <strong>COPILOT_MCP_</strong>. So add <strong>COPILOT_MCP_UNLEASH_BASE_URL</strong> and <strong>COPILOT_MCP_UNLEASH_PAT</strong> under <strong>Settings &gt; Environments &gt; copilot</strong> (create a new environment named “copilot” if it doesn’t exist already) and reference them from the MCP config as shown above. Make sure the base URL ends with <strong>/api</strong>, since that is what the Unleash MCP package expects. The PAT only needs permission to read and update flags in the relevant project. The same JSON shape works in <strong>.vscode/mcp.json</strong> for local development, and full IDE setup details live in the <a href="https://docs.getunleash.io/integrate/github-copilot.md">Unleash MCP integration docs</a>.</p>
<p>Once the MCP server is reachable, Copilot has nine tools to work with. Three of them are the ones it leans on for a cleanup task: <strong>detect_flag</strong> to find every reference to the flag in the repo, <strong>get_flag_state</strong> to confirm the chosen variant when the issue specifies one, and <strong>cleanup_flag</strong> to receive a list of file locations and the framework-specific removal instructions. The MCP knows what unleash-client for Node.js looks like, what the Python SDK looks like, what guard patterns Java and Go developers tend to write, and it adapts the cleanup advice to the file it is editing.</p>
<h3>What the before and after will look like</h3>
<p>For a Node.js Express service, the before and after look like this. Before:</p>
<pre><code class="language-js">
const { isEnabled } = require('unleash-client');

app.post('/checkout', async (req, res) =&gt; {
  const context = { userId: req.user.id };

  if (isEnabled('stripe-payment-integration', context)) {
    const result = await stripeService.processPayment(req.body);
    return res.json(result);
  } else {
    const result = await legacyPaymentService.process(req.body);
    return res.json(result);
  }
});
</code></pre>
<p>After Copilot applies the cleanup with <strong>outcome=kept</strong>:</p>
<pre><code class="language-js">
app.post('/checkout', async (req, res) =&gt; {
  const result = await stripeService.processPayment(req.body);
  return res.json(result);
});

</code></pre>
<p>The agent removes the <strong>unleash-client</strong> import if it is no longer used elsewhere in the file, drops the <strong>context</strong> object that only existed to feed into <strong>isEnabled</strong>, deletes the <strong>legacyPaymentService</strong> import if it is now orphaned, and updates any tests that asserted on both branches. The same shape applies to Python, Go, Java, and other supported languages: the MCP gives Copilot the framework-aware patterns and the agent applies them.</p>
<p>The agent commits the changes on a branch, pushes, and opens a pull request that links back to the original issue. From the team&#8217;s point of view, the cleanup arrives as a normal PR with a small, focused diff.</p>
<h2>The workflow in practice</h2>
<p>Here is what the running pipeline looks like end to end on a real flag, from the moment Unleash dispatches the webhook to the cleanup PR landing in GitHub.</p>
<img alt="" class="alignnone size-large wp-image-14065" height="475" src="https://cdn.getunleash.io/uploads/2026/04/webhook-success-1024x475.png" width="1024" />
<p>&nbsp;</p>
<p>The Unleash dashboard shows the <strong>feature-completed</strong> event was dispatched and the GitHub API returned a successful response. This view is also the first place to look if anything misfires later, since failed deliveries surface their status code and response body right here.</p>
<p>&nbsp;</p>
<img alt="" class="alignnone size-large wp-image-14066" height="618" src="https://cdn.getunleash.io/uploads/2026/04/github-issue-created-1024x618.png" width="1024" />
<p>&nbsp;</p>
<p>The cleanup issue lands in the target repo with the <strong>unleash-flag-completed</strong> label, the workflow assigns <strong>@copilot</strong>, and the GitHub UI shows an active Copilot session working on the task. From this point on, the agent is operating in its own ephemeral environment.</p>
<p>&nbsp;</p>
<img alt="" class="alignnone size-large wp-image-14067" height="1017" src="https://cdn.getunleash.io/uploads/2026/04/copilot-session-running-1024x1017.png" width="1024" />
<p>&nbsp;</p>
<p>Opening the running session reveals the agent&#8217;s live trace: it boots the ephemeral environment, scans the repository for references to the flag, drafts a cleanup plan, and calls into the Unleash MCP server to fetch the framework-aware removal instructions. This is also the place to step in mid-run if you want to nudge the agent in a different direction.</p>
<p>&nbsp;</p>
<img alt="" class="alignnone size-large wp-image-14068" height="799" src="https://cdn.getunleash.io/uploads/2026/04/copilot-pr-created-1024x799.png" width="1024" />
<p>&nbsp;</p>
<p>A few minutes later Copilot pushes a branch and opens a pull request with the flag-related code removed and the surrounding tests, helpers, and imports tidied up. The PR links back to the originating issue so the audit trail stays clean and reviewers can jump back to the Unleash event that started everything.</p>
<h2>Production hardening notes</h2>
<p>Before turning this on across an organization, a few details are worth getting right.</p>
<p>Keep the webhook subscribed only to the <strong>feature-completed</strong> event. Unleash supports a long list of lifecycle events, and a noisier subscription will start opening issues for things you do not want automated yet. If you later want to handle archival or stale flags, build a second webhook with its own label and its own workflow.</p>
<p>Use a fine-grained PAT or, better, a small GitHub App for the webhook. The token only needs <strong>Issues: Read and write</strong> on a single repository. A token that is wider than that is a token you have to worry about.</p>
<p>Wait for production usage metrics on the flag to drop to zero after merging the PR, before archiving the flag. The Unleash UI keeps showing usage data after a flag is marked Completed for exactly this reason. The MCP gives you a clean diff, but a human should still gate the merge and archival, especially when the outcome was <strong>kept with variant</strong> and the change rewrites a critical path.</p>
<p>For monorepos, point Copilot at the right working directory in the issue body or in <strong>.github/copilot-instructions.md</strong>. The agent will otherwise assume the repo root and may take longer to find the references.</p>
<h2>Wrap up</h2>
<p>You now have a workflow where flag completion in Unleash directly produces a Copilot-authored pull request, with humans involved only at the moments that matter: deciding the outcome, and approving the merge. Everything in between, the issue creation, the routing, the cleanup logic, the framework-specific edits, runs without anyone copy-pasting flag names into a JIRA ticket.</p>
<p>If you want to extend this further, the <a href="https://docs.getunleash.io/integrate/github-copilot.md">Unleash GitHub Copilot integration</a> exposes a full set of MCP tools beyond <strong>cleanup_flag</strong>. The same pattern can drive flag creation when a new feature lands, rollout adjustments based on metrics, or environment-specific toggles tied to deployment events. Cleanup is just the easiest place to start, because the moment of completion already tells you exactly what the team decided.</p>
