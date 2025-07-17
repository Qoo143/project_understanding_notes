# srl/learning_regulated_fillout.php Analysis

This file is the **interface for students to fill out the SRL checklists and rubrics** that were created by the teacher in `checklists_new.php`. It's a dynamic page that renders the correct form based on the type of checklist being used.

### Key Functionality:

1.  **Form Rendering**:
    *   The script receives a `check_list` SN and a `mission_sn` from the URL.
    *   It makes an AJAX call to `prodb_learning_regulated_fillout.php` (action: `getTable`) to fetch the structure of the specified checklist (title, criteria, scoring, and type).
    *   Based on the `type` of the checklist, it dynamically renders one of two main interfaces:
        *   **Checklist (`檢核單`)**: Displays each criterion with "Yes" and "No" radio buttons.
        *   **Rubric/Score Sheet (`評分表`)**: Displays each criterion with a series of stars (`<i class="far fa-star"></i>`) that the user can click to assign a score.

2.  **Handling Different Rubric Types**:
    *   The page has complex logic to handle the four different types of forms:
        *   **Checklist (Type 1)**: A simple form for self-assessment.
        *   **Peer-Review (Type 2, `同儕評分表`)**: The left panel lists all classmates. The student selects a classmate to score them using the rubric on the right.
        *   **Inter-Group (Type 3, `組間評分表`)**: The left panel lists all other groups in the class. The student (likely a group leader) selects a group to score.
        *   **Intra-Group (Type 4, `組內評分表`)**: The left panel lists the members of the student's *own* group, allowing them to score each other.

3.  **User Interaction and Data Submission**:
    *   **Student View**: Students interact with the form (clicking radio buttons or stars) and can add a text comment in the "Other Suggestions" box. When finished, they click "Submit," which sends the results to the backend.
    *   **Teacher View**: The page also supports a "view" mode for teachers (`$_GET['type'] == 'view'`). In this mode, the form is disabled, and the teacher can select a student from a dropdown to see their submitted form.
    *   **AJAX Submission**: All data submissions (saving scores or checklist answers) are handled via AJAX calls to `prodb_learning_regulated_fillout.php` (actions: `save_score`, `save_check`).

4.  **State Management**:
    *   The page makes extensive use of JavaScript and jQuery to manage the UI state, such as switching between target students/groups, highlighting selected scores, and showing/hiding the correct buttons.
    *   It also fetches and displays previously submitted answers, disabling the form if the student has already completed it.

### Conclusion:

`learning_regulated_fillout.php` is the student-facing counterpart to the checklist creation tool. It's a highly interactive and versatile page that can render many different kinds of assessment forms. Its primary role is to present the form defined by the teacher and capture the student's self-assessment or peer-review data, which is crucial for the self-regulated learning process.
