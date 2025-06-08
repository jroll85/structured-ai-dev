07\_performance\_optimization\_ai\_rules.md

\# LiveSales.us AI-Assisted Development: Performance Optimization

Performance is crucial for the LiveSales.us PWA, especially given it’s mobile-first and real-time (likely dealing with live sales updates, etc.). AI-generated code must be scrutinized for efficiency. While Cursor might not intentionally write slow code, it might not automatically optimize unless instructed. Always keep an eye on complexity and resource usage of AI suggestions.

\#\# Directing AI for Performance

When asking Cursor to implement something, mention any performance constraints in the prompt. For example: \*“Generate a function to calculate recommendations. The dataset could be large (thousands of records), so it needs to be optimized (O(n) or O(n log n) solution, no O(n^2) loops).”\* This nudges the AI towards better algorithms. If you know a specific approach is needed (e.g., use a Map for lookup instead of nested loops), include that detail. Similarly, if an operation will run on the client side, emphasize to minimize payload size and computation (maybe do more on server). Our project often involves real-time data (using Supabase’s subscriptions or similar). Ensure that AI code handling real-time events is efficient (debounce rapid events, don’t re-render unnecessarily, etc.).

\#\# Identifying Bottlenecks

After AI generates code, think about its performance. Does that SQL query have filters that use indexes? If not sure, ask AI or a teammate – e.g., “Will this query benefit from an index on user\\\_id?” (The Supabase RLS prompt in their docs even recommends adding an index for common policy filters). If the AI wrote a recursive function, consider the maximum depth – is tail recursion or an iterative approach better? If it set up an interval timer in JavaScript, ensure it clears it properly and isn’t doing work too often.

\#\# Testing and Profiling

Use our profiling tools (browser devtools, Node profiling, etc.) on AI-written code if you suspect a slowdown. If an AI-generated component re-renders too frequently, you might need to add \`React.memo\` or adjust dependencies – something AI might not do automatically. Prompt the AI: \*“Optimize the above component to prevent unnecessary re-renders.”\* It might introduce \`React.memo\` or move state around, which you then test. For database heavy operations, ensure the AI uses lazy loading or pagination. If it, say, generated code to fetch all records and then filter in memory, that’s a no-go for large datasets. Better to adjust it to use a DB query for filtering. Always prefer server-side filtering via Supabase queries over client-side, to minimize data transfer.

\#\# Real-time and API throughput

LiveSales likely has real-time features. Code handling high-frequency events (like price updates or chat messages) should be efficient. AI might not automatically throttle or debounce; be mindful to add those if needed. Also consider using web workers or background threads for heavy computations an AI snippet might do on the main thread.

\#\# Memory usage

If AI creates large arrays or holds data in memory, check that it’s feasible. For example, if it reads a large JSON fully into memory, maybe we stream it instead. Direct the AI accordingly if needed (e.g., “stream the response instead of loading it all at once”).

In summary, treat AI code performance as you would any code: think about big-O complexity, network and disk access, and user-perceived speed. The difference is you may need to explicitly tell the AI to consider these, as it might not by default.

\#\# Performance Do’s and Don’ts:

\* \*\*Do\*\* specify performance expectations in your AI prompts whenever relevant (“optimize for mobile”, “handle up to 10k records efficiently”, “use caching for repeated calls”). This helps get performance-conscious code from the start.

\* \*\*Do\*\* analyze the complexity of AI-generated algorithms. If the AI gave you a double nested loop that could become problematic with 1000+ items, refactor it (manually or prompt AI: “Can you optimize this using a set for O(n) performance?”).

\* \*\*Do\*\* use efficient APIs and patterns. For instance, if Cursor isn’t aware of our use of React context or selectors for state, a suggestion might re-fetch data more than necessary. Optimize by using global state or context properly (you may need to integrate that into the code after AI output).

\* \*\*Do\*\* implement caching or memoization where appropriate. If the AI writes a function that performs an expensive calculation, consider wrapping it with memoization (and you can ask AI to do that: “Add caching to this function results to avoid recomputation”).

\* \*\*Do\*\* test the code under realistic conditions. If AI wrote an infinite scroll loader, test it with thousands of items. If it wrote an image processing loop, test it on large images. Profile memory and CPU to catch inefficiencies early.

\* \*\*Do\*\* leverage browser devtools Lighthouse or performance audits for UI changes. If AI generates a big chunk of client-side code, run a Lighthouse audit to ensure it’s not hurting our PWA metrics (like blocking main thread, large bundle, etc.). If it is, have AI split code (code-splitting or using dynamic import if needed).

\* \*\*Don’t\*\* accept naive implementations for critical loops or queries. AI might not automatically use the most optimal method (e.g., using \`.map().filter()\` in a chain where one loop would do). If performance is a concern, it’s okay to micro-optimize AI code when necessary.

\* \*\*Don’t\*\* ignore potential scalability issues. Today’s test data might be small, but if this feature will handle large scale, make sure the AI code will hold up. For example, an AI might sort a list on the client that should be sorted on the server for big lists – adjust it.

\* \*\*Don’t\*\* allow excessive network calls or chatty behavior. If AI code calls an API in a loop or doesn’t batch requests when it could, change that. E.g., if generating code that fetches each item’s detail in a loop, better to fetch all in one query. AI doesn’t feel the pain of network latency, but our users would.

\* \*\*Don’t\*\* let the AI disable performance-related ESLint rules (like using \`.forEach\` when a simpler loop would be clearer, or vice versa). Also, be cautious if AI suggests increasing timeouts or something as a fix – find the root cause of slowness instead of brute-forcing delays.

\* \*\*Don’t\*\* forget about memory leaks. If AI is adding event listeners or intervals, ensure they clean up (perhaps in \`useEffect\` return in React, etc.). A memory leak is a performance issue over time. Always review such patterns for proper cleanup.

In short, treat AI like a junior developer who wrote code that passes initial tests but hasn’t thought about scale – it’s on us to apply that production-ready lens.

