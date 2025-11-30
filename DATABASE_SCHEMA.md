# DATABASE SCHEMA - EXAM SCHEDULING SYSTEM

**Project:** Exam Planner Desktop Application
**Database:** SQLite 3.44
**Version:** 1.0
**Last Updated:** November 26, 2025

---

## TABLE OF CONTENTS

1. [Overview](#overview)
2. [Entity-Relationship Diagram](#entity-relationship-diagram)
3. [Database Tables](#database-tables)
4. [Relationships](#relationships)
5. [Indexes](#indexes)
6. [Sample Data](#sample-data)
7. [SQL Schema Script](#sql-schema-script)
8. [Common Queries](#common-queries)
9. [Database Initialization](#database-initialization)
10. [Best Practices](#best-practices)

---

## OVERVIEW

### Database Purpose

The database stores all data required for the exam scheduling system:
- **Student records** - All students who will take exams
- **Course catalog** - All courses that need exams scheduled
- **Classroom inventory** - Available classrooms with capacities
- **Enrollment data** - Which students are enrolled in which courses
- **Generated schedules** - Complete exam schedules with metadata
- **Exam assignments** - Individual course-to-classroom-time assignments

### Database Technology

**SQLite 3.44** - Chosen for:
- **Zero configuration** - No server setup required
- **Embedded** - Single file database
- **Cross-platform** - Works on Windows, macOS, Linux
- **ACID compliant** - Full transaction support
- **Lightweight** - Perfect for desktop applications
- **Easy backup** - Just copy the .db file

### Database File Location

- **Development:** `./data/exam_scheduler.db`
- **Production:** User's Documents folder or application data directory
- **Backup:** Automatic export to CSV on schedule save

---

## ENTITY-RELATIONSHIP DIAGRAM

### High-Level ER Diagram

```
┌─────────────┐         ┌──────────────┐         ┌──────────────┐
│   Student   │         │  Enrollment  │         │    Course    │
├─────────────┤         ├──────────────┤         ├──────────────┤
│ student_id  │◄───────►│ enrollment_id│◄───────►│ course_code  │
│ created_at  │   M:N   │ student_id   │   M:N   │ created_at   │
└─────────────┘         │ course_code  │         └──────┬───────┘
                        │ created_at   │                │
                        └──────────────┘                │ 1:M
                                                        │
                                                        ▼
┌─────────────┐         ┌──────────────────────────────────────┐
│  Classroom  │         │         ExamAssignment               │
├─────────────┤         ├──────────────────────────────────────┤
│classroom_id │◄────────│ exam_id                              │
│ capacity    │   1:M   │ schedule_id      (FK)                │
│ created_at  │         │ course_code      (FK)                │
└─────────────┘         │ classroom_id     (FK)                │
                        │ day                                  │
                        │ time_slot                            │
                        │ created_at                           │
                        └──────────────┬───────────────────────┘
                                       │ M:1
                                       ▼
                        ┌──────────────────────────────────────┐
                        │           Schedule                   │
                        ├──────────────────────────────────────┤
                        │ schedule_id                          │
                        │ created_date                         │
                        │ optimization_strategy                │
                        │ num_days                             │
                        │ slots_per_day                        │
                        │ status                               │
                        └──────────────────────────────────────┘
```

### Relationship Summary

| Relationship | Type | Description |
|-------------|------|-------------|
| Student ↔ Enrollment | 1:M | One student has many enrollments |
| Course ↔ Enrollment | 1:M | One course has many enrollments |
| Student ↔ Course | M:N | Many-to-many through Enrollment |
| Course ↔ ExamAssignment | 1:M | One course can have multiple assignments (across schedules) |
| Classroom ↔ ExamAssignment | 1:M | One classroom hosts many exams |
| Schedule ↔ ExamAssignment | 1:M | One schedule contains many exam assignments |

---

## DATABASE TABLES

### 1. students

**Purpose:** Store all student records who will participate in exams.

**Table Definition:**

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| student_id | TEXT | PRIMARY KEY, NOT NULL | Unique student identifier (e.g., "Std_ID_001") |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Record creation timestamp |

**Business Rules:**
- Student IDs must be unique
- Student IDs are imported from CSV
- Cannot be deleted if enrollments exist (protected by foreign key)

**Sample Data:**
```sql
INSERT INTO students (student_id) VALUES
('Std_ID_001'),
('Std_ID_002'),
('Std_ID_003');
```

**Expected Size:** 250-500 students (sample data has 250)

---

### 2. courses

**Purpose:** Store all courses that require exam scheduling.

**Table Definition:**

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| course_code | TEXT | PRIMARY KEY, NOT NULL | Unique course identifier (e.g., "CourseCode_01") |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Record creation timestamp |

**Business Rules:**
- Course codes must be unique
- Course codes are imported from CSV
- Cannot be deleted if enrollments exist (protected by foreign key)

**Sample Data:**
```sql
INSERT INTO courses (course_code) VALUES
('CourseCode_01'),
('CourseCode_02'),
('CourseCode_03');
```

**Expected Size:** 20-50 courses (sample data has 20)

---

### 3. classrooms

**Purpose:** Store available classrooms with their seating capacities.

**Table Definition:**

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| classroom_id | TEXT | PRIMARY KEY, NOT NULL | Unique classroom identifier (e.g., "Classroom_01") |
| capacity | INTEGER | NOT NULL, CHECK(capacity > 0) | Maximum seating capacity |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Record creation timestamp |

**Business Rules:**
- Classroom IDs must be unique
- Capacity must be positive integer
- Imported from CSV format: `ClassroomID;Capacity`
- Cannot be deleted if used in exam assignments (protected by foreign key)

**Sample Data:**
```sql
INSERT INTO classrooms (classroom_id, capacity) VALUES
('Classroom_01', 40),
('Classroom_02', 35),
('Classroom_03', 50);
```

**Expected Size:** 10-20 classrooms (sample data has 10, all capacity 40)

---

### 4. enrollments

**Purpose:** Many-to-many relationship between students and courses (which students are enrolled in which courses).

**Table Definition:**

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| enrollment_id | INTEGER | PRIMARY KEY AUTOINCREMENT | Auto-generated unique ID |
| student_id | TEXT | NOT NULL, FK → students(student_id) | Reference to student |
| course_code | TEXT | NOT NULL, FK → courses(course_code) | Reference to course |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Record creation timestamp |
| UNIQUE | | (student_id, course_code) | Prevent duplicate enrollments |

**Indexes:**
```sql
CREATE INDEX idx_enrollments_student ON enrollments(student_id);
CREATE INDEX idx_enrollments_course ON enrollments(course_code);
```

**Business Rules:**
- Each student-course pair can only appear once
- Cannot enroll student in non-existent course
- Cannot enroll non-existent student
- Imported from attendance list CSV

**Sample Data:**
```sql
INSERT INTO enrollments (student_id, course_code) VALUES
('Std_ID_001', 'CourseCode_01'),
('Std_ID_001', 'CourseCode_05'),
('Std_ID_002', 'CourseCode_01'),
('Std_ID_002', 'CourseCode_03');
```

**Expected Size:** 800-10,000 records (sample: ~800 enrollments, ~40 students per course)

**Cascade Behavior:**
- If student deleted → all enrollments deleted (CASCADE)
- If course deleted → all enrollments deleted (CASCADE)

---

### 5. schedules

**Purpose:** Store metadata for each generated exam schedule.

**Table Definition:**

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| schedule_id | INTEGER | PRIMARY KEY AUTOINCREMENT | Auto-generated unique ID |
| created_date | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP, NOT NULL | When schedule was generated |
| optimization_strategy | TEXT | NOT NULL | Strategy used (see values below) |
| num_days | INTEGER | NOT NULL, CHECK(num_days > 0) | Number of exam days configured |
| slots_per_day | INTEGER | NOT NULL, CHECK(slots_per_day > 0) | Number of time slots per day |
| status | TEXT | DEFAULT 'generated', CHECK(status IN (...)) | Schedule status (see values below) |

**Optimization Strategy Values:**
- `'minimize_days'` - Pack exams into fewest days
- `'balance_distribution'` - Spread exams evenly across days
- `'minimize_classrooms'` - Use fewest classrooms
- `'balance_classrooms'` - Balance classroom usage per day

**Status Values:**
- `'generated'` - Just created
- `'saved'` - User explicitly saved
- `'archived'` - Old schedule kept for history

**Business Rules:**
- num_days and slots_per_day must be positive integers
- Total slots (num_days × slots_per_day) should be ≥ number of courses
- Cannot delete schedule if it's the only one (application logic)

**Sample Data:**
```sql
INSERT INTO schedules (optimization_strategy, num_days, slots_per_day, status) VALUES
('balance_distribution', 5, 4, 'saved'),
('minimize_days', 5, 4, 'generated'),
('minimize_classrooms', 6, 3, 'archived');
```

**Expected Size:** 10-100 records (historical schedules kept for comparison)

---

### 6. exam_assignments

**Purpose:** Store individual exam assignments (which course exam is in which classroom at what time).

**Table Definition:**

| Column | Type | Constraints | Description |
|--------|------|-------------|-------------|
| exam_id | INTEGER | PRIMARY KEY AUTOINCREMENT | Auto-generated unique ID |
| schedule_id | INTEGER | NOT NULL, FK → schedules(schedule_id) | Which schedule this belongs to |
| course_code | TEXT | NOT NULL, FK → courses(course_code) | Which course exam |
| classroom_id | TEXT | NOT NULL, FK → classrooms(classroom_id) | Which classroom |
| day | INTEGER | NOT NULL, CHECK(day > 0) | Which day (1, 2, 3, ...) |
| time_slot | INTEGER | NOT NULL, CHECK(time_slot > 0) | Which time slot (1, 2, 3, ...) |
| created_at | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | Record creation timestamp |
| UNIQUE | | (schedule_id, course_code) | Each course once per schedule |
| UNIQUE | | (schedule_id, classroom_id, day, time_slot) | No classroom double-booking |

**Indexes:**
```sql
CREATE INDEX idx_assignments_schedule ON exam_assignments(schedule_id);
CREATE INDEX idx_assignments_course ON exam_assignments(course_code);
CREATE INDEX idx_assignments_classroom_time ON exam_assignments(classroom_id, day, time_slot);
```

**Business Rules:**
- Each course appears exactly once per schedule
- Each {classroom, day, time_slot} combination used at most once per schedule
- Day and time_slot must be positive integers
- day must be ≤ num_days from schedule
- time_slot must be ≤ slots_per_day from schedule (enforced by application)

**Sample Data:**
```sql
INSERT INTO exam_assignments (schedule_id, course_code, classroom_id, day, time_slot) VALUES
(1, 'CourseCode_01', 'Classroom_01', 1, 1),
(1, 'CourseCode_02', 'Classroom_02', 1, 1),
(1, 'CourseCode_03', 'Classroom_01', 1, 2),
(1, 'CourseCode_04', 'Classroom_03', 1, 3);
```

**Expected Size:** 20-50 assignments per schedule (one per course)

**Cascade Behavior:**
- If schedule deleted → all assignments deleted (CASCADE)
- If course deleted → all assignments deleted (CASCADE)
- If classroom deleted → all assignments deleted (CASCADE)

---

## RELATIONSHIPS

### Primary Key - Foreign Key Relationships

#### 1. students → enrollments (1:M)

```sql
FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE
```

**Meaning:** Each student can have many enrollments. If a student is deleted, all their enrollments are automatically deleted.

**Query Example:**
```sql
-- Get all courses for a student
SELECT c.course_code
FROM courses c
JOIN enrollments e ON c.course_code = e.course_code
WHERE e.student_id = 'Std_ID_001';
```

---

#### 2. courses → enrollments (1:M)

```sql
FOREIGN KEY (course_code) REFERENCES courses(course_code) ON DELETE CASCADE
```

**Meaning:** Each course can have many enrollments. If a course is deleted, all enrollments in that course are automatically deleted.

**Query Example:**
```sql
-- Get all students in a course
SELECT s.student_id
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
WHERE e.course_code = 'CourseCode_01';
```

---

#### 3. courses → exam_assignments (1:M)

```sql
FOREIGN KEY (course_code) REFERENCES courses(course_code) ON DELETE CASCADE
```

**Meaning:** Each course can have multiple exam assignments (across different schedules). If a course is deleted, all its exam assignments are deleted.

---

#### 4. classrooms → exam_assignments (1:M)

```sql
FOREIGN KEY (classroom_id) REFERENCES classrooms(classroom_id) ON DELETE CASCADE
```

**Meaning:** Each classroom can host many exams. If a classroom is deleted, all exam assignments using it are deleted.

---

#### 5. schedules → exam_assignments (1:M)

```sql
FOREIGN KEY (schedule_id) REFERENCES schedules(schedule_id) ON DELETE CASCADE
```

**Meaning:** Each schedule contains many exam assignments. If a schedule is deleted, all its assignments are automatically deleted.

---

## INDEXES

### Purpose of Indexes

Indexes speed up queries by creating a data structure that allows fast lookups. Critical for:
- JOIN operations
- WHERE clauses
- ORDER BY operations

### Index Definitions

#### 1. enrollments Indexes

```sql
CREATE INDEX idx_enrollments_student ON enrollments(student_id);
```
**Purpose:** Fast lookup of all courses for a student
**Used by:** Constraint validation (check if student has consecutive exams)

```sql
CREATE INDEX idx_enrollments_course ON enrollments(course_code);
```
**Purpose:** Fast lookup of all students in a course
**Used by:** Capacity validation, solver domain creation

---

#### 2. exam_assignments Indexes

```sql
CREATE INDEX idx_assignments_schedule ON exam_assignments(schedule_id);
```
**Purpose:** Fast retrieval of all assignments in a schedule
**Used by:** Loading schedules, display views

```sql
CREATE INDEX idx_assignments_course ON exam_assignments(course_code);
```
**Purpose:** Fast lookup of assignment for a specific course
**Used by:** Manual editing, validation

```sql
CREATE INDEX idx_assignments_classroom_time ON exam_assignments(classroom_id, day, time_slot);
```
**Purpose:** Fast check if classroom is available at specific time
**Used by:** Solver (check classroom availability), validation

---

### Index Performance

**Without indexes:**
- Query time for "students in course": O(n) - scan all enrollments
- Query time for "classroom availability": O(n) - scan all assignments

**With indexes:**
- Query time: O(log n) - binary search on index
- 10x-100x faster for typical dataset sizes

**Trade-off:**
- Indexes use disk space (~10-20% more)
- Inserts/updates slightly slower (must update indexes)
- For this application: Read-heavy, so indexes are beneficial

---

## SAMPLE DATA

### Sample Dataset Statistics

Based on provided sample data:

```
Students:     250 (Std_ID_001 to Std_ID_250)
Courses:      20  (CourseCode_01 to CourseCode_20)
Classrooms:   10  (Classroom_01 to Classroom_10)
Capacity:     40 seats per classroom (all classrooms)
Enrollments:  ~800 total (~40 students per course average)
```

### Sample Enrollment Distribution

```
CourseCode_01: 40 students
CourseCode_02: 40 students
CourseCode_03: 38 students
...
CourseCode_20: 42 students
```

**Characteristics:**
- Fairly balanced enrollment across courses
- No course exceeds max classroom capacity (40)
- Average student takes 3-4 courses
- Solvable with 5 days × 4 slots = 20 total slots

---

## SQL SCHEMA SCRIPT

### Complete Schema (schema.sql)

This script creates all tables with proper constraints, indexes, and foreign keys.

```sql
-- ============================================================
-- EXAM SCHEDULING SYSTEM - DATABASE SCHEMA
-- Database: SQLite 3.44
-- Version: 1.0
-- Created: November 26, 2025
-- ============================================================

-- Enable foreign key constraints (required for SQLite)
PRAGMA foreign_keys = ON;

-- ============================================================
-- TABLE: students
-- Purpose: Store all student records
-- ============================================================

CREATE TABLE IF NOT EXISTS students (
    student_id TEXT PRIMARY KEY NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- ============================================================
-- TABLE: courses
-- Purpose: Store all course records
-- ============================================================

CREATE TABLE IF NOT EXISTS courses (
    course_code TEXT PRIMARY KEY NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- ============================================================
-- TABLE: classrooms
-- Purpose: Store classroom information with capacities
-- ============================================================

CREATE TABLE IF NOT EXISTS classrooms (
    classroom_id TEXT PRIMARY KEY NOT NULL,
    capacity INTEGER NOT NULL CHECK(capacity > 0),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- ============================================================
-- TABLE: enrollments
-- Purpose: Many-to-many relationship between students and courses
-- ============================================================

CREATE TABLE IF NOT EXISTS enrollments (
    enrollment_id INTEGER PRIMARY KEY AUTOINCREMENT,
    student_id TEXT NOT NULL,
    course_code TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
    FOREIGN KEY (course_code) REFERENCES courses(course_code) ON DELETE CASCADE,

    UNIQUE(student_id, course_code)
);

-- Indexes for enrollments (for fast lookups)
CREATE INDEX IF NOT EXISTS idx_enrollments_student ON enrollments(student_id);
CREATE INDEX IF NOT EXISTS idx_enrollments_course ON enrollments(course_code);

-- ============================================================
-- TABLE: schedules
-- Purpose: Store metadata for generated exam schedules
-- ============================================================

CREATE TABLE IF NOT EXISTS schedules (
    schedule_id INTEGER PRIMARY KEY AUTOINCREMENT,
    created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL,
    optimization_strategy TEXT NOT NULL CHECK(
        optimization_strategy IN (
            'minimize_days',
            'balance_distribution',
            'minimize_classrooms',
            'balance_classrooms'
        )
    ),
    num_days INTEGER NOT NULL CHECK(num_days > 0),
    slots_per_day INTEGER NOT NULL CHECK(slots_per_day > 0),
    status TEXT DEFAULT 'generated' CHECK(
        status IN ('generated', 'saved', 'archived')
    )
);

-- ============================================================
-- TABLE: exam_assignments
-- Purpose: Store individual exam assignments
-- ============================================================

CREATE TABLE IF NOT EXISTS exam_assignments (
    exam_id INTEGER PRIMARY KEY AUTOINCREMENT,
    schedule_id INTEGER NOT NULL,
    course_code TEXT NOT NULL,
    classroom_id TEXT NOT NULL,
    day INTEGER NOT NULL CHECK(day > 0),
    time_slot INTEGER NOT NULL CHECK(time_slot > 0),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,

    FOREIGN KEY (schedule_id) REFERENCES schedules(schedule_id) ON DELETE CASCADE,
    FOREIGN KEY (course_code) REFERENCES courses(course_code) ON DELETE CASCADE,
    FOREIGN KEY (classroom_id) REFERENCES classrooms(classroom_id) ON DELETE CASCADE,

    -- Each course appears once per schedule
    UNIQUE(schedule_id, course_code),

    -- No classroom double-booking
    UNIQUE(schedule_id, classroom_id, day, time_slot)
);

-- Indexes for exam_assignments (for fast queries)
CREATE INDEX IF NOT EXISTS idx_assignments_schedule ON exam_assignments(schedule_id);
CREATE INDEX IF NOT EXISTS idx_assignments_course ON exam_assignments(course_code);
CREATE INDEX IF NOT EXISTS idx_assignments_classroom_time ON exam_assignments(classroom_id, day, time_slot);

-- ============================================================
-- END OF SCHEMA
-- ============================================================
```

---

## COMMON QUERIES

### Query 1: Get All Courses for a Student

```sql
SELECT c.course_code
FROM courses c
JOIN enrollments e ON c.course_code = e.course_code
WHERE e.student_id = ?
ORDER BY c.course_code;
```

**Parameters:** `student_id` (e.g., 'Std_ID_001')

**Returns:** List of course codes the student is enrolled in

**Usage:** Constraint validation, student view display

---

### Query 2: Get All Students in a Course

```sql
SELECT s.student_id
FROM students s
JOIN enrollments e ON s.student_id = e.student_id
WHERE e.course_code = ?
ORDER BY s.student_id;
```

**Parameters:** `course_code` (e.g., 'CourseCode_01')

**Returns:** List of student IDs enrolled in the course

**Usage:** Capacity validation, solver domain creation

---

### Query 3: Get Enrollment Count for a Course

```sql
SELECT COUNT(*) as enrollment_count
FROM enrollments
WHERE course_code = ?;
```

**Parameters:** `course_code`

**Returns:** Number of students enrolled

**Usage:** Capacity validation (must be ≤ classroom capacity)

---

### Query 4: Get All Exam Assignments for a Schedule

```sql
SELECT
    ea.course_code,
    ea.classroom_id,
    ea.day,
    ea.time_slot,
    cl.capacity,
    COUNT(e.student_id) as enrolled_count
FROM exam_assignments ea
JOIN classrooms cl ON ea.classroom_id = cl.classroom_id
LEFT JOIN enrollments e ON ea.course_code = e.course_code
WHERE ea.schedule_id = ?
GROUP BY ea.exam_id
ORDER BY ea.day, ea.time_slot, ea.classroom_id;
```

**Parameters:** `schedule_id`

**Returns:** Complete schedule with capacities and enrollment counts

**Usage:** Display all views, validation

---

### Query 5: Get Student's Personal Exam Schedule

```sql
SELECT
    c.course_code,
    ea.day,
    ea.time_slot,
    ea.classroom_id
FROM courses c
JOIN enrollments e ON c.course_code = e.course_code
JOIN exam_assignments ea ON c.course_code = ea.course_code
WHERE e.student_id = ?
  AND ea.schedule_id = ?
ORDER BY ea.day, ea.time_slot;
```

**Parameters:** `student_id`, `schedule_id`

**Returns:** Student's personal exam schedule

**Usage:** Student view display

---

### Query 6: Check if Classroom is Available

```sql
SELECT COUNT(*) as is_occupied
FROM exam_assignments
WHERE schedule_id = ?
  AND classroom_id = ?
  AND day = ?
  AND time_slot = ?;
```

**Parameters:** `schedule_id`, `classroom_id`, `day`, `time_slot`

**Returns:** 0 if available, 1 if occupied

**Usage:** Solver domain filtering, validation

---

### Query 7: Get Exams for Specific Day and Time Slot

```sql
SELECT
    ea.course_code,
    ea.classroom_id,
    COUNT(e.student_id) as student_count
FROM exam_assignments ea
LEFT JOIN enrollments e ON ea.course_code = e.course_code
WHERE ea.schedule_id = ?
  AND ea.day = ?
  AND ea.time_slot = ?
GROUP BY ea.exam_id
ORDER BY ea.classroom_id;
```

**Parameters:** `schedule_id`, `day`, `time_slot`

**Returns:** All exams in that time slot

**Usage:** Day view display

---

### Query 8: Get All Schedules (History)

```sql
SELECT
    s.schedule_id,
    s.created_date,
    s.optimization_strategy,
    s.num_days,
    s.slots_per_day,
    s.status,
    COUNT(DISTINCT ea.course_code) as course_count,
    COUNT(DISTINCT ea.classroom_id) as classroom_count
FROM schedules s
LEFT JOIN exam_assignments ea ON s.schedule_id = ea.schedule_id
GROUP BY s.schedule_id
ORDER BY s.created_date DESC;
```

**Returns:** All schedules with metadata and statistics

**Usage:** Schedule history view

---

### Query 9: Check for Student Conflicts (Consecutive Exams)

```sql
-- Check if student has exam in previous slot
SELECT COUNT(*) as has_conflict
FROM enrollments e1
JOIN exam_assignments ea1 ON e1.course_code = ea1.course_code
WHERE e1.student_id = ?
  AND ea1.schedule_id = ?
  AND (
      -- Check previous slot same day
      (ea1.day = ? AND ea1.time_slot = ? - 1)
      OR
      -- Check last slot of previous day
      (ea1.day = ? - 1 AND ea1.time_slot = (
          SELECT slots_per_day FROM schedules WHERE schedule_id = ?
      ))
  );
```

**Parameters:** `student_id`, `schedule_id`, `day`, `time_slot`

**Returns:** Count of conflicts (should be 0)

**Usage:** Constraint validation

---

### Query 10: Delete Schedule and All Assignments

```sql
-- Due to CASCADE, this automatically deletes all exam_assignments
DELETE FROM schedules WHERE schedule_id = ?;
```

**Parameters:** `schedule_id`

**Effect:** Deletes schedule and all associated assignments

**Usage:** Schedule deletion

---

## DATABASE INITIALIZATION

### Step 1: Create Database File

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

public class DatabaseInitializer {

    private static final String DB_URL = "jdbc:sqlite:./data/exam_scheduler.db";

    public static void initializeDatabase() throws SQLException {
        // Create data directory if it doesn't exist
        new File("./data").mkdirs();

        // Connect to database (creates file if doesn't exist)
        try (Connection conn = DriverManager.getConnection(DB_URL);
             Statement stmt = conn.createStatement()) {

            // Enable foreign keys
            stmt.execute("PRAGMA foreign_keys = ON;");

            // Read schema.sql and execute
            String schema = readFile("src/main/resources/database/schema.sql");
            stmt.executeUpdate(schema);

            System.out.println("Database initialized successfully!");
        }
    }
}
```

---

### Step 2: Verify Schema

```sql
-- Check all tables exist
SELECT name FROM sqlite_master WHERE type='table';

-- Expected output:
-- students
-- courses
-- classrooms
-- enrollments
-- schedules
-- exam_assignments

-- Check indexes exist
SELECT name FROM sqlite_master WHERE type='index';

-- Check foreign keys are enabled
PRAGMA foreign_keys;
-- Should return: 1
```

---

### Step 3: Import Sample Data

```java
public void importSampleData() throws SQLException {
    try (Connection conn = DriverManager.getConnection(DB_URL)) {
        conn.setAutoCommit(false); // Start transaction

        try {
            importStudents(conn, "data/sample/students.csv");
            importCourses(conn, "data/sample/courses.csv");
            importClassrooms(conn, "data/sample/classrooms.csv");
            importEnrollments(conn, "data/sample/enrollments.csv");

            conn.commit(); // Commit transaction
            System.out.println("Sample data imported successfully!");
        } catch (Exception e) {
            conn.rollback(); // Rollback on error
            throw e;
        }
    }
}
```

---

## BEST PRACTICES

### 1. Always Use Transactions for Multiple Operations

```java
try (Connection conn = getConnection()) {
    conn.setAutoCommit(false);

    try {
        // Multiple inserts/updates
        insertStudent(conn, student1);
        insertStudent(conn, student2);
        insertEnrollment(conn, enrollment1);

        conn.commit(); // Success - commit all
    } catch (Exception e) {
        conn.rollback(); // Error - rollback all
        throw e;
    }
}
```

**Why:** Ensures data consistency. If any operation fails, all are rolled back.

---

### 2. Use Prepared Statements (Prevent SQL Injection)

**❌ BAD (Vulnerable to SQL injection):**
```java
String sql = "SELECT * FROM students WHERE student_id = '" + studentId + "'";
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery(sql);
```

**✅ GOOD (Safe):**
```java
String sql = "SELECT * FROM students WHERE student_id = ?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setString(1, studentId);
ResultSet rs = pstmt.executeQuery();
```

---

### 3. Close Resources Properly (Use Try-With-Resources)

**❌ BAD:**
```java
Connection conn = DriverManager.getConnection(DB_URL);
Statement stmt = conn.createStatement();
ResultSet rs = stmt.executeQuery(sql);
// If exception occurs, resources not closed!
```

**✅ GOOD:**
```java
try (Connection conn = DriverManager.getConnection(DB_URL);
     Statement stmt = conn.createStatement();
     ResultSet rs = stmt.executeQuery(sql)) {
    // Process results
} // Resources automatically closed
```

---

### 4. Use Batch Inserts for Performance

**Slow (one at a time):**
```java
for (Student student : students) {
    String sql = "INSERT INTO students (student_id) VALUES (?)";
    PreparedStatement pstmt = conn.prepareStatement(sql);
    pstmt.setString(1, student.getId());
    pstmt.executeUpdate(); // 250 database round-trips!
}
```

**Fast (batch):**
```java
String sql = "INSERT INTO students (student_id) VALUES (?)";
PreparedStatement pstmt = conn.prepareStatement(sql);
for (Student student : students) {
    pstmt.setString(1, student.getId());
    pstmt.addBatch();
}
pstmt.executeBatch(); // 1 database round-trip!
```

**Performance:** Batch inserts are 10-100x faster for large datasets.

---

### 5. Enable Foreign Keys

SQLite doesn't enable foreign keys by default! Must enable:

```java
try (Connection conn = DriverManager.getConnection(DB_URL);
     Statement stmt = conn.createStatement()) {
    stmt.execute("PRAGMA foreign_keys = ON;");
}
```

**Without this:** Foreign key constraints are ignored (data corruption possible)

---

### 6. Use Indexes for Frequently Queried Columns

Already created in schema:
- `idx_enrollments_student` - Fast student lookup
- `idx_enrollments_course` - Fast course lookup
- `idx_assignments_schedule` - Fast schedule lookup
- `idx_assignments_classroom_time` - Fast availability check

**When to add more indexes:** If queries are slow, profile and add indexes to WHERE/JOIN columns.

---

### 7. Regular Database Maintenance

```sql
-- Analyze database for query optimization
ANALYZE;

-- Rebuild indexes (occasionally, if database grows large)
REINDEX;

-- Vacuum database to reclaim space (after many deletes)
VACUUM;
```

---

### 8. Backup Strategy

**Manual backup:**
```bash
# Just copy the database file
cp data/exam_scheduler.db data/exam_scheduler_backup.db
```

**Automatic backup on schedule save:**
```java
public void saveSchedule(Schedule schedule) {
    // Save to database
    scheduleDAO.save(schedule);

    // Also export to CSV as backup
    exportService.exportToCSV(schedule, "backups/");
}
```

---

## DATABASE FILE STRUCTURE

### Database File Location

```
exam-scheduler/
├── data/
│   ├── exam_scheduler.db           # Main database
│   ├── exam_scheduler_backup.db    # Backup (optional)
│   └── sample/
│       ├── students.csv
│       ├── courses.csv
│       ├── classrooms.csv
│       └── enrollments.csv
```

### Database File Size Estimates

| Data Size | Students | Courses | Enrollments | DB Size |
|-----------|----------|---------|-------------|---------|
| Small | 100 | 10 | 200 | ~50 KB |
| Sample | 250 | 20 | 800 | ~100 KB |
| Medium | 500 | 50 | 2,500 | ~500 KB |
| Large | 1,000 | 100 | 10,000 | ~2 MB |

**Note:** SQLite is very efficient. Even with 10 schedules saved (10 × 100 assignments = 1,000 rows), database remains < 5 MB.

---

## TROUBLESHOOTING

### Issue 1: Foreign Key Constraint Violation

**Error:**
```
FOREIGN KEY constraint failed
```

**Causes:**
- Trying to insert enrollment for non-existent student/course
- Trying to insert exam assignment for non-existent course/classroom

**Solution:**
```java
// Always check references exist first
if (studentDAO.exists(studentId) && courseDAO.exists(courseCode)) {
    enrollmentDAO.insert(enrollment);
} else {
    throw new IllegalArgumentException("Student or course does not exist");
}
```

---

### Issue 2: Unique Constraint Violation

**Error:**
```
UNIQUE constraint failed: enrollments.student_id, enrollments.course_code
```

**Cause:** Trying to enroll student in same course twice

**Solution:**
```java
// Check if enrollment exists before inserting
if (!enrollmentDAO.exists(studentId, courseCode)) {
    enrollmentDAO.insert(enrollment);
}
```

---

### Issue 3: Database Locked

**Error:**
```
database is locked
```

**Cause:** Another connection has an open transaction

**Solution:**
```java
// Set busy timeout (wait instead of failing immediately)
try (Connection conn = DriverManager.getConnection(DB_URL)) {
    Statement stmt = conn.createStatement();
    stmt.execute("PRAGMA busy_timeout = 5000"); // Wait up to 5 seconds
}
```

---

### Issue 4: Foreign Keys Not Enforced

**Symptom:** Can insert invalid foreign keys without error

**Cause:** Foreign keys not enabled

**Solution:**
```java
// Enable on EVERY connection
try (Connection conn = DriverManager.getConnection(DB_URL);
     Statement stmt = conn.createStatement()) {
    stmt.execute("PRAGMA foreign_keys = ON;");
    // Now do queries
}
```

---

## VERSION HISTORY

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-26 | Claude Code | Initial schema design |

---

## REFERENCES

- SQLite Documentation: https://www.sqlite.org/docs.html
- SQLite JDBC: https://github.com/xerial/sqlite-jdbc
- SQL Tutorial: https://www.sqlitetutorial.net/

---

**END OF DATABASE SCHEMA DOCUMENTATION**
