# dashboard/modules_student.php Analysis

This file generates the **student's mission dashboard**. It's a comprehensive page that lists all assigned tasks, tracks their progress, and provides entry points to start or continue them.

### Key Functionality:

1.  **Mission Retrieval and Display**:
    *   It includes `mission_action_general.php`, which appears to be the core script responsible for fetching all relevant missions for the logged-in student from the database.
    *   The main part of the file is a `foreach` loop that iterates through the `$sub_mission` array (provided by the included script) and generates an HTML "card" for each mission.
    *   Each card displays crucial information:
        *   Mission type, name, and assigning teacher.
        *   A countdown timer for the deadline.
        *   A progress bar showing the completion percentage (`$finish_node_count / $all_node_count`).

2.  **Filtering and View Options**:
    *   Similar to the teacher's dashboard, students can filter their missions. However, the options are simpler:
        *   **Assignor**: Teacher-assigned vs. Self-assigned vs. Parent/Tutor-assigned.
        *   **Mission Type**: Filter by the type of task (e.g., video, practice).
        *   **Status**: In Progress, Expired, or Completed.
    *   It also supports switching between a **Card View** and a **List View**, and this preference is saved per-user in the `user_person_config` table.

3.  **Interactive Elements**:
    *   **Mission Details**: Clicking on a mission card triggers an AJAX call (handled in `assignment.js`) to `prodb_mission_info_seri.php` to fetch and display detailed information about the mission's nodes or content.
    *   **Starting a Mission**: The mission title is a link that takes the student to the actual task. The code constructs the appropriate URL, handling different mission types (e.g., regular missions vs. Google Form questionnaires).
    *   **Calendar Integration**: Each mission has an icon allowing the student to add the task to their personal in-system calendar (`srl/calender_todolist.php`). This is handled via a sophisticated JavaScript modal window.

4.  **Special Handling**:
    *   **Google Forms**: It has specific logic to correctly format URLs for Google Form-based missions (questionnaires), pre-filling student information where possible.
    *   **Mobile/Tablet**: It detects if the user is on a mobile device and may adjust how mission windows are opened (e.g., forcing a new tab).
    *   **Group Leaders**: It checks if the student is a group leader and, if so, may display additional links to view their group's progress.

### Conclusion:

`modules_student.php` is the student's central hub for all learning tasks. It's a feature-rich interface that provides a clear overview of assigned work, tracks progress, and allows for easy navigation to the learning activities themselves. Like the teacher's dashboard, it relies on a combination of server-side data preparation and client-side JavaScript for its interactivity and dynamic content loading.
