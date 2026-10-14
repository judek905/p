# MySQL Academy

A static, single-page website that teaches MySQL step by step, with a practice database you can install locally. The site has six lessons, a cheat sheet of common statements, copy-and-highlight code examples, and an FAQ.

## Files

| File | Purpose |
|------|---------|
| `index.html` | The page: header, hero, about, six lessons, cheat sheet, practice section, FAQ, and footer. Its behaviour (mobile menu, active section highlighting, SQL highlighting, copy buttons) is in an inline `<script>` at the bottom of the file. |
| `styles.css` | All styling, in 14 numbered sections: design tokens, base, buttons, header, hero, about, lessons, code blocks, cheat sheet, practice, FAQ, closing band, footer, and responsive rules. |
| `academy.sql` | The practice database script. It creates `academy_db` with four related tables, three indexes, and sample data, followed by a query that checks the setup. |

```
mysql-academy/
├── index.html
├── styles.css
└── academy.sql
```

## Running the site

1. Keep all three files in the same folder. The page loads `styles.css` and links to `academy.sql` by relative path, so moving one file breaks the link.
2. Open `index.html` in any modern browser. No build step or server is needed.

The page loads fonts from Google Fonts. Without an internet connection, it falls back to Georgia and system fonts, so the layout still works but looks different.

## Installing the practice database

You need MySQL 8.0.16 or later. The script enforces `CHECK` constraints, which older versions ignore.

From a terminal, run:

```bash
mysql -u root -p < academy.sql
```

Enter your root password when prompted. Then open the database:

```bash
mysql -u root -p academy_db
```

> **Warning:** the script runs `DROP DATABASE IF EXISTS academy_db`. Running it again erases any changes you have made to that database. Don't run it against data you need to keep.

To confirm the setup, run the query at the end of the script, or:

```sql
SELECT 'instructors' AS table_name, COUNT(*) AS row_count FROM instructors
UNION ALL
SELECT 'students',    COUNT(*) FROM students
UNION ALL
SELECT 'courses',     COUNT(*) FROM courses
UNION ALL
SELECT 'enrollments', COUNT(*) FROM enrollments;
```

You should see **4, 10, 7 and 18** rows.

## The practice database

`academy_db` is a small school dataset used throughout the lessons.

| Table | Rows | Contents | Keys and constraints |
|-------|-----:|----------|----------------------|
| `instructors` | 4 | Teachers who create courses | Primary key `instructor_id`; unique `email`; `salary` must be zero or more |
| `students` | 10 | People enrolled in courses | Primary key `student_id`; unique `email` |
| `courses` | 7 | Course catalogue with price, category and publish status | Primary key `course_id`; foreign key `instructor_id` → `instructors`; `price` must be zero or more |
| `enrollments` | 18 | Links students to courses, with a grade and status (`active`, `completed` or `dropped`) | Primary key `enrollment_id`; foreign keys `student_id` and `course_id` (both `ON DELETE CASCADE`); each student can enrol in a course only once |

The script also creates three indexes: `idx_students_city`, `idx_courses_category`, and `idx_enrollments_course`.

## Page structure

`index.html` contains these sections, in order. The navigation links point to the anchors in the second column.

| Section | Anchor | Content |
|---------|--------|---------|
| Header | `#top` | Site name, navigation (About, Lessons, Cheat sheet, Practice, FAQ) |
| Hero | `#top` | Headline, call-to-action buttons, and a sample `mysql` command-line output |
| About | `#about` | What MySQL is and how it relates to SQL, with four facts |
| Lessons | `#lessons` | Six lessons, from beginner to intermediate, with topic tags |
| Cheat sheet | `#cheatsheet` | Eight common statements with copy buttons: `SELECT`, `WHERE`, `ORDER BY`/`LIMIT`, `INSERT`, `UPDATE`, `DELETE`, `JOIN`, and `GROUP BY`/`HAVING` |
| Practice | `#practice` | Three setup steps, a table layout reference, and six challenges |
| FAQ | `#faq` | Five collapsible questions |
| Closing | — | A call to start Lesson 01 |
| Footer | — | Links, copyright, and a trademark notice |

## Features

- **Responsive layout:** the layout adapts at 1000px, 760px and 480px. Below 760px, the navigation collapses into a menu button.
- **Copy buttons:** each code block has a Copy button. It uses the Clipboard API where available and falls back to `execCommand` on older browsers or non-HTTPS pages.
- **SQL highlighting:** code blocks marked `language-sql` are coloured by a small tokenizer that handles keywords, strings, numbers and comments. No libraries are used.
- **Active section in the menu:** the navigation link for the section in view is highlighted as you scroll.
- **Accessibility:** a skip link, visible focus outlines, labelled buttons, and `prefers-reduced-motion` support that turns off transitions.

## Customising

- **Colours:** change the values in the `:root` block at the top of `styles.css`. The palette uses `--paper`, `--ink`, `--teal`, `--marigold` and related tokens.
- **Fonts:** the Google Fonts link is in the `<head>` of `index.html`. Fallback stacks are set in `--font-serif`, `--font-sans` and `--font-mono`.
- **Content:** each lesson, cheat-sheet item and FAQ entry is a plain HTML block in `index.html`. Add a block by copying an existing one.
- **Practice data:** edit the `INSERT` statements in `academy.sql`. Keep the row counts in the check query and in the "Check the data" step of the page in sync with the data.

## Notes

- MySQL is a trademark of Oracle and/or its affiliates. This project is an independent learning resource and is not affiliated with Oracle.
- The inline script in `index.html` is the only JavaScript in the project. Moving it to a separate `script.js` file is a simple change if you want to keep the HTML and JavaScript apart.
