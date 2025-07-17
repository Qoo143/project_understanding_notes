# dashboard/modules_teacher_get_mission.php Analysis

This file is the **backend data provider (API endpoint)** for the teacher's mission dashboard. It's a pure logic file that receives AJAX requests from the frontend (`modules_dashboard.php`), queries the database based on the provided filters, and returns the mission data in JSON format.

### Key Functionality:

1.  **Action-Based Controller**:
    *   The entire file operates as a controller that routes requests based on an `action_code` parameter.
    *   It uses a `switch` statement to handle different actions, such as:
        *   `GET_MISSION_TOTAL`: Get the total count of missions based on filters.
        *   `GET_MISSION_INFO`: Get the detailed information for missions on a specific page.
        *   `GET_QUICK_TABLE_INFO`: Get the data needed to build the "Quick Table" view (student vs. mission grid).
        *   `SET_MISSION_UNABLE`: Mark a mission as deleted.
        *   `GET_SELECT_OPT`: Get the options for the filter dropdowns (e.g., list of classes, mission types).
        *   `SET_SHARE_CODE`: Generate or update a sharing code for a mission.

2.  **Complex Database Querying**:
    *   The core of this file is building and executing complex SQL queries to fetch mission data from the `mission_info` table and related tables (`mission_stud_record`, `user_info`, etc.).
    *   **Dynamic Query Building**: The `getMissionInfoByMissionCondition` and `getMissionTotalCount` functions dynamically construct the `WHERE` and `JOIN` clauses of the SQL query based on the numerous filter conditions sent from the frontend (search text, semester, class type, assignor type, etc.). This is a powerful but complex approach.
    *   **Role-Based Scopes**: It correctly adjusts the query scope based on the user's role (`access_level`). For example, a principal (`ALL_CLASS`) can see all missions in the school, while a teacher (`TEACHER_CLASS`) can only see their own.

3.  **Data Processing and Enrichment**:
    *   After fetching the raw data from the database, the script heavily processes and enriches it before sending it back as JSON. This includes:
        *   **Deserialization**: It unserializes mission data stored in a serialized format in the database (using `unserialize_with_hash`).
        *   **Calculating Completion Rates**: It calls `getMissionCompleteAndTotalCount` to calculate the number of students who have completed each mission and the overall pass rate.
        *   **Generating URLs**: It constructs the correct URLs for students to start each part of a mission (e.g., links to videos, practice questions, dynamic assessments).
        *   **Formatting Data**: It formats data for display (e.g., creating a duration string like "5 days left").
        *   **Encryption**: It encrypts sensitive IDs (like `mission_sn`) before sending them to the client using `string2hash`.

4.  **Mission Patching**:
    *   It includes a call to `createMissionPatch`, which seems to be a mechanism to automatically assign a mission to new students who join a class *after* the mission was originally assigned, ensuring no one is missed.

### Conclusion:

`modules_teacher_get_mission.php` is the **workhorse backend for the teacher dashboard**. It's a pure data processing script that encapsulates the complex business logic of filtering, retrieving, and preparing mission data. It acts as the "model" and "controller" in an MVC pattern, serving data to the `modules_dashboard.php` "view" via AJAX. The extensive use of constants for action codes and mission types makes the code more readable and maintainable.
