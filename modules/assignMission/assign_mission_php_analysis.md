# assignMission/assign_mission.php Analysis

This file is the user interface for the **mission creation wizard**. It provides a step-by-step process for teachers to create and assign new learning tasks to students. It's a highly complex and dynamic single-page interface.

### Key Functionality:

1.  **Four-Step Wizard Interface**:
    *   The entire process is broken down into four clear steps, with tabs at the top guiding the user:
        1.  **Step 1: Task Type (任務類型)**: The user first chooses the type of mission they want to create (e.g., Knowledge Structure, Unit Test, Questionnaire). They can also choose to load a mission from a shared template (either school-wide or via a private code).
        2.  **Step 2: Task Content (任務內容)**: Based on the type selected in Step 1, this step dynamically loads the appropriate interface for selecting content. For a "Knowledge Structure" mission, it loads an `iframe` with the D3.js knowledge map. For a "Unit Test," it provides dropdowns to select the subject, grade, and specific test papers.
        3.  **Step 3: Task Settings (任務設定)**: Here, the teacher defines the mission's parameters:
            *   **Name**: The title of the mission.
            *   **Duration**: Start and end dates.
            *   **Target**: Who to assign the mission to. This is a very flexible section, allowing assignment to entire classes, specific individuals, or student groups.
            *   **Group Leader Authorization**: Optionally grant group leaders permission to view their group's progress.
        4.  **Step 4: Confirm Task (確認任務)**: This final step displays a summary of all the selected settings and content for the teacher to review before creating the mission.

2.  **Dynamic and Interactive UI**:
    *   **JavaScript Heavy**: The page relies heavily on JavaScript (specifically `assignment.js` and `adp_core_validation.js`) to manage the user's journey through the four steps.
    *   **AJAX for Content**: The content for Step 2 (the mission content itself) is loaded dynamically via AJAX calls to `assign_mission_prodb.php`. This prevents the page from having to load all possible mission types at once.
    *   **Conditional UI**: The UI changes based on user selections. For example, selecting "Group" as the target in Step 3 will show a list of student groups, while selecting "Class" will show a list of classes. The "More Settings" section also dynamically shows relevant options (like "Randomize Questions" or "Share Mission") based on the mission type.

3.  **Role-Based Variations**:
    *   The interface shows slightly different options based on the user's role. For example, a regular teacher has a full list of mission types, while a parent might only be able to assign "Knowledge Structure" or "Diagnostic Test" tasks.

4.  **Backend Communication**:
    *   The final "Create" button in Step 4 sends all the collected data (mission type, content, settings, targets) to `assign_mission_prodb.php` to be processed and saved to the database.

### Conclusion:

`assign_mission.php` is a sophisticated and user-friendly wizard for creating learning tasks. It masterfully uses a combination of PHP to structure the page and extensive JavaScript/AJAX to create a dynamic, single-page application experience. It guides the teacher through a complex process in a clear, step-by-step manner, hiding and showing relevant options as needed. This is the primary interface for content creators and instructors within the system.
