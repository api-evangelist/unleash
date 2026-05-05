---
title: "Without feature flags, your AI software will break you"
url: "https://www.getunleash.io/blog/cloudflare-flagship-feature-flags"
date: "Wed, 22 Apr 2026 12:22:08 +0000"
author: "Egil Østhus"
feed_url: "https://www.getunleash.io/feed"
---
<p>Good news for software developers: Cloudflare just announced a native feature-flag service called <a href="https://blog.cloudflare.com/flagship/">Flagship</a>. Feature flags are the foundational technology behind FeatureOps, a set of technologies and practices pioneered by Unleash and others that control how software behaves in production.</p>
<p>Software developers now have one more option to move faster, reduce toil, secure applications, and speed recovery. It is also good news for millions of consumers who rely on web services built on Cloudflare. These services will now be more resilient in the face of inevitable outages. Bravo to Cloudflare and the <a href="https://openfeature.dev/ecosystem/?instant_search%5BrefinementList%5D%5Btype%5D%5B0%5D=Provider&amp;instant_search%5BrefinementList%5D%5Bvendor%5D%5B0%5D=Unleash">OpenFeature community</a> on which the service is built.</p>
<p>In this blog post, we will discuss why this announcement matters more than you might think and how to succeed in building enterprise applications in the Age of AI.</p>
<h2>A brief history of feature flags</h2>
<p><a href="https://github.com/Unleash/unleash/commit/f80d9a8827edbbd3725564cc4144afea807339d0">Starting as far back as 2014</a>, with feature flag services like Unleash, instead of relying on a software deployment to change application behavior, for example, enabling or disabling a capability like an API endpoint or UI element, developers could flip a switch at runtime to turn code paths on or off. This simple technique allowed teams to move away from shipping software with endless testing cycles and infrequent releases that still failed to catch bugs, to releasing hundreds of times a day.</p>
<p>This technique was popularized by <a href="https://martinfowler.com/articles/feature-toggles.html">Pete Hodgson</a> writing on the blog of Thoughtworks leader Martin Fowler in 2017, who described feature flags (what he called feature toggles at the time), as “a powerful technique… to modify system behavior without changing code.”</p>
<p>Fast forward to today, and feature flags are widely seen as an engineering best practice alongside techniques like version control, CI/CD, and multi-factor authentication. You don’t have to do any of these things to ship code, but you forgo them at your own peril.</p>
<h2>Feature flags: Widely recognized best practice</h2>
<p>So, who recommends feature flags? <a href="https://www.thoughtworks.com/radar/tools/unleash">Thoughtworks, of course</a>. But also:</p>
<p><a href="https://www.gartner.com/doc/reprints?id=1-2LNB5HM6&amp;ct=250811&amp;st=sb#cppdip.830991">Gartner</a>: “Almost all businesses can benefit from faster, more reliable delivery of software, the impact of feature management in that sense could be universal.”</p>
<p><a href="https://status.cloud.google.com/incidents/ow5i3PPK96RduMcb1SsW">Google</a>: Postmortem from a 2025 outage costing hundreds of millions, “If this had been flag protected, the issue would have been caught in staging.”</p>
<p><a href="https://github.blog/news-insights/company-news/github-availability-report-march-2026/">GitHub</a>: After a series of outages in March 2026, dropping to below 90% availability,  “We have added a killswitch and improved monitoring to the caching mechanism.“</p>
<p>And <a href="https://blog.cloudflare.com/18-november-2025-outage/">Cloudflare itself</a>. In November 2025, Cloudflare went down for more than five hours after a routine configuration change cascaded across the network. In their postmortem, CEO Matthew Prince committed to “enabling more global kill switches,” which are a special type of feature flag, so the next routine change could be <a href="https://blog.cloudflare.com/fail-small-resilience-plan/">contained without relying on a software deployment</a> that had already failed, before it became a global incident.</p>
<p>Five months later, they shipped Flagship, a native feature flag service built into Cloudflare Workers.</p>
<h2>Sounds great…but</h2>
<img alt="walking on rakes" class="alignnone size-full wp-image-13846" height="432" src="https://cdn.getunleash.io/uploads/2026/04/walking-on-rakes.gif" width="576" />
<p>&nbsp;</p>
<p>With feature flags seen as a near-universal best practice, validated over and over again in the aftermath of outages, we still hear two things from the massive global enterprises we work with when we talk about wrapping all non-trivial code in flags.</p>
<ol>
<li>Feature flags sound great, but we can’t do anything that slows us down, we need to move faster.</li>
<li>Our teams are not ready to adopt feature flags yet, they need more training.</li>
</ol>
<p>Both of these tend to be misunderstandings. Feature flags are the most direct mechanism for increasing release velocity, and they are much simpler to implement than most people realize when following a few <a href="https://docs.getunleash.io/guides/feature-flag-best-practices">best practices</a>.</p>
<p>As a result, we’ve seen many of the world’s largest organizations deploy <a href="http://featureops.io">FeatureOps</a> organization-wide, accelerating software development while making it less risky.</p>
<p>But AI changes the game completely. It makes FeatureOps both more necessary to roll out universally and more urgent to adopt quickly, while also making that rollout easier.</p>
<h2>Why Feature Flags are not a best practice, but a requirement, for AI</h2>
<p>When I talk to CIOs and engineering leaders, I ask them to challenge these statements. None do.</p>
<ul>
<li>The use of generative AI in software development is exploding.</li>
<li>Every enterprise is asking how it can use agents to move even faster.</li>
<li>Even at the same quality as humans, GenAI code and agents will lead to more security vulnerabilities and outages because the volume of code generated is increasing.</li>
<li>Someone is going to get fired when the inevitable happens.</li>
</ul>
<p>And it is already happening, with terms like “AI slop” competing with “vibe coding” to capture the zeitgeist of the moment.</p>
<p>For example, AWS is rumored to be asking teams to triple-check agentic output to avoid another outage in us-east.</p>
<p>In their announcement, Cloudflare correctly says that today “an AI agent writes code and a human reviews, merges, and deploys it. Tomorrow, the agent does all of that itself. The question becomes: how do you let an agent ship to production without removing every safety net?” Feature flags are the answer.</p>
<p>When agents are enabled in production, speed stops being your advantage and becomes your risk surface. AI doesn’t just help you ship faster, it increases the number of production decisions being made, the frequency at which they are made, and the blast radius when they are wrong. What used to be a handful of coordinated releases per week becomes thousands of autonomous changes flowing continuously into production. And critically, many of those changes are no longer fully understood by a human before they go live.</p>
<p><strong>In that world, the only viable safety mechanism is one that operates at runtime, is reversible instantly, and is enforced automatically. That is exactly what FeatureOps provides.</strong></p>
<p>If you are getting lost in the details, look at it this way. Dropping a 1-pound weight on your foot hurts, but you’ll live. At 99% the speed of light, that same weight carries the energy of roughly 4,000 Hiroshima-scale nuclear explosions. The weight is a code change. AI is the speed.</p>
<p>The good news is that feature flags can be implemented as part of the CI/CD workflows teams are already using. It is effectively a no-op from a process standpoint, with massive upside.</p>
<p>For instance, with Unleash today:</p>
<ul>
<li>Every pull request is automatically evaluated for risk, and a feature flag is created and implemented in code if the risk is elevated.</li>
<li>Every capability, frontend or backend, is gradually rolled out in a standardized way based on company policy.</li>
<li>Production metrics are monitored in real time so that rollouts can be automatically paused if errors spike or conversions drop.</li>
<li>Stale flags are automatically cleaned up from the codebase to avoid technical debt.</li>
</ul>
<p>All of this happens transparently, without human intervention. It is a process designed for agents.</p>
<p>All that is required is a skill in an AI coding assistant like <a href="https://docs.getunleash.io/integrate/claude-code">Claude</a>, <a href="https://docs.getunleash.io/integrate/kiro">Kiro</a>, or <a href="https://docs.getunleash.io/integrate/github-copilot">Copilot</a>, the Unleash MCP server, and a directive from the CIO that, just like all developers must use SSO for critical services, all non-trivial code must be wrapped in a flag. <a href="https://cdn.getunleash.io/uploads/2025/10/unleash-for-ai.pdf">Unleash does the rest.</a></p>
<img alt="runtime-control-ai-agents" class="alignnone size-large wp-image-13848" height="605" src="https://cdn.getunleash.io/uploads/2026/04/runtime-control-ai-agents-1024x605.png" width="1024" />
<h2>Should I use Cloudflare Flagship for feature flags?</h2>
<p>It depends on your architecture and requirements. Besides the obvious, which is that Flagship is currently in private beta, it has a unique architecture that is both a strength and a potential weakness, especially for mission-critical enterprise applications. Engineering is always about tradeoffs, so here is how Flagship and Unleash compare.</p>
<p>Flagship runs as a Cloudflare Worker, essentially a serverless function-as-a-service binding in your wrangler.jsonc, then evaluates flags directly from the isolate, handling each request. The feature flag control plane lives in a single Durable Object backed by SQLite, which serves as the global source of truth for flag state. Updates propagate to Workers KV within seconds and replicate across Cloudflare&#8217;s edge. Evaluation then runs fully inside the Worker isolate, which means zero outbound requests at decision time and sub-millisecond latency on warm paths.</p>
<p>Cloudflare&#8217;s architecture choice also comes with an architectural cost. Workers are ephemeral isolates, spun up on demand and discarded when idle. Every new isolate means a fresh fetch of flag config from Workers KV. Cloudflare states that KV reads are fast, typically under ten milliseconds on warm paths, but fast is not free. At scale, with thousands of cold starts per second, that latency compounds in ways that do not show up in a simple benchmark.</p>
<p>Compare that to how <a href="https://docs.getunleash.io/sdks">Unleash SDKs</a> evaluate flags. Flag configuration is fetched once at SDK startup and cached in-process in a long-lived application. Updates stream in from <a href="https://www.getunleash.io/unleash-enterprise-edge">Unleash Enterprise Edge</a>. Evaluation never leaves the process. A single Unleash instance already handles <a href="https://www.getunleash.io/blog/how-scaleable-is-unleash-7-5-trillion-flags-a-day">7.5 trillion flag evaluations per day</a>, because at that volume, the only path that works is zero network hops.</p>
<p>Cold starts are one half of the story. The other half is the source of truth. Flagship writes to a single Durable Object, which is globally unique by design. Reads are fast from any Worker, but writes travel back to that one Durable Object wherever it happens to be hosted. For a global enterprise operating from Mumbai, São Paulo, and Tokyo, that one-region write path adds operational coupling that may or may not matter for your use case, but you should know it is there.</p>
<p>A concrete way to think about the difference: an Unleash SDK evaluating a flag inside a JVM in a Mumbai data center hits zero network. A Flagship evaluation in a Mumbai Worker reads Workers KV, which may or may not be warm depending on isolate lifecycle and Cloudflare&#8217;s own propagation timing. Sub-millisecond evaluation is a great headline. Zero network hops is a better architecture.</p>
<p>Additionally, rolling out flags is one thing, doing it in an enterprise environment is another. We are big supporters of the OpenFeature project and thanks to their efforts, Unleash supports <a href="https://github.com/orgs/Unleash/discussions/3896#discussioncomment-15287054">four languages.</a> When we speak to our largest customers, however, they have requirements that go beyond the OpenFeature spec, and they are not willing to give up the security and control that comes from a full-featured FeatureOps platform. When it comes to avoiding lock in, they simply wrap the Unleash SDK is a small<a href="https://docs.getunleash.io/guides/manage-feature-flags-in-code#architecting-flag-evaluation"> library</a> that makes switching providers trivial if it ever comes to that, while getting capabilities like the following that OpenFeature does not standardize:</p>
<ul>
<li><a href="https://docs.getunleash.io/concepts/release-templates">Release templates</a> and multi-stage rollout blueprints</li>
<li><a href="https://docs.getunleash.io/concepts/impact-metrics">Impact metrics</a> tied directly to flags, with counters, gauges, and histograms flowing in from SDKs</li>
<li><a href="https://docs.getunleash.io/guides/getting-started-release-management#configure-automated-progressions">Automatic progression</a> of rollouts based on real production signals</li>
<li><a href="https://docs.getunleash.io/guides/getting-started-release-management#configure-a-safeguard">Automatic safeguards</a> that pause a rollout when error rates or latency cross thresholds</li>
<li><a href="https://docs.getunleash.io/concepts/technical-debt">Stale flag detection</a> and lifecycle management</li>
<li><a href="https://docs.getunleash.io/concepts/change-requests">Change requests</a> with four-eyes approval workflows</li>
<li><a href="https://docs.getunleash.io/concepts/events">Audit trails</a> rich enough to satisfy SOC2 and ISO27001 auditors</li>
</ul>
<p>To put this in context, consider <a href="https://www.getunleash.io/prudential-case-study">Prudential</a>, a 150-year-old financial services company with more than 40,000 employees across insurance, retirement, and investments.</p>
<p>Their Lead DevOps Engineer, Joseph Garcia, describes their environment as &#8220;everything from COBOL to LLMs.&#8221;</p>
<p>Their VP of DevOps, Peter Ho, describes their compliance reality:</p>
<blockquote><p><em>&#8220;Flipping a flag in production is considered a change that requires auditing, and as an enterprise, we have standardized on ServiceNow. But as a developer, I don&#8217;t like filling out tickets. With Unleash, developers never have to see ServiceNow. They simply interact with Unleash, and all changes and approvals are automatically synced in the background. Our auditors are happy, and our developers are more efficient.</em></p></blockquote>
<p>A binding in a wrangler.jsonc file does not produce that outcome. Sync with core systems, automated change control, and adminless onboarding for thousands of developers are not implemented in OpenFeature. But they are the parts that make feature flags usable in a regulated environment.  So you can think of OpenFeature like a floor, not a ceiling, for enterprise FeatureOps.</p>
<h2>FeatureOps: Accelerating AI-native development</h2>
<p>Feature flags are no longer a nice-to-have. They are becoming foundational infrastructure for how modern software runs.</p>
<p>In the Age of AI, that shift accelerates. When code is generated and shipped at machine speed, the problem is no longer how fast you deploy. It’s how you control what actually happens in production.</p>
<p>That’s the role of FeatureOps.</p>
<p>With this announcement, Cloudflare is pushing this movement forward. But for large enterprises, the challenge goes beyond fast evaluation. It’s about control, governance, and operating safely at a global scale.</p>
<p>The companies that win won’t be the ones that ship the most code. They’ll be the ones that can control it in production.</p>
