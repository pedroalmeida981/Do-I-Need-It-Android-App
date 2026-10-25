# Do I Need It? — Android list-management prototype

**Java · Android SDK · Firebase Authentication · Cloud Firestore**

> **Status:** Academic/personal learning project. Source code documents implemented flows, but a current end-to-end build and Firebase deployment have not been verified.

## Overview

Do I Need It? is a native Android application for creating and managing personal entries containing a title and description. Although the project name suggests purchase decisions, the current source implements a **Firebase-backed list and account flow**, not a purchase recommendation engine.

It is presented here as an example of Android UI development, asynchronous cloud operations, and a practical target for QA analysis.

## Features visible in the source

- Email/password registration and sign-in with Firebase Authentication.
- Sign-out navigation.
- Create and update Firestore documents with a title and description.
- Retrieve entries into a RecyclerView.
- Delete an entry and refresh the list.
- Search UI and a Firestore search query (see limitation below).
- Feedback through Toast messages and progress dialogs.

## Technology and structure

| Component | Implementation |
| --- | --- |
| Language | Java (Gradle also declares Kotlin tooling) |
| UI | Android Views, XML layouts, RecyclerView |
| Authentication | Firebase Authentication |
| Data | Cloud Firestore collection named Documents |
| Build | Gradle / Android Gradle Plugin; compile and target SDK 34 |
| Minimum Android version | API 26 |
| Test dependencies | JUnit and AndroidX Espresso (configured; no comprehensive automated suite verified) |

Important classes are under app/src/main/java/com/example/mainactivity:
- **FirstActivity / LoginActivity** — account registration and authentication.
- **HomeActivity** — entry creation and editing.
- **ListActivity** — listing, deletion and search query.
- **Model / CustomAdapter / ViewHolder** — list data and rendering.

## Run locally

1. Clone the repository and open it in Android Studio.
2. Use a compatible JDK/Android SDK, then allow Gradle to sync.
3. Configure **your own Firebase project** for authentication and Firestore. Review Android application ID and Firebase configuration before running. Never publish private credentials or service-account keys.
4. Review Firebase Security Rules and enable only the necessary authentication providers.
5. Start on an Android device/emulator running API 26 or newer.

An existing Firebase configuration file is tracked in the historical project; do not assume it is suitable or authorised for use in a new environment. No build or deployment has been validated as part of this documentation update.

## Known limitations and risks

- **Data isolation:** entry reads and writes use the shared Documents collection without a user-ID filter in the inspected client code. Production use requires correctly enforced server-side Firebase Security Rules and user ownership.
- **Search:** the query filters on a search field, but creation only writes id, title and description. Newly created entries may not be returned by search.
- **Error handling:** some asynchronous flows provide generic feedback, and deletion completion should check actual task success.
- **Maintenance:** progress dialogs and some Android APIs are dated; code and dependencies need a modern build review.
- **Testing:** dependency declarations do not demonstrate meaningful automated test coverage.

## QA opportunities

Useful manual tests include registration and login failures, empty/whitespace input, offline save and retry, cross-account data isolation, duplicate submissions, update/delete persistence, empty lists, search consistency, loading/error feedback and screen-reader/keyboard accessibility.

Read the [QA case study](docs/QA_CASE_STUDY.md) for a detailed source-based assessment of security, CRUD, search and error handling. These scenarios are **proposals, not claims that tests have been executed**.

## Portfolio context

This repository is a learning project rather than a production application. It demonstrates the foundations of an Android/Firebase workflow and offers realistic examples for discussing functional quality, accessibility, data security and technical debt.