# Challenge: Report Students' Grades

* **Source:** HackerRank
* **Difficulty:** Medium
* **Link:** [The Report](https://www.hackerrank.com/challenges/the-report/problem?isFullScreen=true)

---

## ❓ Problem Statement

The goal is to generate a report containing three columns: Name, Grade and Mark. We don't want the NAMES of those students who received a grade lower than 8. The report must be in descending order by grade -- i.e. higher grades are entered first. If there is more than one student with the same grade (8-10) assigned to them, order those particular students by their name alphabetically. Finally, if the grade is lower than 8, use "NULL" as their name and list them by their grades in descending order. If there is more than one student with the same grade (1-7) assigned to them, order those particular students by their marks in ascending order.

---

## 📊 Dataset Schema

**Table: `Students`**

| Column | Type |
| --- | --- |
| ID | Integer |
| Name | String |
| Marks | Integer |

**Table: `Grades`**

| Column | Type |
| --- | --- |
| Grade | Integer |
| Min_Mark | Integer |
| Max_Mark | Integer |

---

## 🤔 My Thought Process

1.  The core of the problem is to assign a `Grade` to each `Student` based on their `Marks`. This requires joining the `Students` table with the `Grades` table.
2.  The join condition won't be a direct equality (`=`). Instead, I need to check if a student's `Marks` fall *between* the `Min_Mark` and `Max_Mark` for a given grade. The `BETWEEN` operator is perfect for this.
3.  The main conditional logic is for the `Name`. I need to display the name only if the grade is 8 or higher. A `CASE` statement is the ideal tool for this, as it's highly readable and standard across SQL dialects.
4.  Finally, the result needs to be sorted. The primary sorting key is `Grade` (descending); the secondary sorting key is `Name` (ascending); the third sorting key is `Mark` (ascending). This requires an `ORDER BY` clause with three conditions.

---

## 💻 SQL Solution

```sql
SELECT
    CASE
        WHEN g.Grade < 8  THEN NULL     # not display the names of students whose grades < 8
        ELSE s.Name                       # display the names of students whose grades >= 8
    END AS Name,
    g.Grade,
    s.Marks
FROM
    Students s
LEFT JOIN
    Grades g
ON
    s.Marks BETWEEN g.Min_Mark AND g.Max_Mark
ORDER BY
    g.Grade DESC,Name ASC, s.Marks ASC;
```
---

## ✅ Key Takeaways

Concepts Practiced: `JOIN` with a non-equi condition (`BETWEEN`), `CASE` statements for conditional logic, and multi-level `ORDER BY`.

Key Learning: This problem was a great exercise in using `JOIN` on a range rather than a direct key match. It also highlights the power of `CASE` statements for creating conditional columns in the final output.
