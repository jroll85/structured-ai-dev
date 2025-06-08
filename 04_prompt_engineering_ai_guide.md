04\_prompt\_engineering\_ai\_guide.md

````
# LiveSales.us AI-Assisted Development: Prompt Engineering Templates & Best Practices

Using well-crafted prompts is critical for getting useful and safe output from Cursor. We maintain standardized Prompt Engineering Templates to guide developers in interacting with the AI consistently. These templates encapsulate our project’s context, requirements, and the desired format of responses. By using them, we ensure that Cursor “understands” our stack and expectations before it writes any code. Below we provide the full AI Coder Prompt template and the corresponding AI Coder Response Summary template that we use in this project, followed by some real-world examples of their usage for specific tasks (Supabase RLS policies, Next.js API routes, Shadcn/UI components).

## AI Coder Prompt Template

This template is a structured prompt developers should use (via Cursor’s “include file” or copy-paste) when asking the AI to generate or modify code. It sets the stage for Cursor, telling it how to behave and what to consider:

```markdown
**Project**: LiveSales.us – Next.js 14+ PWA, TypeScript strict, Tailwind CSS, Shadcn/UI, Supabase (Postgres with RLS). Mobile-first design.
**Coding Guidelines**:
- Follow strict TypeScript typing (no `any`, no implicit `any`, no ts-ignore unless absolutely necessary).
- Use Shadcn/UI React components for all UI elements (e.g. use `<Button>` from `@/components/ui/button` instead of raw `<button>` for consistency).
- Use Tailwind CSS utility classes for styling, favoring mobile-first responsive classes (e.g. `class="p-4 sm:p-6"`).
- Adhere to modular architecture: business logic in `/services` or `/lib` modules, UI in components, API routes only orchestrate calls.
- Ensure functions are small and focused (single responsibility) and adhere to DRY principles.
- Include input validation and error handling in any API or logic code (throw or return errors with messages, handle exceptions).
- Security: Sanitize user inputs to prevent XSS/SQL injection. Use parameterized queries with Supabase client (no string concatenation in SQL).
- Performance: write efficient code (consider Big-O complexity, use indexes for DB queries if needed, avoid unnecessary re-renders in React, etc).
**Task**:
- [Clearly describe the specific coding task]. Be explicit about what output is expected (function, component, file structure, etc). For example: “Create a Next.js API route `/api/orders` that returns a list of orders for the authenticated user. Use Supabase JS client with RLS policies enforced. Include error handling for unauthenticated requests and query failures.”
**Additional Context** (if any):
- [Provide relevant code snippets, data models, or file names]. For example: “We have a Supabase table `orders(user_id, item, price)`. Only the user with matching `user_id` should see their orders (RLS enforced). Use `supabase.auth.getUser()` to get the user’s ID server-side.”
- “Refer to existing service function in `orderService.ts` for patterns on data fetching.”
- You can include code by referencing files or quoting snippets to give AI more context on how our code looks.
**Output Requirements**:
- Provide the solution in **Markdown** format with proper code fences (```tsx for React/TSX, ```ts for TS, ```sql for SQL, etc.).
- If multiple files or functions are involved, clearly separate them (with file names as needed).
- Include brief **comments or docstrings** in the code where helpful to explain complex logic.
- After providing code, output a short **summary** (in plain text) explaining what the code does and any assumptions.
````

When using this template, replace the bracketed sections with the actual task description and context. The template ensures the AI is aware of our tech stack and rules each time.

## **AI Coder Response Summary Template**

After the AI produces code, we often ask it to generate a concise summary of the changes or additions. This summary is useful for code review, pull request descriptions, or understanding the AI’s actions. The template for the AI’s response summary is:

Markdown

