# srl/checklists_new.php Analysis

This file provides the user interface for teachers to **create, manage, and view self-regulated learning (SRL) checklists and rubrics**. It's a comprehensive tool that allows teachers to design various types of assessment forms.

### Key Functionality:

1.  **Main View - List of Checklists**:
    *   The default view is a list of all checklists the teacher has created.
    *   **Filtering**: Teachers can filter this list by academic semester and by the type of form.
    *   **Actions**: For each checklist in the list, several actions are available:
        *   **View**: Preview the checklist.
        *   **Edit**: Modify the checklist (only if it hasn't been "exported" or used in a mission).
        *   **Copy**: Duplicate an existing checklist to create a new one.
        *   **Delete**: Remove a checklist.
        *   **Export/Lock**: A key feature is the ability to "export" a checklist. This locks the checklist, preventing further edits, and makes it available to be assigned as a mission.

2.  **Checklist Creation/Editing Interface**:
    *   Clicking "Add Checklist" or "Edit" opens a modal window with a step-by-step wizard.
    *   **Step 1: Form Type**: The teacher first chooses the type of form they want to create:
        *   `檢核單` (Checklist): A simple yes/no checklist.
        *   `同儕評分表` (Peer-review Rubric): A rubric for students to score their peers.
        *   `組間評分表` (Inter-group Rubric): A rubric for scoring other groups.
        *   `組內評分表` (Intra-group Rubric): A rubric for students to score members within their own group.
    *   **Step 2: Build Form**: The teacher defines the content of the form:
        *   **Title**: The name of the checklist.
        *   **Criteria**: They can add, edit, and delete rows, with each row representing a "scoring standard" or criterion.
        *   **Scoring**: For each criterion, they can assign a point value.
    *   **Step 3: Confirm Form**: A final preview of the form is shown before it is saved.

3.  **Backend Communication**:
    *   All actions (loading data, creating, saving, deleting, exporting) are handled via AJAX requests to the backend processor, `prodb_checklists_new.php`.
    *   The frontend sends the form data (title, criteria, scores, etc.) to the backend, which then performs the necessary database operations on the `check_list_table`.

### Conclusion:

`checklists_new.php` is a powerful and flexible tool for creating various types of rubrics and checklists to support self-regulated learning activities. It provides a user-friendly, wizard-based interface for a complex creation process. The distinction between "draft" and "exported" (locked) checklists is a key feature, ensuring that once a form is used in a live mission, its structure cannot be changed, thus maintaining data integrity for any results collected.
