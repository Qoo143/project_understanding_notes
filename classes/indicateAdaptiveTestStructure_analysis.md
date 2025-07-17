# indicateAdaptiveTestStructure.php Analysis

This file defines the `indicateAdaptiveTestStructure` class, which is the core engine for handling adaptive tests within the platform. It manages the entire lifecycle of an adaptive test, from initialization to result submission.

## Key Responsibilities:

1.  **Test Initialization (`__construct`)**:
    *   Takes a `mission_sn` (mission serial number) as input.
    *   Fetches mission data, including the subject, test type, and the structure of the knowledge map (nodes).
    *   Introduces a caching mechanism (`getPreData`, `makePreData`) to store pre-processed test structure data (`node_link_data`, `bNode_sNodes_count`, `total_items`) in the `indicate_test_predata` table. This avoids costly recalculations for each test session and checks if the map has been updated to invalidate the cache.
    *   Initializes the status of all nodes (`bNode` - big nodes, `sNode` - small nodes) and sets up initial test parameters.

2.  **Item Selection (`getNextItem`)**:
    *   The heart of the adaptive logic. It determines which question (`item_sn`) to present next.
    *   Navigates through a pre-defined order of "small nodes" (`sNodes_order`).
    *   When a small node is completed, it calls `modifyNodeStatus` to update the student's mastery status and `modifyLowNodeStatus` to propagate the results to connected nodes in the knowledge graph (e.g., predicting mastery of higher-level nodes).
    *   Handles different test phases (`step=1` for adaptive, `step=2` for an optional full test).

3.  **Answering and Response Handling (`checkAns`)**:
    *   Receives the user's answer, compares it to the correct answer (handles multiple-choice), and records the response, time, and other metrics.
    *   Updates overall item success rates for statistical purposes.

4.  **State Management and Persistence**:
    *   **Pause/Resume**: Implements robust functionality (`examPause`, `getExamPauseData`) to save the complete test state to the `exam_record_indicate_tmp` table, allowing users to resume later.
    *   **Final Submission**:
        *   `makeExamSql`: Compiles the final results and saves them permanently to the `exam_record_indicate` table.
        *   `updateExamineeSql`: Updates the `map_node_student_status` table, which stores the student's long-term mastery profile for the knowledge map.
        *   Integrates with a `giveCoin` class for gamification rewards.

## Key Concepts & Terminology:

*   **Mission (`mission_sn`)**: A specific test or learning task.
*   **Nodes (`bNode`, `sNode`)**: The test is structured around a knowledge map of "big nodes" (concepts) and "small nodes" (skills).
*   **Node Status**: A numerical code representing the student's mastery state for a node (-1: Not attempted, 0: Incorrect, 1: Correct, 2: Predicted Correct, etc.).
*   **Caching (`indicate_test_predata`)**: Pre-calculating and storing the node structure to improve performance.

## Dependencies:

*   `prodb_mission_record_update.php`: For updating mission records.
*   `adp_core_class.php`: Contains shared core functions/classes.
*   `giveCoin` class: For the reward system.
*   Global database handles (`$dbh`, `$dbh_slave`).
*   Global functions like `sNode2bNode()`.

## Overall Impression:

This is a complex and sophisticated class for an adaptive testing system. It handles test flow, caching, state persistence, results storage, and integration with a student's learning profile and a gamification system. The logic for traversing the knowledge graph and updating node statuses is its most critical feature.
