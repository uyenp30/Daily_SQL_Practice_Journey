# Challenge: Winners of Coding Contest

* **Source:** HackerRank
* **Difficulty:** Medium
* **Link:** [The Competitors](https://www.hackerrank.com/challenges/full-score/problem?isFullScreen=true)

---

## ❓ Problem Statement

The goal is to identify hackers who achieved a full score on more than one challenge. The final report should print the `hacker_id` and `name` of these hackers, ordered first by the total number of full-score challenges they completed (in descending order), and then by their `hacker_id` (in ascending order) to break any ties.

---

## 📊 Dataset Schema

**Table: `Hackers`**

| Column | Type |
| --- | --- |
| hacker_id | Integer |
| name | String |

**Table: `Challenges`**

| Column | Type |
| --- | --- |
| challenge_id | Integer |
| hacker_id | Integer |
| difficulty_level | Integer |

**Table: `Difficulty`**

| Column | Type |
| --- | --- |
| difficulty_level | Integer |
| score | Integer |

**Table: `Submissions`**

| Column | Type |
| --- | --- |
| submission_id | Integer |
| hacker_id | Integer |
| challenge_id | Integer |
| score | Integer |

---

## 🤔 My Thought Process

1. The first step is to define what a "full score" is. This requires comparing a submission's actual score with the maximum possible score for that challenge's difficulty level.

2. This means I need data from four tables. The logic requires a multi-table **JOIN**: `Submissions` -> `Challenges` (to find the difficulty level) -> `Difficulty` (to find the max score for that level). I also need to join to `Hackers` to get the hacker's `name`.

3. The core filtering logic is to isolate only the full-score submissions. This is a condition on individual rows, making it a perfect use case for the **WHERE** clause: **WHERE** `Submissions.score` = `Difficulty.score`.

4. The problem asks for a count of challenges per hacker. The phrase "per hacker" is a clear signal that I need to **GROUP BY** `hacker_id` and `name`.

5. The condition "more than one challenge" is a filter that applies to the groups created by the **GROUP BY** clause. This means I must use the **HAVING** clause: `HAVING COUNT(...) > 1`.

6. Finally, the two-level sorting requirement is handled by the **ORDER BY** clause.

---

## 💻 SQL Solution

```sql
SELECT
    H.hacker_id,
    H.name
FROM
    Submissions AS S
JOIN
    Challenges AS C ON S.challenge_id = C.challenge_id
JOIN
    Difficulty AS D ON C.difficulty_level = D.difficulty_level
JOIN
    Hackers AS H ON S.hacker_id = H.hacker_id
WHERE
    S.score = D.score
GROUP BY
    H.hacker_id,
    H.name
HAVING
    COUNT(C.challenge_id) > 1
ORDER BY
    COUNT(C.challenge_id) DESC,
    H.hacker_id ASC;
```
---

## ✅ Key Takeaways

Concepts Practiced: Multi-table **JOIN**s, **GROUP BY**, aggregate functions (**COUNT**), and multi-level **ORDER BY**.

Key Learning: This problem is a classic exercise in understanding the critical difference between the **WHERE** clause and the **HAVING** clause. **WHERE** filters individual rows before aggregation, while **HAVING** filters entire groups after aggregation.
