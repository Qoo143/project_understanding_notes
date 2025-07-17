# ExamResult/prodb_missionreport.php Analysis

This file is the **backend data provider for the teacher's mission report page** (`missionReport.php`). It's a pure logic file responsible for handling AJAX requests, fetching vast amounts of data from various database tables, and processing it into a format that the frontend can use to display the complex comparison grid.

### Key Functionality:

1.  **API Endpoint Router**:
    *   It uses a `switch` statement on the `$_POST['act']` parameter to handle different requests from the frontend.
    *   **`getSelectOpt`**: Fetches the necessary data to populate the filter dropdowns on the report page (e.g., lists of classes for the logged-in teacher).
    *   **`getMissionList`**: This action is for the *student's* view of their own report. It fetches all of a single student's completed missions and their scores across multiple attempts.
    *   **`getMissionReport`**: This is the main action for the *teacher's* report. It's the most complex part of the file.
    *   **`getMissionAssigner`**: Retrieves a list of teachers who have assigned missions to a specific class.

2.  **Complex Data Aggregation (`getMissionReport`)**:
    *   **Multi-Table Queries**: This action performs incredibly complex data aggregation. The main query is a `UNION` of multiple `SELECT` statements, each designed to fetch results from a different type of exam record table:
        *   `exam_record_indicate` (for adaptive tests)
        *   `exam_record_indicator` (for older indicator-based tests)
        *   `eduexam_record` (for general educational exams)
        *   `mission_stud_record` (for SRL and other non-standard missions)
        *   `exam_record_literacy_interactive` (for literacy tests)
    *   **Data Grouping**: It uses `GROUP_CONCAT` extensively to aggregate multiple attempts for the same mission into a single row, with scores and exam IDs separated by a special delimiter (`@XX@`). This is a key technique for collecting all the data needed for comparison.
    *   **Student & Mission Filtering**: It takes the filter parameters from the AJAX request (class, semester, assignor) and builds the appropriate `WHERE` clauses to fetch only the relevant data.

3.  **Data Processing and Structuring**:
    *   After fetching the aggregated data, it loops through the results to structure them for the frontend.
    *   For each mission, it creates an array of student results.
    *   For each student, it creates an array of their attempts at that mission, including the score and a pre-formatted URL to the detailed report for that specific attempt.
    *   This nested data structure is then encoded as JSON and sent back to the frontend, where JavaScript code can easily parse it to build the report grid.

4.  **Report URL Generation**:
    *   A significant part of the logic is dedicated to generating the correct URL for the detailed report (`viewErrors.php`, `adaptiveExamResult.php`, etc.) based on the `mission_type` of each record. This ensures that clicking on a score in the report grid takes the user to the correct type of report page.

### Conclusion:

`prodb_missionreport.php` is the powerful data engine behind the mission report feature. It demonstrates advanced SQL techniques (especially `UNION` and `GROUP_CONCAT`) to aggregate data from many different parts of the system into a single, coherent report. It completely separates the data-access and business logic from the presentation layer (`missionReport.php`), which is a strong architectural pattern.