```
**AI Code Response Summary**:
- **Feature/Functionality Implemented**: [AI provides a brief description of what was implemented or changed, in one or two sentences].
- **Key Operations**:
  - [Bullet 1: Explanation of a critical part of the code, e.g., “Validates JWT and fetches orders for the user from Supabase”].
  - [Bullet 2: Another key operation, e.g., “Uses Shadcn `<Table>` component to display order list in UI, with responsive design”].
  - [Bullet 3: etc., focusing on important steps or algorithms].
- **Security Considerations**:
  - [Bullet: note any security measures in code, e.g., “All database queries use parameterized inputs to prevent SQL injection”].
  - [Bullet: e.g., “User input in the search field is sanitized before use in query”].
  - [If applicable: “RLS policy on Supabase ensures users only access their data”].
- **Errors & Edge Cases**:
  - [Bullet: how errors are handled, e.g., “Returns 401 if no auth token is provided”].
  - [Bullet: any edge case handling, e.g., “If no orders found, returns an empty list instead of error”].
- **Next Steps / Notes**:
  - [Bullet: suggestions or things to double-check, e.g., “Ensure the Supabase environment URL/key are configured in env for this to work”].
  - [Bullet: e.g., “Add unit tests for the order service filtering by user_id”].
```

The above summary format ensures that whenever the AI completes a task, we capture *what* it did in a structured way. It’s essentially what the AI assistant should explain to the developer: what it implemented, how it addressed the problem, and any important considerations. We require our developers to include such a summary (either AI-generated or manually written) in pull request descriptions for AI-written code.

## **Prompt Examples for Specific Scenarios**

Using the templates, here are examples of how to prompt Cursor for common LiveSales.us tasks:

* **Supabase RLS Policy Example:** *“Using the AI Coder Prompt, include:* Task: *‘Write a SQL policy for Supabase to ensure only the owner of an order (matching user\_id) can select or update it. The table is orders (columns: id, user\_id, item, price). Use Supabase’s auth.uid() function in the USING clause. Provide the SQL in a markdown code block with an explanation.’*” – This prompt will yield a SQL snippet in a `sql` block, for example:

   SQL

```
CREATE POLICY "Allow order owner access" 
ON orders 
FOR SELECT 
USING ( auth.uid() = user_id );
```

* 

   *Explanation:* The AI might add a short note that this policy allows only the user whose ID matches user\_id to read the row. We would verify correctness and possibly ask for an UPDATE policy similarly. The summary might note that RLS is enforced by the DB and mention using `auth.uid()`. Always double-check AI-generated SQL against Supabase docs.

* **Next.js API Route Example:** *Task:* “Create a Next.js Route Handler at `app/api/orders/route.ts` to retrieve the current user’s orders. It should: check the user’s session (using Supabase Auth helpers), call a service function (if available) or direct Supabase query to get orders, and return JSON. Use our response format `{ data, error }`. Include error handling for no auth or db errors.” — With context about our auth setup (Supabase Auth Helpers in Next.js), Cursor should produce something like:

   TypeScript

```
import { createRouteHandlerClient } from '@supabase/auth-helpers-nextjs';
import { cookies } from 'next/headers';
import { NextResponse } from 'next/server';
import type { Database } from '@/types/supabase'; // assume we have types

const supabase = createRouteHandlerClient<Database>({ cookies });

export async function GET() {
  const { data: { session } } = await supabase.auth.getSession();

  if (!session) {
    return NextResponse.json({ data: null, error: 'Not authenticated' }, { status: 401 });
  }

  const { data: orders, error } = await supabase.from('orders').select('*').eq('user_id', session.user.id);

  if (error) {
    console.error(error);
    return NextResponse.json({ data: null, error: 'Failed to fetch orders' }, { status: 500 });
  }

  return NextResponse.json({ data: orders, error: null });
}
```

* 

   This code uses Supabase auth helpers correctly and respects RLS (only selecting where user\_id \= user’s id) – the AI might know this pattern from our context or rules. We would ensure it used `createRouteHandlerClient` and not any deprecated approach. The Response Summary would note that it returns 401 if not logged in, uses Supabase query with user\_id filter, and returns data or error accordingly.

* **Shadcn/UI Component Example:** *Task:* “Build a React component `<OrderCard>` using Shadcn UI that displays an order’s details. It should accept an `Order` prop (with item name, price, etc.), and use `<Card>`, `<CardHeader>`, `<CardContent>` from Shadcn UI. Include Tailwind classes to layout the content nicely, mobile-first.” — Cursor might output:

   TypeScript

