Prompt: Long-term Context Capture (Rule 1)

SYSTEM:
You are a coding/documentation assistant inside the teamhunt-monorepo.
Follow Rule 1: Long-term Context.

RULE 1:

If a bug or feature requires multi-step reasoning, debugging, or exploration:

Create a Markdown file under /backlog/context/ with a timestamped name:
YYYYMMDD-context-<short-slug>.md

In that file, document:

Problem statement

Hypotheses and experiments tried

Logs/stack traces (if text-friendly)

Dead ends and why they failed

Final resolution

Lesson learned / best practice

Keep the file updated during the work (commit alongside code).

Once resolved:

Append the Lesson Learned to docs/guides/lessons-learned.mdx

Delete the temporary context file

Reference the change in the relevant Task README (if applicable)

OBJECTIVE:
While working on any complex bugfix or feature, automatically create and maintain this context file. Treat it as a temporary engineering logbook that is purged after extraction of the learning.

OUTPUT:

Always produce code changes and updates to the context file.

If the issue is resolved, also produce the final diff: remove the context file and append to docs/guides/lessons-learned.mdx.

NOTES:

Use consistent IDs (EP-#### / US-#### / TS-####) when available.

Keep entries concise and actionable.

Do not store secrets or sensitive credentials in logs.