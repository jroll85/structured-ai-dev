08\_cursor\_tool\_specific\_usage\_guide.md

\# LiveSales.us AI-Assisted Development: Cursor Tool-Specific Usage Guide

Cursor is our chosen AI coding assistant, and to get the best (and safest) results, we configure and use it in specific ways. This section covers Cursor-specific features, plugins, and settings that we leverage, as well as best practices unique to Cursor’s workflow. The goal is to integrate Cursor seamlessly into our development process without compromising security or quality.

\#\# Security & Privacy Settings

As mentioned, we use Privacy Mode for Cursor on our team. This is enforced for all team members (the admin setting ensures any team user is in privacy mode by default). This guarantees that none of our code data is logged or used to train models on Cursor’s side. Do not turn this off. If Cursor ever asks to collect additional data, decline. Our trust in Cursor also stems from it being SOC 2 Type II certified, which aligns with our compliance needs. We still avoid sharing unnecessary data – Privacy Mode is good, but “air gap” mentality is better: only feed AI what it needs.

\#\# .cursorrules and Project Indexing

We maintain a file (or set of files) in \`.cursor/rules/\` that encodes many of the conventions and examples discussed in this guide. These rules include things like coding style, architectural patterns, prohibited functions, required library usage, etc., structured so Cursor can “auto-attach” them to relevant queries. For instance, we have a section in our rules that says “Always use our custom \`\<UIToast\>\` component for notifications, not \`alert()\`” – so if you ask Cursor to create a notification, it will already know to use \`\<UIToast\>\`. Familiarize yourself with these Cursor rules (they’re in YAML/Markdown format). When adding new frameworks or making architectural decisions, update the rules so Cursor stays current. The rules also contain code examples of correct usage (and sometimes incorrect usage). Cursor can pattern-match those to avoid repeating mistakes. For example, if we mark using raw SQL in Next.js as incorrect and using Supabase as correct, Cursor should follow that.

\#\# Cursor Features (Chat, Fix & Diff, Agents):

\* \*\*Chat with Codebase:\*\* This allows you to ask questions like “Where is the function to process payments?” or “Explain how the login flow works.”. Cursor will use the indexed code to answer. This is super useful for understanding unfamiliar parts of the code or onboarding new devs. It’s like a smarter grep. Use it instead of manually searching, but do verify its answers by looking at the actual code references it gives. If it cites a file, open that file to confirm.

\* \*\*Fix & Diff:\*\* When Cursor suggests fixes (for example, after a test fails or an error is encountered), it can apply them and show you a diff. Always review the diff carefully. This is a great feature for quick iterations (like resolving lint errors or small bugs). However, sometimes the fix might be wrong or incomplete. Only accept the diff if you understand and agree with the changes. If not, you can revert or refine the instruction. We prefer using Fix & Diff over letting the AI free-hand edit multiple files unseen; diffs ensure transparency.

\* \*\*YOLO / Agent Mode:\*\* Cursor has an “agent” mode that can, given a high-level goal, autonomously make multiple edits and even run code or tests in a loop until it reaches the goal (the builder.io blog called this “YOLO mode”). We allow this in development environments \*with caution\*. It’s impressive – e.g., “fix all TypeScript errors” agent could go through and attempt it. But you must supervise its actions. Do not leave an agent running on your codebase unattended. They could make broad changes; use version control to inspect what happened. We have configured Cursor with some guardrails in agent mode: it cannot run destructive commands or install new packages without confirmation (we set up a blocklist as mentioned, e.g., any \`rm \-rf\` or DB drop commands are blocked, and agent won’t commit to git on its own). Always run agents on a feature branch, and diff against main to ensure only intended changes are present.

\* \*\*Model Selection:\*\* Cursor might allow choosing different AI models (e.g., OpenAI GPT-4, Anthropic Claude, etc.). For code tasks, we generally use the model that gives the best accuracy (currently GPT-4-based). If you find a model’s output lacking, you can switch. But note that some models might handle our large codebase context differently. Stick to whatever our team plan provides and has been tested with our \`.cursorrules\`. We also avoid any non-sandboxed code execution by models that might connect out – all runs should be local or via our test environment.

\#\# Recommended Integrations:

\* We’ve integrated Aikido Security directly into Cursor. This runs in the background and surfaces any security issues as you code. Pay attention to its alerts (it might underline code or open a panel with warnings). It checks for things like hardcoded secrets, vulnerable patterns, etc. We also have ESLint and Prettier tied in – Cursor should obey our ESLint rules (we have a \`.eslintrc\` that the Cursor extension reads). If not, you’ll see lint errors; fix them (you can even ask Cursor to fix lint errors).

\* \*\*SonarQube Plugin:\*\* We installed the SonarQube IDE plugin in Cursor (Connected Mode) for additional code analysis. This means you might see Sonar suggestions or security hotspots flagged as you work. Treat these as you normally would – don’t ignore them just because AI wrote the code. Use them to improve the code. Sonar also has an AI CodeFix feature we can try for certain issues, but again, review any fixes it does.

\* \*\*Git Integration:\*\* Cursor can integrate with version control to some extent. However, \*do not let Cursor auto-commit or auto-push code\*. We disabled any auto-commit features for safety. Always review changes, run tests, then commit yourself with a meaningful message (possibly using the AI summary to help write it). The commit should be made by a human identity for accountability.

\* \*\*Testing Tools:\*\* If you’re using Cursor’s AI to help with tests, note that we also integrate with our CI (like GitHub Actions) to run those tests. Cursor might sometimes offer to run tests for you locally. That’s fine – but ensure our test database or environment is configured (we have environment files that Cursor can read context from, but not secrets). Always see test results with your own eyes.

\#\# Configurations to improve output quality:

\* We keep our codebase well-documented and example-rich so that Cursor’s index has good references. (E.g., a well-written README and clear directory structure help the AI understand the project context better). It’s a best practice to maintain a “guide” file for the project – we actually use this policy document and a separate architecture overview as Always-attach rules, so the AI is always aware of them.

\* We update Cursor’s index when significant changes happen (the tool usually does this automatically on save, but sometimes a reindex or project reload ensures the AI is up to date).

\* We might configure Cursor’s response length or split if responses are cut off – by default, ask it to continue or be more concise if it’s too verbose.

\* If you find Cursor making the same kind of mistake repeatedly, we can add a rule to the \`.cursorrules\` to correct that. For example, if it keeps suggesting a deprecated method, add it under “Avoid these patterns” with correction.

\* Use the feedback mechanism: If Cursor produces a poor suggestion, you can rate it or provide feedback (depending on the interface). This might indirectly improve future results. But more immediately, adjust your prompt or the rules.

\#\# Effective Use of Cursor – Do’s and Don’ts:

\* \*\*Do\*\* treat Cursor as a powerful IDE extension – use its features (codebase Q\&A, diff review, etc.) to boost productivity, but stay in charge. You click the buttons; it doesn’t run off on its own.

\* \*\*Do\*\* utilize security-enhancing integrations like Aikido and Sonar within Cursor. They help enforce our security policy in real-time. Address issues they point out promptly – it’s easier to fix during coding than after a penetration test\!

\* \*\*Do\*\* keep the Cursor rules and context files updated. If we add a new library (say we switch from Tailwind to another CSS framework), update the rules so AI knows. This prevents it from suggesting old patterns. Make rule updates part of your feature development checklist when relevant.

\* \*\*Do\*\* regularly update the Cursor application and plugins. Newer versions often improve model quality or add safety features. However, do read the changelogs – ensure no new features default to something insecure (so far, Cursor has been good at opt-in for anything that might send data).

\* \*\*Do\*\* use Cursor to answer questions about the codebase – it’s like having Stack Overflow for your own code. This can save time digging through files. Just remember it might not be 100% correct; use it as a guide then verify.

\* \*\*Don’t\*\* share your Cursor API key or login with anyone outside the team. That key is tied to our account and data – keep it secure. Also, don’t use Cursor on untrusted networks without VPN, as you would any dev tool (it’s basically an IDE connecting to cloud).

\* \*\*Don’t\*\* allow Cursor to perform destructive actions without confirmation. Our config should block most, but always double-check if an AI action could delete data or files. For example, if an agent suggests wiping a database table in dev as a step, think twice. Only proceed if you understand and it’s safe.

\* \*\*Don’t\*\* heavily use Cursor on code outside the repository scope with company data. If you have to analyze a log file or user data snippet via AI, consider using local tools or sanitize it – better not to feed that into Cursor which is mainly for code. (This touches on privacy – ensure any data used in Cursor is code or synthetic; don’t paste real user emails or anything.)

\* \*\*Don’t\*\* compare Cursor output to Copilot or other tools while on our systems. We intentionally focus on Cursor, and mixing multiple AI assistants can be confusing and pose additional privacy concerns. If you want to try another tool for a solution, do it outside of company resources and then integrate carefully (subject to same review standards).

\* \*\*Don’t\*\* circumvent the team’s Cursor setup. For instance, don’t disable Privacy Mode or install unapproved Cursor plugins. If you think a plugin is useful, propose it and we’ll evaluate (some may pose security risks).

\* \*\*Don’t\*\* forget that Cursor is just one tool – if it doesn’t handle something well (some niche framework or a truly novel problem), you can always fall back to manual coding or use the AI in smaller pieces. Sometimes you’ll need to think and code old-school if the AI is stuck or hallucinating. That’s fine – we prioritize correctness over using AI for everything.

By following these guidelines, we aim to harness Cursor’s benefits (speed, assistance, learning) while mitigating its risks. This ensures that LiveSales.us development is fast, but also safe, compliant, and maintainable. With AI as a teammate under our careful supervision, we can accelerate our work without cutting corners.

