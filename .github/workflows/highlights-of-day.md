---
name: Highlights of Day
on:
  schedule:
    - cron: '0 */6 * * *'  # Every six hours
  workflow_dispatch:
permissions:
  contents: read
  copilot-requests: write
engine: copilot
tools:
  github:
    mode: gh-proxy
    toolsets: [repos]
  edit: {}
  web-fetch: {}
network:
  allowed:
    - gh-aw
safe-outputs:
  create-pull-request:
    allowed-files:
      - index.html
    max: 1
---

# Highlights of Day

You are an AI assistant tasked with fetching FAQ content and adding it to the Daily Updates section of the website.

## Your Task

Fetch a GitHub Agentic Workflows FAQ and add it to the Daily Updates dialog for today's UTC date in `index.html`. If no FAQ can be added (FAQ already in dialog, no unused FAQs, or date doesn't exist), use safe-output `noop`.

## Instructions

1. Get the current UTC date in the format "Nth of Month" (e.g., "1st of August", "23rd of December"). This is the date you'll add the FAQ to.

2. Fetch the GitHub Agentic Workflows FAQ page:
   ```
   https://github.github.com/gh-aw/reference/faq/
   ```
   Use `tools.web-fetch` to retrieve the FAQ content.

3. Read the current `index.html` file.

4. Identify which FAQs are already represented in `index.html` by scanning all dialog content for FAQ-like question/answer pairs.

5. Select ONE FAQ from the fetched page that is NOT already in `index.html`.

6. Check if today's date already exists in the Daily Updates:
   - Look for a dialog with `id="[month]-[day]-dialog"` matching today's UTC date
   - If no matching dialog exists, use safe-output `noop` and make no changes
   - If a matching dialog exists, proceed to update it

7. Check if today's dialog already contains FAQ content:
   - If the dialog already has FAQ question/answer content (look for `<h2>` and `<p>` with Q&A style text), use safe-output `noop`
   - If the dialog only has a placeholder or no content, proceed to add the FAQ

8. Add the selected FAQ to today's dialog:
   - Update the `<h2 id="[month]-[day]-question">` with the FAQ question
   - Update the `<p id="[month]-[day]-answer">` with the FAQ answer (keep it concise and accurate)
   - Keep the existing header format (e.g., "Daily Update / 1st of August")
   - Do NOT modify the dialog structure, styling, or accessibility attributes

   Example structure (do not change):
   ```html
   <dialog
     class="daily-update-dialog"
     id="august-1-dialog"
     aria-labelledby="august-1-question"
     aria-describedby="august-1-answer"
   >
     <article class="daily-update-dialog-content">
       <header class="daily-update-dialog-header">
         <p>Daily Update / 1st of August</p>
         <form method="dialog">
           <button class="dialog-close" type="submit" aria-label="Close dialog" title="Close dialog">
             <span aria-hidden="true">&#10005;</span>
           </button>
         </form>
       </header>
       <h2 id="august-1-question">[FAQ Question]</h2>
       <p id="august-1-answer">
         [FAQ Answer - concise and accurate]
       </p>
     </article>
   </dialog>
   ```

9. Preserve all existing daily updates and dialogs exactly as they are. Do not modify `styles.css` or any other files.

10. Ensure all HTML is valid and properly formatted. Use two-space indentation to match the existing file.

## Safe Output Conditions

Use safe-output `noop` (no changes) if:
- Today's date does not exist in Daily Updates navigation
- Today's dialog already contains FAQ content
- No unused FAQs remain to add (all FAQs are already in `index.html`)
- The FAQ page cannot be fetched or parsed

Otherwise, create a pull request with the updated `index.html`.

## Ordinal Number Rules

For ordinal numbers in the date format:
- 1st, 2nd, 3rd, 21st, 22nd, 23rd, 31st
- All others use "th": 4th, 5th, 6th, 7th, 8th, 9th, 10th, 11th, 12th, 13th, 14th, 15th, 16th, 17th, 18th, 19th, 20th, 24th, 25th, 26th, 27th, 28th, 29th, 30th
