# Test Execution Assistant — AI agent skill for QA

A reusable **AI-agent skill (`SKILL.md`)** that runs end-to-end QA test execution from a single Jira ticket link, with the tester kept in control.

## What it does
1. **Read** – acceptance criteria, wording in every language, sprint, linked test cases
2. **Test** – executes the cases on a backoffice test environment, plus light regression
3. **Capture** – full-HD (1920×1080) evidence per test case, rendered in-browser with html2canvas
4. **Execute** – updates QAlity Plus test executions with step statuses and one-line results
5. **Report** – drafts bug sub-tasks in the team's format and links them to the failed test cases

## Human in the loop
- The tester logs in (incl. 2FA); the agent never handles credentials
- The agent asks before saving any data on the environment
- It never touches other testers' executions
- The tester reviews results and adds bugs the agent misses

## How to use
1. Add `SKILL.md` as a skill in your AI agent (e.g. Claude skills).
2. Replace the placeholders: `<your-site>`, `<PROJ>`, `<your-test-backoffice>`, `<Cycle prefix>`, `<your QA alias>`.
3. Give the agent a Jira ticket link: *"Execute the test cases for <PROJ>-1234"*.

## Known limits
- Some web apps block file pickers, paste and drag-drop for agents, so evidence files are handed back to the tester to attach.
- Some buttons only respond to a real user click.

## Tech
AI agent (Claude) · Jira REST API · QAlity Plus · JavaScript · html2canvas · Python

## Author
Jovita Sutanto — QA Engineer
