---
name: test-execution-assistant
description: "Execute assigned test cases for a Jira ticket on a backoffice test environment: test, capture full-HD evidence, fill QAlity Plus executions with short comments, and raise Bug Sub-Tasks. Works for any sprint."
---

# Test execution assistant (Jira ticket → test env → QAlity Plus → bugs)

## Inputs (ask only for what is missing)
| Input | Default / how to get it |
|---|---|
| Jira ticket URL | Required, e.g. `https://<your-site>.atlassian.net/browse/<PROJ>-1234` |
| Test environment | `https://<your-test-backoffice>` (default env). Use another if the user names one |
| Frontend/member site URL | Ask only when a test case checks the frontend (e.g. cache/refresh cases) |
| Test cycle | Read the ticket's **Sprint** field → cycle `<Cycle prefix> - <Month YYYY>` |
| Bug parent | Given by the user later (a Bug issue) |
| Bug format example | A past bug ticket the user points to; copy its type, title style and description layout |

Tester = `<your QA alias>`.

## Ground rules
- If the Jira connector is blocked, use the browser and call the Jira REST API with `fetch` from a logged-in Jira tab (same-origin, user's session).
- The user logs into the test environment themselves (incl. 2FA). Never enter credentials.
- Ask before any **save** on the environment. Prefer testing without saving; reload to discard unsaved edits. Restore language/settings afterwards.
- Never touch rows or executions owned by other testers.
- If the user is clicking in the same browser, stop and ask, so you don't overwrite each other's changes.
- If the feature is not deployed yet, stop and report; don't fail test cases for it.

## 1. Read the ticket
Acceptance criteria, wording in every supported language, Sprint, and linked test cases (`is tested by` → `[Module] Verify ... [FUN]/[VAL]/[UI]`).

## 2. Test on the environment
- Run every test case + light regression of existing fields on that page.
- Use JS to read real values (hidden inputs, form validator rules) to give exact actual results.
- Check all UI languages.

## 3. Full-HD evidence (1920×1080)
Browser screenshots are small, so render real full-HD images in the page:
1. Set the tab viewport to 1920×1080.
2. Inject html2canvas from a CDN and define:
```js
window.cap = async () => { const c = await html2canvas(document.body,{x:scrollX,y:scrollY,width:innerWidth,height:innerHeight,windowWidth:innerWidth,windowHeight:innerHeight,scale:1,useCORS:true,logging:false}); window._img=c.toDataURL('image/jpeg',0.9); return [c.width,c.height]; };
```
3. Set the UI state, call `await cap(); window._img`, decode the base64 to `<TC-key>_<what>_FHD.jpg`, and check it visually.
4. If a dropdown is clipped at the page bottom, use a higher row instead of altering the UI.
5. Save the files to the user's Downloads folder, then reset the viewport.

Note: html2canvas may render icon fonts as boxes. Tell the user.

## 4. Fill executions in QAlity Plus
Ticket → QAlity Plus panel → test case `⋯` → **Execute** → choose the sprint cycle.
- **Step comment – SHORT, one plain line** (e.g. `Default color for a new row is Black (#121212).`).
- Step + overall **Status**: Passed / Failed.
- **Execution Comment**: one line, e.g. `FAILED - 3 UI bugs found (21 Oct 2026). Bug: <PROJ>-5678`.
- Keep the user's own comments.
- Leave cases blocked by missing input as Unexecuted and say why.

Editing gotchas:
- Confirm the comment editor is open before select-all, or you'll select the whole page.
- Save by clicking a truly empty area. Never click near the assignee "x" or on attachment thumbnails.
- Layout shifts often, so take a fresh screenshot before each click.
- UI defects found later go to the **[UI]** test case and turn it Failed.

## 5. Raise bugs (after the user gives the parent)
Read the example bug first and match it. Typical format:
- **Bug Sub-Task** under the parent, one per failed test case.
- Summary `[BUG] - <feature> <what is wrong>`
- Description = table **No. | Description | Test Step | Expected Result**, steps as a numbered list.
- Don't send the sprint field on subtasks (they inherit it).
- Link each bug to its test case (`Relates`) and add `Bug: <key>` to the execution comment.

## Known limits
- Some apps block file pickers, image paste and drag-drop for AI agents. Hand the user a table of `execution → file name` to attach.
- Some app buttons (e.g. QAlity "Bugs +") only respond to a real user click.

## Report back
Short table: test case → result → note, bug keys, what the user still needs to do, and anything pending.
