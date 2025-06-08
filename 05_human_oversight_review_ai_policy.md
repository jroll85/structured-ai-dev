05\_human\_oversight\_review\_ai\_policy.md

\# LiveSales.us AI-Assisted Development: Human Oversight & Review Policy

No matter how good the AI assistant is, human oversight is mandatory at LiveSales.us for every code change. Think of Cursor as an apprentice: it can do a lot of the heavy lifting, but a senior developer (you or a colleague) must review and approve its work. This aligns with SOC 2 change management controls and our internal QA process – we never deploy code that hasn’t been human-verified. Developers-in-the-loop are our safety net to catch AI mistakes, which can range from subtle logic bugs to glaring security holes.

\#\# Code Review Process for AI Contributions

All AI-generated or AI-modified code should go through the normal pull request process, with an extra eye for the kinds of errors AI might introduce. When you open a PR that includes AI-written code, mention it in the description (and include the AI Response Summary as noted). Reviewers will then scrutinize it with appropriate skepticism.

During review, consider: \*Does the code actually solve the problem correctly? Are all edge cases handled? Is the code style consistent? Are there any suspicious or nonsensical lines?\* For example, if an AI-generated algorithm looks unusually concise, make sure it’s not using a mathematically incorrect shortcut. Or if there’s a comment that seems AI-generated and not quite matching the code, double-check the code logic – it might have changed and the comment not updated.

Never “rubber stamp” AI code just because tests pass. Tests might not cover everything, and AI can sometimes even write incorrect tests (that always pass). A human reviewer can notice things like missing audit logging (e.g., a security-relevant action that should be logged but the AI didn’t include), or adherence to business rules that the AI wasn’t aware of.

We have a checklist (below) for reviewing AI-generated code. As a rule, if an AI suggestion is poorly understood by the developer, that’s a red flag – you should either not use that suggestion or take the time to understand and perhaps simplify it. Under no circumstances should we merge code that no one on the team fully understands. Understanding vs. blind acceptance is key: the AI might produce code that “works” but if we don’t grasp it, maintenance will be impossible and errors could lurk.

\#\# Encouraging a Learning Mindset

Use the AI as a learning tool. If it writes something you don’t understand, ask it to explain (“Explain why you chose this approach”). This serves two purposes: (1) You verify it’s doing something sensible, and (2) you learn and can confirm the explanation matches the code. If the explanation is unsatisfactory or highlights a risk (“I used a bubble sort as it was easiest.”), you might decide to refactor to a better approach. This interplay ensures the developer remains in control and aware of the code’s functioning, which is essential for long-term maintainability.

\#\# AI Code Review Checklist:

When reviewing code that was AI-assisted (either as the author or a peer reviewer), go through the following points:

\* \*\*Functionality & Requirements:\*\* Does the code meet the requirements of the task? Cross-check against the acceptance criteria or user story. (If AI was told to implement X feature, is it fully implemented and correct?)

\* \*\*Security:\*\* Double-check for any security issues. Input validation in place? No secrets or sensitive info present? No obvious injection or XSS vectors left open? (Refer to Section 1’s practices in "Cursor rules.md" (Source 10)).

\* \*\*Compliance:\*\* Does the code adhere to GDPR/CCPA in terms of data handling? (e.g., if exporting personal data, is it secure?). If it involves user data or financial data, are we following PCI DSS mandates (encryption, no logging of card details)? Ensure any AI-generated endorsements or content in the code (if any) would follow FTC guidelines – though code-level, this might mean checking that any user-generated content features have proper disclosures or cannot be misused for fake reviews.

\* \*\*Logic & Edge Cases:\*\* Test mentally (and with actual tests) different scenarios. Will it break with null values or unexpected input? Did the AI account for failures (network issues, etc.)? Are all branches covered?

\* \*\*Code Style & Consistency:\*\* Is the code formatted and structured according to our conventions? (Naming, modularization, use of our frameworks correctly, etc.) No odd patterns that don’t match the rest of the codebase.

\* \*\*Performance:\*\* Any potential performance red flags? (Inefficient loops, unneeded database calls inside loops, lack of caching where it would be expected, etc.) If so, address them now.

\* \*\*Error Handling & Logging:\*\* Are errors handled gracefully and logged appropriately? AI might miss nuanced error handling; ensure, for instance, that it doesn’t swallow exceptions without logging or that it returns proper HTTP status codes, etc.

\* \*\*Dependency Changes:\*\* If the AI introduced a new dependency or a major version upgrade, review that dependency’s necessity and impact. Run \`npm audit\`. Ensure licenses are compatible (as discussed in IP section of "Cursor rules.md" (Source 10)).

\* \*\*Tests:\*\* Are there tests, and do they pass? If AI generated tests, are they actually asserting correct behavior or just trivial? Add or modify tests if needed to cover what AI might have missed.

\* \*\*Documentation & Comments:\*\* Are there comments explaining non-trivial logic? If AI wrote docstrings or comments, verify they’re accurate. Incorrect comments can be misleading. Update any docs (like README or architecture docs) if this change affects them.

Reviewers should be empowered to push back on AI-generated code more than human code if something feels off, because the AI doesn’t get offended – it’s purely about code quality. Often, a quick “AI, fix this bug” or “simplify this code” in Cursor can address review findings, but again, run through the checklist on the new changes.

\#\# Oversight Do’s and Don’ts:

\* \*\*Do\*\* have at least one human review for any code that goes into the repository, regardless of AI involvement. Our policy: \*AI can assist, but it cannot be the sole approver of any code change.\*

\* \*\*Do\*\* insist that developers understand the code they commit. In stand-ups or code reviews, anyone who used AI should be able to explain the resulting code. If you can’t explain it, you need to dig deeper or rewrite it in a way you do understand.

\* \*\*Do\*\* use pair programming or mob review for critical code sections. Two sets of eyes (or more) on AI code can catch issues one person might miss. This is especially useful for security-sensitive features – treat AI output as if a junior dev wrote it and do a thorough security review.

\* \*\*Do\*\* maintain an audit trail. Our version control tracks changes, and we note in commit messages when AI was used heavily. This is good for SOC 2 (change management evidence) and helps in case we need to audit a potential IP issue later.

\* \*\*Do\*\* continue to enforce QA testing and staging for all code. AI code goes through the same pipeline: dev testing, code review, automated tests, staging testing, etc. Often AI errors only surface in integration (maybe a subtle type mismatch with real data), so testing is crucial.

\* \*\*Don’t\*\* ever deploy code that hasn’t been reviewed due to a false sense of security. Even if AI says “All tests passed,” trust but verify – run the tests yourself, consider writing new tests for boundary conditions AI didn’t consider.

\* \*\*Don’t\*\* allow “fast-tracking” of AI code because it was quick to produce. Speed is not a reason to skip process. If anything, be more deliberate – AI can inadvertently introduce subtle bugs that only careful review would catch.

\* \*\*Don’t\*\* get lured into a false sense of expertise. AI may produce authoritative-sounding explanations or code, but if something doesn’t make sense to you, do not assume “it must know better.” Challenge it or seek a second opinion from a teammate or reliable source. It’s fine to be skeptical – AI can and does make mistakes.

\* \*\*Don’t\*\* blame the AI in commit history or comments (“Cursor wrote this, not my fault”). Ultimately, the team owns the code. If something goes wrong, saying “the AI did it” is not acceptable. Always take responsibility: if the AI suggestion was wrong, we are responsible for catching it. Thus, treat AI as part of the toolchain, not an autonomous coder.

By keeping humans firmly in control – reviewing, testing, and understanding – we ensure that AI remains a boon to productivity without becoming a risk.