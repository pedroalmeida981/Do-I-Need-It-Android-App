# Interview preparation and QA review

> These are source-review notes and **suggested** test scenarios. They do not represent executed test cases, successful builds or approved production requirements.

## Project in one sentence

Android CRUD app with Firebase Auth and Cloud Firestore.

## Technical interview questions and grounded answers

### 1. What is the app's data flow?

The user authenticates with Firebase Auth; HomeActivity creates or updates a document, ListActivity reads or deletes from the Documents collection, and RecyclerView renders data. Firestore calls are asynchronous.

### 2. What would you improve first?

Add an authenticated owner identifier to every record and enforce ownership with Firestore Security Rules; validate the app with two test accounts before discussing production readiness.

### 3. What is wrong with search?

The code queries whereEqualTo('search', query), but the create path writes id, title and description only. That mismatch means newly created records will usually not satisfy the search.

### 4. How would you make it testable?

Separate repository/service logic from Activities, introduce fake Firebase services for unit tests, use instrumentation tests for UI flows, and maintain repeatable test data. This is a proposal, not work already implemented.

### 5. What does an Espresso dependency prove?

Only that the build declares a UI-test framework, not that meaningful automated tests exist or have passed.

## Suggested functional and accessibility test cases

| ID | Scenario | Expected / verification objective |
| --- | --- | --- |
| AUTH-01 | Register with empty email/password | Validation messages are presented and no account is created |
| AUTH-02 | Log in with invalid credentials | Login fails without navigating into protected screens |
| DATA-01 | Add a valid title and description | Exactly one record persists and can be retrieved |
| DATA-02 | Edit and delete a record | Updated values persist; deleted item is absent after refresh |
| SEC-01 | Sign in as account B and attempt to read account A's entries | Other users' data is inaccessible under correctly configured Security Rules |
| SEARCH-01 | Search for a just-created title | Behaviour matches a documented search specification; current implementation has a likely mismatch |
| A11Y-01 | Navigate fields and buttons with TalkBack and enlarged text | Controls have labels, sensible focus order and readable layout |
| OFFLINE-01 | Lose network during save/delete | UI handles failure without duplicate writes or false success messages |

## Limitations to disclose

The client currently uses a shared Firestore collection without a demonstrated per-user query. Firebase rules cannot be inferred from Android code. Do not claim secured multi-user isolation without checking the deployed rules.

## How to discuss the project

Explain what the code currently contains, which components you actually developed, the difference between intent and implementation, one defect or risk you identified, and how you would validate a fix. Avoid claiming tests have passed unless you can provide reproducible evidence.

## Suggested next improvements

- Verify a clean checkout/build with a documented environment.
- Record reproducible bugs or missing features as GitHub Issues.
- Add evidence-backed screenshots and a short demo only after confirming they match the actual project.
- Add focused automated tests where the architecture makes them worthwhile; keep manual exploratory and accessibility testing visible.
