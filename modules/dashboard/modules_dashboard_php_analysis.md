# dashboard/modules_dashboard.php Analysis

This file acts as the main entry point for the **teacher's dashboard**. It dynamically loads and displays assigned missions (tasks) and provides extensive filtering and viewing options.

### Key Functionality:

1.  **Role-Based Access**:
    *   It first checks the user's `access_level` from the session to determine if they are a teacher or a parent. This suggests that while the file is primarily for teachers, it might have variations for other roles.

2.  **Dynamic Mission Display**:
    *   The core of the page is a highly interactive interface for viewing missions. It does not contain the mission data itself but rather provides the HTML structure and JavaScript hooks to fetch and display it.
    *   **AJAX-driven**: The page sets up several JavaScript variables (`routerForDashboard`, `routerForMissionLayout`, etc.) that contain AJAX endpoints. These endpoints are used by the accompanying JavaScript files (`mission_list.js`, `assignment.js`) to asynchronously load mission data. The main data source appears to be `modules_teacher_get_mission.php`.
    *   **Multiple Views**: It supports three different views for missions, which the user can switch between:
        *   **Card View** (`missionLayout_card`): Displays each mission as a separate card.
        *   **List View** (`missionLayout_list`): Displays missions in a more compact list format.
        *   **Quick Table View** (`quickTable`): A powerful grid view that shows students on the y-axis and missions on the x-axis, with icons indicating the completion status of each student for each mission.

3.  **Advanced Filtering**:
    *   It features a comprehensive filtering sidebar that allows teachers to narrow down the displayed missions based on:
        *   Semester (`semeSelector`)
        *   Class Type and Class Number (`classTypeSelector`, `classNumSelector`)
        *   Assignor (e.g., Teacher-assigned, Student-assigned) (`founder`)
        *   Mission Type (`missionType`)
        *   Specific Child (for parents) (`child`)
        *   Keyword Search (`searchText`)

4.  **Teacher Actions**:
    *   A prominent "+ 指派任務" (+ Assign Mission) button links to the mission assignment interface (`assign_mission`), allowing teachers to create new tasks for their students.

5.  **State Persistence**:
    *   The script is designed to remember the user's filter settings and current page across reloads by reading parameters from the URL (`$_GET['page']`, `$_GET['searchText']`, etc.) and passing them to the JavaScript.

### Conclusion:

`modules_dashboard.php` is the central hub for teachers to manage and monitor student missions. It is a sophisticated, client-side driven interface that relies heavily on JavaScript and AJAX to provide a flexible and interactive user experience. It acts as the "view" in an MVC-like pattern, with the data being fetched from other PHP scripts (`modules_teacher_get_mission.php`) and the user interaction being handled by dedicated JavaScript files.
