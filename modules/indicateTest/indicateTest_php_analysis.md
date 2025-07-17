# indicateTest/indicateTest.php Analysis

This file acts as the **launcher and initializer** for the session-based adaptive test. It is responsible for setting up the test environment, building the test structure, and displaying the first question. It is tightly coupled with the functions in `classes/indicateTestFun.php`, which it uses to drive the testing process.

## Functional Breakdown:

1.  **Parameter Handling and Initialization**:
    *   It processes incoming request parameters such as `map_sn` (knowledge map ID), `subject`, and `result_sn` (likely equivalent to `mission_sn`).
    *   It calls the `chk_exam_indicate` function to check the `exam_record_indicate_tmp` table for any incomplete test records for the current user.

2.  **Test Flow Control**:
    *   **Resuming a Test (`continue_Exam`)**: If an incomplete test is found, this function is called. It reads the saved state from the database, restores it into the `$_SESSION['indicateInfo']` variable, determines the next question, and calls `showItem` to display it, allowing the user to seamlessly continue.
    *   **Starting a New Test**: If no incomplete test exists, it executes a large block of initialization logic:
        *   **Build Node Order**: Starting from an initial node (`firstIndicate`), it recursively queries the `indicateTest` database table to build a complete, ordered sequence of `bNodes` and `sNodes`.
        *   **Prepare Items**: It iterates through the sorted `sNodes` and fetches all associated questions from the `concept_item` table.
        *   **Save to Session**: Critically, it saves all the prepared test data—node sequences, item lists, answers, statuses—into a large `$_SESSION['indicateInfo']` array.
        *   **Display First Item**: Finally, it calls the `showItem` function (from `indicateTestFun.php`) to render the first question, officially starting the test.

3.  **Relationship with `indicateTestFun.php`**:
    *   This file is the **consumer** of the `indicateTestFun.php` library. It prepares the complex `$_SESSION['indicateInfo']` data structure that the functions in `indicateTestFun.php` rely on. Once the session is prepared, it hands off control to those functions to manage the interactive part of the test.

## Conclusion:

`modules/indicateTest/indicateTest.php` is the **setup and launch script** for the session-based adaptive testing flow. It handles all the preparatory work before a test begins, including parameter parsing, resuming interrupted tests, and building the entire test structure for new tests. This confirms that `indicateTest.php` and `indicateTestFun.php` work together to form a complete, albeit likely older, implementation of the adaptive testing system.
