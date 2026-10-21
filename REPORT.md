# Lab 3 Web API -- Project Report

## What I specified

Before completing each test, I discussed the expected behaviour and checks. A safe method does not request a change to server state. An idempotent method has the same intended effect when repeated; its responses can differ.

| Request | Safety and idempotency | Expected checks |
| --- | --- | --- |
| `POST /employees` | Not safe or idempotent. | Two identical requests produce different ids; both call `save`. |
| `GET /employees/{id}` | Safe and idempotent. | Repeated requests return the same employee or `404` for an absent id, without repository writes. |
| `GET /employees` | Safe and idempotent. | Repeated requests return the same list without repository writes. |
| `PUT /employees/{id}` | Not safe, but idempotent. | Two identical requests leave the same employee at the target id; the first returns `201` and the second `200`. |
| `DELETE /employees/{id}` | Not safe, but idempotent. | Two requests call delete for the same id and both return `204`; the intended final state is absent. |

The tests should pass when the expected HTTP responses and repository interactions match. The repository is mocked, so its configured responses represent the scenario rather than proving persistence in a real database.

## What I changed

I completed the setup and verification TODOs in `src/test/kotlin/es/unizar/webeng/lab3/ControllerTests.kt` and added the required MockK and `Optional` imports. The existing requests and response expectations were kept. I did not change the controller or add bonus work.

## Technical decisions

- **POST:** I used consecutive `save` answers with ids 1 and 2. The test verifies two saves of Mary with a null id and no repository reads or deletes. The request does not contain an id, so this test alone does not prove what happens when a client supplies one.
- **GET one:** I configured `findById(1)` to return Mary and `findById(2)` to return empty. Each lookup is verified twice; `save`, `deleteById`, and `findAll` are not called. `findAll` is a read, but it is not needed for an individual lookup.
- **PUT:** I configured `findById(1)` to return empty and then Tom, and `save` to return Tom at id 1. Two lookups and two saves of the same employee are verified, with no delete or list calls. The different `201` and `200` responses are compatible with the same intended final state.
- **DELETE:** I used `justRun` for `deleteById(1)` and verified two delete calls and no save or read calls. The mock records calls; it does not actually remove a database row.
- **GET collection:** I configured `findAll` to return a list containing Mary. It is verified twice, while save, delete and individual lookup are not called.

I used the blocks from `docs/GUIDE.md` for these scenarios. I did not change the controller to make the tests pass or add a real database test: the required tests use a mock.

## How I verified

Initially, the five tests were incomplete and contained TODOs, so `.\gradlew.bat check` reported `NotImplementedError` for each test. I completed the tests one at a time and ran each one individually using `.\gradlew.bat test --tests` with its test name.

After confirming that the individual tests passed and checking the code format, I ran `.\gradlew.bat check` for the complete project. It returned `BUILD SUCCESSFUL`, confirming that the tests and format checks passed. I also reviewed the mock interactions and changes before committing.

## AI disclosure

- **Tools / skills:** ChatGPT-5.6 Terra. No project skill or `AGENTS.md` was added.
- **Purpose:** Understand the guide and HTTP semantics; explain test blocks one step at a time; review changes, diagnose formatting, and help with Git and this report.
- **Representative prompts:** Explain the lab requirements and HTTP method semantics, describe each test step by step using the guide's blocks, help verify the results, and assist in preparing the report.
- **Affected files/sections:** `ControllerTests.kt` and this report.
- **Validation steps:** I ran the individual tests and the final `.\gradlew.bat check`, reviewed the changes and results, and approved the commits.
- **Citations:** MockK blocks were taken from the supplied `docs/GUIDE.md`; no other external code snippets were adapted.
- **Human-reviewed:** I reviewed the proposed steps and explanations, ran the commands, reviewed and corrected the code changes, and authorised the commits. I chose not to do bonus work.