```
import { Card, CardHeader, CardTitle, CardContent, CardDescription } from "@/components/ui/card";

interface OrderCardProps {
  order: {
    item: string;
    price: number;
    date: string;
  };
}

export function OrderCard({ order }: OrderCardProps) {
  return (
    <Card className="w-full max-w-sm bg-white">
      <CardHeader>
        <CardTitle className="text-lg font-bold">{order.item}</CardTitle>
        <CardDescription>Purchased on {order.date}</CardDescription>
      </CardHeader>
      <CardContent>
        <p className="text-sm">Price: <span className="font-medium">${order.price}</span></p>
      </CardContent>
    </Card>
  );
}
```

* 

   This is a simple example where the AI uses our imported Shadcn UI components. We’d check that classes are mobile-friendly (maybe add `sm:max-w-md` or similar if needed) and the component is accessible (e.g., ensure any icons or images have alt text, etc.). The prompt template’s context about Shadcn ensured it didn’t try to use Material-UI or plain HTML for card styling. The summary might mention it displays order info in a card layout, uses Shadcn’s Card for consistent design, and is responsive.

In all these examples, notice how the prompts are specific about what we want and what technologies to use. This yields more accurate and project-aligned code. Always incorporate relevant details (like table names, component names, expected behaviors) into your prompt – it reduces guesswork by the AI.

## **Prompting Do’s and Don’ts:**

* **Do** use the AI Coder Prompt template for significant coding tasks. It ensures you don’t forget to tell Cursor about an important requirement (security, performance, etc.). The consistency helps the AI respond in a format we expect.  
* **Do** be specific and clear in prompts. State the problem, the desired solution format, and any constraints. Ambiguous prompts yield dubious answers. For example, “Make a page to show user info” is too vague. Instead: “Create a Next.js page at `/profile` that shows the current user’s profile information (name, email) using data from Supabase. Use Tailwind for styling, ensure only logged-in users can access (redirect otherwise).”  
* **Do** provide contextual code when needed. If you want the AI to follow a certain pattern, you can paste a short snippet or refer to an existing file. E.g., “Follow the style of `ProfileCard.tsx` for card layout.” Cursor’s “chat with codebase” can reference files by name if the index is built, which helps it follow context without full copy-paste.  
* **Do** break complex tasks into smaller prompts. If you need a multi-step solution (e.g., database migration \+ API \+ UI), do it one step at a time with the AI, so you can review each piece. You might first prompt for the database migration SQL, then separately ask for the API route to use that DB change, etc. This makes it easier to catch mistakes.  
* **Do** encourage the AI to explain tricky code in comments if it’s doing something non-obvious. (You can also ask it after generating code: “What does this regex do? Add a comment.”)  
* **Don’t** ask for too much at once. A prompt like “Build me the whole payments module” is likely to fail or produce sloppy output. Granular, focused prompts yield better results and allow iterative improvement.  
* **Don’t** assume the AI knows *our* project implicitly beyond what you’ve told it or what it has indexed. Cursor might have some repo indexing, but always specify project-specific details (like function names, custom types). If you just say “use the auth library”, it might not know we use Supabase – clarify it.  
* **Don’t** forget to mention non-functional requirements. If performance or security is important for that piece, include it in the prompt. For instance, “This function will run on every page load, so it must be optimized for speed (no heavy computations on main thread).” The AI then knows to avoid certain expensive operations.  
* **Don’t** use prompts that violate guidelines or policies. For example, don’t ask the AI to generate tests with real user data or to write code that scrapes a website illegally. Also, do not prompt it to produce disallowed content (Cursor likely won’t, but our policy is to avoid any unethical use).  
* **Don’t** accept the first answer blindly. Sometimes the first output may be slightly off – maybe it misunderstood something. It’s okay (encouraged, actually) to give follow-up prompts: “That’s not quite right – adjust it to do X,” or “Simplify this part,” etc. The conversation with Cursor is iterative. Use it to refine the code until it meets our standards.

