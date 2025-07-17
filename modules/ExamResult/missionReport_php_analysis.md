# ExamResult/missionReport.php Analysis

This file generates a powerful and complex **class-level mission report** for teachers. Its primary purpose is to display a grid comparing the performance of all students in a class across multiple mission attempts.

### Key Functionality:

1.  **Report Generation Interface**:
    *   **Filtering**: The page provides a comprehensive set of filters for teachers to select the exact data they want to see. They can filter by:
        *   Academic Year and Semester
        *   Class Type (Normal, Remedial, Custom)
        *   Specific Class
        *   Assigning Teacher
    *   **Comparison Mode**: A key feature is the ability to compare a student's latest attempt with either their **first** attempt or their **previous** attempt. This allows teachers to easily track progress and improvement over time.
    *   **AJAX-Driven**: The entire report is generated dynamically. After the teacher selects their filters and clicks "Query Report," an AJAX call is made to `prodb_missionreport.php` to fetch the necessary data.

2.  **Data Display Grid**:
    *   The main feature is a large table where:
        *   **Rows** represent individual students in the selected class.
        *   **Columns** represent the different missions that have been assigned.
    *   **Cell Content**: Each cell in the grid is rich with information, showing:
        *   The student's score on their latest attempt (e.g., "85%").
        *   A performance comparison indicator:
            *   An **up arrow** (green) if the score improved.
            *   A **down arrow** (red) if the score decreased.
            *   A **minus icon** (yellow) if the score was the same.
        *   The percentage change in score.
        *   A link to view the detailed diagnostic report for that specific attempt.

3.  **Backend Communication**:
    *   The file heavily relies on `prodb_missionreport.php` (which I will analyze next) to do the heavy lifting. This frontend file is responsible for building the UI and making the initial AJAX request. The backend script then fetches all the student records, compares the scores from different attempts, and returns the structured data needed to build the grid.

4.  **Role-Based View**:
    *   The code checks if the user is a teacher. While the current implementation seems focused on teachers, this check suggests that other roles (like students) might see a simplified version of this page, likely showing only their own report data.

### Conclusion:

`missionReport.php` is a sophisticated data visualization tool for teachers. It provides a high-level overview of class performance across multiple assignments and attempts, with a powerful comparison feature that makes it easy to track student growth. It follows the same pattern as the dashboard, where the PHP file sets up the view and the core logic is handled by a separate backend processor (`prodb_missionreport.php`) and client-side JavaScript.
