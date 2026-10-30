# Jenkins CI Challenge - Student Evidence & Submission

**Student Alias (Git Author):** `Yazh-7z7`  
**Class Code:** `1`  
**Challenge Code:** `00387`  
**Pipeline Job:** `festpass-ci`  
**Agent:** `student-laptop`  

---

### 1. Build Trace & Commits

| Metric | Red Build | Green Build |
| :--- | :--- | :--- |
| **Build Number** | `#2` | `#3` |
| **Commit ID** | `56bc6ce33575d60e8ca53f97c64d492a6142fb8f` | `cd3829a52696719ad2d4cbb8a0bd8c174154f8ff` |
| **Commit Message** | `Personalize my help introduction` | `Fix booking cancellation defect` |
| **Build Cause** | `Started by an SCM change` (automatic polling) | `Started by an SCM change` (automatic polling) |
| **Pipeline Result** | `FAILURE` | `SUCCESS` |
| **Test Counts** | 28 tests (25 passed, 3 failed, 0 errors, 0 skipped) | 28 tests (28 passed, 0 failed, 0 errors, 0 skipped) |
| **Skipped Stages** | `Build JAR`, `Smoke test JAR`, `Save artifact` | None (all stages executed successfully) |
| **JAR Status** | `NOT_PUBLISHED` | Published (`festpass.jar`) |
| **JAR SHA-256** | N/A | `21b98f6ca56cd8035d8dde46d148397810e7e2cd66e43637cf6f53e44e6c9c7b` |

---

### 2. Diagnosis and Defect Repair

- **Failing Assertion:**  
  `FAIL cancellationFreesSeat: SOLD OUT. Even the developer needs a ticket.` (`FestPassTest.java:147`)  
  Additional failures: `cancellationsSurviveReload` and `cliWorkflowPersistsAcrossCommands`.
- **Violated Business Rule:**  
  When a student cancels their registration for an event, that seat must be freed so another student can book it.
- **Root Cause:**  
  In `src/main/java/BookingService.java`, the `cancel(String eventId, String studentId)` method validated that the booking existed, but failed to remove the student from the internal registration list (`event.students`).
- **Code Fix Made:**  
  Added `event.students.remove(studentId);` directly inside `cancel()` in `BookingService.java`:
  ```java
  public void cancel(String eventId, String studentId) {
      validateId(studentId);
      Registration event = requireEvent(eventId);
      if (!event.students.contains(studentId)) {
          throw new IllegalArgumentException("No booking for student: " + studentId);
      }
      event.students.remove(studentId);
  }
  ```
  All 28 tests remained untouched and passed cleanly.

---

### 3. Runnable JAR Verification

Executing `java -jar festpass.jar help`:
```text
FestPass - campus event registration (Yazh-7z7)
Usage: java -jar build/festpass.jar [--data FILE] COMMAND
  add-event ID "TITLE" CAPACITY
  list
  book EVENT_ID STUDENT_ID
  cancel EVENT_ID STUDENT_ID
  roster EVENT_ID
  summary
  help
Default data file: data/festpass.tsv
IDs are case-sensitive. Use synthetic student IDs, not personal data.
Exit codes: 0 success, 2 invalid request, 3 storage failure.
```

---

### 4. Submitted Artifacts

1. `submission/build-red/submission-evidence.csv` (Archived diagnostic evidence from the failing SCM-polled Build #2)
2. `submission/build-green/submission-evidence.csv` (Archived evidence from the successful SCM-polled Build #3)
3. `submission/build-green/festpass.jar` (Executable runnable JAR built by Jenkins Build #3)
4. `submission/jar-run-evidence.txt` (Complete CLI execution log of the final JAR)
5. `festpass-java/` (Git repository with complete commit history for Yazh-7z7)
