# Challenge: Symmetric Pairs

* **Source:** HackerRank
* **Difficulty:** Medium
* **Link:** [Symmetric Pairs](https://www.hackerrank.com/challenges/symmetric-pairs/problem?isFullScreen=true)

---
## ❓ Problem Statement

You are given a table, `Functions`, containing two columns: `X` and `Y`. Two pairs `(X1, Y1)` and `(X2, Y2)` are said to be symmetric pairs if `X1 = Y2` and `X2 = Y1`.

Write a query to output all such symmetric pairs in ascending order by the value of `X`. List the rows such that `X1 <= Y1`.

---
## 📊 Dataset Schema

**Table: `Functions`**

| Column | Type |
| --- | --- |
| X | Integer |
| Y | Integer |

---
## 🤔 My Thought Process

1.  The core task is to find rows that have a "mirror image" in the same table. For example, if a row `(5, 10)` exists, we must check if the row `(10, 5)` also exists.
2.  The standard way to compare a table to itself is using a **`SELF JOIN`**. I'll create two aliases for the `Functions` table, `F1` and `F2`.
3.  The `JOIN` condition will be the definition of symmetry provided in the problem: `F1.X = F2.Y` AND `F1.Y = F2.X`.
4.  This join creates two problems:
    * It will find both `(5, 10)` and `(10, 5)`, but we only want one. The rule `X1 <= Y1` is given to solve this, but applying it as `WHERE F1.X <= F1.Y` is tricky.
    * A single row where `X = Y` (e.g., `(8, 8)`) will join with itself. This is not a "pair." A pair of `(8, 8)`s is only symmetric if there are *two* `(8, 8)` rows in the table.
5.  The most robust way to solve both problems is to use **`GROUP BY`** and **`HAVING`**.
6.  I'll `GROUP BY F1.X, F1.Y` to collapse all the rows found by the join.
7.  I'll then use a **`HAVING`** clause with two conditions:
    * `COUNT(F1.X) > 1`: This will be true for symmetric pairs where `X = Y` (like `(8, 8)`) because they will find multiple join matches.
    * `F1.X < F1.Y`: This will be true for pairs like `(5, 10)` (and will exclude the `(10, 5)` mirror).
8.  Combining these with `OR` (`HAVING COUNT(F1.X) > 1 OR F1.X < F1.Y`) correctly filters for both cases.
9.  Finally, I'll `ORDER BY F1.X` as requested.

---
## 💻 SQL Solution

```sql
SELECT F1.X, F1.Y
FROM
    Functions AS F1
JOIN
    Functions AS F2 ON F1.X = F2.Y AND F1.Y = F2.X
GROUP BY
    F1.X, F1.Y
HAVING
    -- This handles pairs where X = Y; they must appear more than once
    COUNT(F1.X) > 1
    -- This handles pairs where X != Y, selecting only the one where X < Y
    OR F1.X < F1.Y
ORDER BY
    F1.X;
