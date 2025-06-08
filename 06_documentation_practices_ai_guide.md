06\_documentation\_practices\_ai\_guide.md

\# LiveSales.us AI-Assisted Development: Documentation Practices

Good documentation is a cornerstone of maintainable software. AI assistance can help generate documentation and comments, but it’s up to us to ensure they are accurate and useful. Our policy is to use AI to enhance documentation, not to replace our judgment on what needs documenting.

\#\# JSDoc and Code Comments

We require JSDoc-style comments for exported functions, complex logic, and all public API endpoints. Developers can ask Cursor to generate JSDoc for a function they’ve written or even one the AI wrote. For example: \*“Add JSDoc comments to the above function, describing its parameters, return value, and side effects.”\* Cursor can produce a decent first draft of a comment. However, you must review and possibly edit AI-generated comments for correctness. Ensure the description matches what the code actually does, and that it doesn’t include irrelevant or incorrect info. Sometimes AI might insert a generic comment that isn’t quite right if our code diverged – double-check every statement in the comment. It’s often faster to let AI draft it and then you tweak phrasing or details. The result should be clear, concise documentation for future readers of the code.

For complex logic blocks, even inside a function, consider adding inline comments. You can prompt AI: \*“Explain this block of code in plain English comments.”\* It might output something like \`// This loop merges the two sorted lists into one.\` That’s helpful. But again, verify the explanation is right. If the AI misinterpreted its own code, that’s a sign to reevaluate the code itself.

\#\# High-Level Documentation

For new modules or major features, developers should update our README or other docs. AI can help by summarizing code or generating usage examples. For instance, after writing a new component, you might ask Cursor, \*“Provide a brief usage example for the new OrderCard component.”\* You can then include that in our Storybook or docs. We maintain a docs directory for any developer-facing documentation (like how to run migrations, how to use certain utilities). Feel free to use AI to draft these, but treat it like a co-writer – ensure the tone and accuracy match our existing docs.

\#\# API and Endpoints documentation

If Cursor generates an API route, have it also produce an OpenAPI spec snippet or at least a comment that describes the request/response format. This can later be compiled into API docs. We want consistent documentation, so use the templates we have (if any) for documenting endpoints.

\#\# Comment Style and Maintenance

Our commenting standard is that comments should explain the “why” behind code if it’s not obvious, not the “what” (the code should show the what). Don’t let AI produce overly verbose comments that just restate code (“\`i++ // increment i by 1\`”). Instead, focus on intent: \*why\* is this algorithm implemented this way? If AI’s comment doesn’t capture that, adjust it.

Also, remove any stale or incorrect comments. AI might leave a comment from an earlier iteration that no longer applies – always synchronize comments with code changes. If you refactor code (manually or via AI), update the comments too.

\#\# Using AI for External Documentation

If we need to create user-facing documentation or technical blog content based on our code, AI can help formulate it, but ensure it doesn’t accidentally include any sensitive info or internal jargon not meant for users. And of course, all such content should be reviewed for accuracy and tone.

\#\# Documentation Do’s and Don’ts:

\* \*\*Do\*\* use AI to generate JSDoc comments for functions, especially if you find it tedious. It can fill in parameter and return type info quickly. But do verify every part of the comment.

\* \*\*Do\*\* maintain our commenting style. If we start comments with a certain format (e.g., imperative mood, or including the function name), ensure AI follows that. You can instruct it: “Write JSDoc in our style (brief description, then @param, @returns).”

\* \*\*Do\*\* ensure accurate descriptions of business logic. If AI describes a function as “fetches all orders” but in reality it fetches only user’s orders, change the comment to reflect reality.

\* \*\*Do\*\* document any AI-introduced algorithm that might not be obvious to other developers. For instance, if AI came up with a clever bit of math or regex, add a comment explaining how it works (you can use AI’s explanation if correct).

\* \*\*Do\*\* include usage examples in documentation. AI can help generate examples of how to call a function or use a component, which can be very useful for others.

\* \*\*Don’t\*\* rely on AI documentation without reading it. An incorrect doc comment can be worse than none, by misleading future maintainers. Never assume it got it right – always review.

\* \*\*Don’t\*\* let documentation lag behind code. It’s easy to forget updating a comment after tweaking code, especially if AI helped make the tweak. Make it a habit: whenever code changes, scan for any comments that need updating too. If AI helped generate the code, also have it update the doc (“Update the above JSDoc to reflect the changes”).

\* \*\*Don’t\*\* use AI to generate \*sensitive documentation\* (like internal architecture diagrams or threat models) without careful oversight. Those often require human insight and may contain sensitive info that shouldn’t leave our system. If you do use AI help for design docs, strip out sensitive details and add them back manually if needed.

\* \*\*Don’t\*\* allow AI to inject any biased or non-inclusive language in documentation or comments. We adhere to professional and inclusive documentation standards. While unlikely, if AI uses phrasing we wouldn’t (like gendered pronouns unnecessarily or colloquial language), adjust it.

\* \*\*Don’t\*\* forget that documentation includes commit messages and PR summaries. Use the AI Response Summary as a starting point for PR descriptions, but ensure it’s edited to truly reflect the changes once final. A well-documented PR (with what, why, and how) is invaluable for future reference.

In short, AI is a documentation assistant, not the documentation authority. We leverage it to reduce grunt work, but the onus is on us to maintain high-quality, accurate docs and comments across the codebase.

