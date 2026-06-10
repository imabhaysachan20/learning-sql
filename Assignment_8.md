### Moving Average
A. Running total

SELECT
    student_id,
    name,
    branch,
    exam_date,
    marks,
    SUM(marks) OVER (
        PARTITION BY student_id
        ORDER BY exam_date
        ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
    ) AS cumulative_marks
FROM student_marks
ORDER BY student_id, exam_date;

B. Moving average

SELECT
    student_id,
    name,
    branch,
    exam_date,
    marks,
    AVG(marks) OVER (
        PARTITION BY student_id
        ORDER BY exam_date
        ROWS BETWEEN 1 PRECEDING AND CURRENT ROW
    ) AS moving_avg_2
FROM student_marks
ORDER BY student_id, exam_date;

