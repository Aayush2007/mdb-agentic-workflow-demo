---
name: Daily App Update
on:
  schedule:
    - cron: '0 0 * * *'  # Every day at midnight UTC
  workflow_dispatch:
permissions:
  contents: read
  copilot-requests: write
engine: copilot
tools:
  github:
    mode: gh-proxy
    toolsets: [repos]
  edit: true
safe-outputs:
  create-pull-request:
    allowed-files:
      - index.html
    max: 1
---

# Daily App Update

You are an AI assistant tasked with updating the Daily Updates section of the website.

## Your Task

Update `index.html` to add today's UTC date to the Daily Updates navigation and create a matching accessible dialog that confirms the daily update ran.

## Instructions

1. Get the current UTC date in the format "Nth of Month" (e.g., "1st of August", "23rd of December", "31st of January"). Use the workflow run's UTC date.

2. Read the current `index.html` file.

3. Check if this date already exists in the Daily Updates list (look for existing navigation buttons and IDs with the date). If it already exists, do not make any changes and use safe-output `noop`.

4. If the date is new, add it to the Daily Updates navigation:
   - Add a new `<li>` item after the existing daily update button in `.daily-updates-list`
   - Follow the exact same HTML structure as the existing button:
     ```html
     <li>
       <button
         class="daily-update-trigger"
         type="button"
         aria-haspopup="dialog"
         aria-controls="[month]-[day]-dialog"
         data-dialog-trigger
       >
         <span>[Nth of Month]</span>
         <span aria-hidden="true">&#8594;</span>
       </button>
     </li>
     ```
   - Use the ID format: `[month-abbrev-lowercase]-[day-number]-dialog` (e.g., `august-1-dialog`, `december-23-dialog`)

5. Add a matching accessible dialog after the first dialog:
   - Follow the exact structure of the existing "1st of August" dialog
   - Update the `id` to match the button's `aria-controls`
   - Set the dialog title (`<h2 id="[month]-[day]-question">`) to a confirmation message
   - Set the dialog description (`<p id="[month]-[day]-answer">`) to confirm the daily update ran
   - Example for August 23rd:
     ```html
     <dialog
       class="daily-update-dialog"
       id="august-23-dialog"
       aria-labelledby="august-23-question"
       aria-describedby="august-23-answer"
     >
       <article class="daily-update-dialog-content">
         <header class="daily-update-dialog-header">
           <p>Daily Update / August 23rd</p>
           <form method="dialog">
             <button class="dialog-close" type="submit" aria-label="Close dialog" title="Close dialog">
               <span aria-hidden="true">&#10005;</span>
             </button>
           </form>
         </header>
         <h2 id="august-23-question">Daily update for August 23rd</h2>
         <p id="august-23-answer">
           The daily update workflow ran successfully on August 23rd. Check back tomorrow for the next update!
         </p>
       </article>
     </dialog>
     ```

6. Preserve all existing daily updates and dialogs exactly as they are. Do not modify `styles.css`.

7. Ensure all HTML is valid and properly formatted. Use two-space indentation to match the existing file.

## Safe Output

The workflow will create a pull request with your `index.html` changes using `safe-outputs.create-pull-request`. Simply provide the updated file, and the system will create and propose the pull request.

## Ordinal Number Rules

For ordinal numbers in the date format:
- 1st, 2nd, 3rd, 21st, 22nd, 23rd, 31st (use these patterns)
- All others use "th": 4th, 5th, 6th, 7th, 8th, 9th, 10th, 11th, 12th, 13th, 14th, 15th, 16th, 17th, 18th, 19th, 20th, 24th, 25th, 26th, 27th, 28th, 29th, 30th
