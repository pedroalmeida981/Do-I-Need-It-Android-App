# QA case study: Android list management with Firebase

**Project:** Do I Need It?  
**Review type:** Static source review and risk-based test design  
**Execution status:** No current emulator/device run, Firebase deployment or automated test execution was performed for this review.

## Objective

Evaluate the most important quality risks in a Java Android app that uses Firebase Authentication and Cloud Firestore to create, display, edit and delete personal entries.

The purpose of this case study is to demonstrate **how I would investigate and test the software**, not to imply that every scenario was executed.

## What the code shows

The main user journey spans:

1. **Registration and login** — `FirstActivity.java` and `LoginActivity.java` communicate with Firebase Authentication.
2. **Entry management** — `HomeActivity.java` creates and updates records in Firestore.
3. **List and deletion** — `ListActivity.java` reads records into a RecyclerView and deletes them.
4. **Search** — `ListActivity.java` executes a Firestore `whereEqualTo("search", query.toLowerCase())` query.

Source references:

- [FirstActivity.java](../app/src/main/java/com/example/mainactivity/FirstActivity.java)
- [LoginActivity.java](../app/src/main/java/com/example/mainactivity/LoginActivity.java)
- [HomeActivity.java](../app/src/main/java/com/example/mainactivity/HomeActivity.java)
- [ListActivity.java](../app/src/main/java/com/example/mainactivity/ListActivity.java)

## Risk-based analysis

| Priority | Area | Source-level observation | Why it matters |
| --- | --- | --- | --- |
| P0 / investigate | Authorisation and privacy | The inspected Firestore CRUD queries use a common `Documents` collection without filtering by authenticated owner. | Proper Firebase Security Rules are essential. Access isolation cannot be assumed from the Android client alone. |
| P1 | Search | Creation writes `id`, `title`, and `description`, while search queries a separate `search` field. | New records are unlikely to match search unless another uninspected process populates that field. |
| P1 | Deletion feedback | The delete completion handler displays `Deleted...` without checking `task.isSuccessful()`. | The UI can report success even if a backend operation fails. |
| P2 | Input handling | Creation checks that trimmed title and description are nonempty. | Length limits, special characters, unusually large input and duplicate submissions still need validation. |
| P2 | Lifecycle and loading | Several Activities display progress dialogs and run asynchronous tasks. | Rotation, navigation and network loss can affect lifecycle and feedback. |
| P2 | Accessibility | Android XML/View screens contain form controls and list interactions. | Labels, keyboard/focus order, touch-target size and large-text behaviour should be evaluated on a device. |

**Priorities are suggested triage priorities**, not confirmed production severity labels or demonstrated exploitability.

## Example exploratory test charter

**Charter:** Investigate data isolation, CRUD consistency and error feedback across two Firebase test accounts.

**Preconditions:** A safe staging Firebase project, authorised test accounts A and B, known Firestore rules, network toggling capability, and a clean install.

**Tests to perform:**

1. Register and sign in as account A. Create two records, including one with punctuation and mixed case.
2. Confirm the list refreshes and shows exactly those records.
3. Edit one record; reopen the screen and confirm persistence.
4. Search by title and record whether search behaves as expected.
5. Sign out, sign in as B and check whether A's records are readable or editable.
6. Disable networking during a create or delete operation. Confirm the UI does not report unsupported success and recovers cleanly.
7. Repeat key tasks with a screen reader, larger font size and keyboard where applicable.

**Evidence to collect:** device/OS version, Firebase rules version, account IDs that contain no real personal information, reproduction steps, expected/actual behaviour, screenshots or video, logs, and network-state observations.

**Pass/fail:** Outcomes must be judged against explicit acceptance criteria and configured backend policies. Do not mark tests as passed without execution and evidence.

## Defect-report example — search-field mismatch

**Title:** New records are not represented in the field queried by search.

**Evidence from code:**
- `HomeActivity.uploadData()` writes `id`, `title`, and `description`.
- `ListActivity.searchData()` queries `whereEqualTo("search", query.toLowerCase())`.

**Expected design to clarify:** Search should return relevant newly created entries according to defined matching rules.

**Observed:** Static inconsistency in the producer and consumer data schema. **Not runtime-reproduced.**

**Suggested fix:** Agree on the search semantics, store/update a normalised searchable field (or use a suitable search service), consider existing documents, then add tests covering creation, update, case variation and empty results.

## Proposed regression suite

- Authentication: valid/invalid registration, sign-in, session expiry and sign-out.
- CRUD: new, edit, delete, refresh and duplicate submission.
- Search: case handling, empty query, punctuation and newly edited records.
- Data security: account separation and read/write rule enforcement.
- Resilience: offline, timeout, interrupted operation and lifecycle recreation.
- Accessibility: screen reader labels, contrast, dynamic text scaling and focus order.

**Automation strategy (proposal):** Extract data interactions behind an interface for unit tests; use Espresso/UI instrumentation for critical navigation; use isolated Firebase test resources/emulators for CRUD and rules tests. Keep accessibility checks part of human-reviewed testing rather than claiming automation can catch all issues.

## What this demonstrates

A useful QA review starts by identifying the user journey and the risks with the biggest impact. This repository provides concrete examples of data integrity, authorisation, asynchronous feedback and testability concerns. The next step in a real project would be to validate each hypothesis, file reproducible defects and retest changes.
