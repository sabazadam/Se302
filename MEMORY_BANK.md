# EXAM SCHEDULING SYSTEM - MEMORY BANK

**Project:** Exam Planner Desktop Application
**Course:** SE302 - Software Engineering
**Team:** Team 3, Section 1
**Date Started:** November 26, 2025
**Technology Stack:** Java 17+, JavaFX, SQLite, Maven

---

## TABLE OF CONTENTS

1. [Project Overview](#project-overview)
2. [Team Members & Roles](#team-members--roles)
3. [Requirements Summary](#requirements-summary)
4. [Database Schema](#database-schema)
5. [Architecture & Design Patterns](#architecture--design-patterns)
6. [Algorithm Design](#algorithm-design)
7. [Constraints & Business Rules](#constraints--business-rules)
8. [User Interface Design](#user-interface-design)
9. [API Contracts Between Layers](#api-contracts-between-layers)
10. [Testing Strategy](#testing-strategy)
11. [Development Workflow](#development-workflow)
12. [Key Technical Decisions](#key-technical-decisions)

---

## PROJECT OVERVIEW

### What We're Building

A **Windows desktop application** that automatically generates exam schedules for educational institutions by solving a constraint satisfaction problem (CSP). The system assigns exams to classrooms across time slots while respecting hard constraints like student availability and classroom capacity.

### Core Value Proposition

- **Automates** a complex, time-consuming manual scheduling process
- **Guarantees** all hard constraints are satisfied (no scheduling conflicts)
- **Optimizes** according to configurable strategies (minimize days, balance distribution, etc.)
- **Provides** multiple views (by classroom, student, course, day)
- **Enables** manual adjustments with real-time constraint validation

### Target Users

Student Affairs staff at educational institutions who need to:
- Schedule exams for 50-500+ students across 10-50+ courses
- Manage classroom allocation based on capacity
- Ensure no student conflicts (consecutive exams, max 2 per day)
- Export schedules for distribution

### Key Success Metrics

- **Performance:** Generate schedule for 50 courses/500 students in <10 seconds
- **Reliability:** 100% compliance with hard constraints (no violations allowed)
- **Usability:** New users can generate first schedule within 30 minutes
- **Accuracy:** Zero data corruption, all imports validated

---

## TEAM MEMBERS & ROLES

### Team Roster

| Name | Student ID | Role Specialty | Primary Responsibilities |
|------|-----------|----------------|------------------------|
| Kerem Bozdag | 20200602012 | Database & Data Layer | SQLite schema, DAO pattern, CSV import/export, data validation |
| Milena Larissa Ünal | 20230602107 | Algorithm Specialist | CSP backtracking solver, constraint validation, optimization strategies |
| Rabia Tülay Gokdag | 20210602029 | UI/FXML Specialist | JavaFX views, FXML layouts, Scene Builder, UI components |
| Mehmet Sefa Keskin | 20230602047 | Controller Layer | MVC controllers, event handling, UI-business logic integration |
| Feyza Gereme | 20230602028 | Testing & QA | JUnit tests, integration testing, CI/CD setup, test automation |
| Emin Arslan | 20230602007 | Documentation & Integration | JavaDoc, user manual, code reviews, feature integration |

### Collaboration Model

- **Specialist ownership** with cross-functional support
- **Weekly integration meetings** to sync work across layers
- **Pull Request reviews** required from at least one other team member
- **Pair programming sessions** for complex integration points (UI-Algorithm, Database-Algorithm)

---

## REQUIREMENTS SUMMARY

### Functional Requirements

#### FR1: Data Import from CSV
- Import 4 types of CSV files:
  1. **Student IDs** - List of all students (one per line)
  2. **Course Codes** - List of all courses (one per line)
  3. **Classroom Capacities** - Format: `ClassroomID;Capacity` (e.g., `Classroom_01;40`)
  4. **Attendance Lists** - Student-Course enrollment mapping
- **Validation requirements:**
  - Detect and reject duplicate entries
  - Validate file format before processing
  - Show line numbers for errors
- **User feedback:**
  - Progress indicator during import
  - Summary report (X students imported, Y duplicates rejected)

#### FR2: Configuration of Exam Period
- User can specify:
  - **Number of exam days** (e.g., 5 days)
  - **Number of time slots per day** (e.g., 4 slots)
  - Total slots = Days × Slots (e.g., 5 × 4 = 20 exam slots)
- Validation: Must have enough slots for all courses

#### FR3: Optimization Strategy Selection
- User chooses one strategy before generation:
  1. **Minimize Days Used** - Pack exams into fewest days possible
  2. **Balance Distribution** - Spread exams evenly across all days
  3. **Minimize Classrooms** - Use fewest classrooms possible
  4. **Balance Classrooms Per Day** - Even classroom usage across days
- Strategy affects solver's variable/value ordering heuristics

#### FR4: Automatic Schedule Generation
- **Algorithm:** Backtracking CSP solver with constraint propagation
- **Performance targets:**
  - Small datasets (50 courses, 500 students): <10 seconds
  - Medium datasets (100 courses, 1000 students): <2 minutes
  - Large datasets: May take several minutes (show progress)
- **User experience:**
  - Progress indicator with percentage complete
  - "Cancel" button to abort long-running generations
  - Success/failure notification with details

#### FR5: Multi-View Schedule Display
Four distinct view modes:

1. **Classroom View**
   - Shows: Which exams are in which classrooms
   - Columns: Classroom ID, Day, Time Slot, Course Code, Student Count

2. **Student View**
   - Shows: Personal exam schedule for each student
   - Columns: Student ID, Course Code, Day, Time Slot, Classroom
   - Filterable by student ID

3. **Course View**
   - Shows: Exact time and classroom for each course exam
   - Columns: Course Code, Day, Time Slot, Classroom, Enrolled Count

4. **Day View**
   - Shows: All exams scheduled for each day/time slot
   - Grid format: Days (rows) × Time Slots (columns)
   - Cell contents: Course codes in that slot

#### FR6: Export to CSV and Excel
- **CSV Export:** Simple comma-separated format for each view
- **Excel Export:**
  - Multi-sheet workbook (one sheet per view)
  - Formatted headers, borders, column widths
  - Ready for printing
- User can choose export format and destination folder

#### FR7: Manual Schedule Adjustments
- **Allowed operations:**
  - Move exam to different time slot
  - Move exam to different classroom
  - Swap two exams
- **Constraint validation:**
  - System checks all constraints before applying change
  - If any constraint would be violated, reject change
  - Show specific error message explaining which constraint prevents the change
- **Limitations:** Cannot change student enrollments or classroom capacities

#### FR8: Database Persistence (SQLite)
- **Local storage:** All data persisted in local SQLite file
- **Saved data:**
  - Imported student/course/classroom data
  - Generated schedules with metadata (creation date, optimization strategy)
  - User configuration settings
- **Operations:**
  - Save current schedule
  - Load previously saved schedule
  - Delete old schedules

#### FR10: Conflict Detection and Reporting
- **When no solution exists:**
  - Identify which constraints cannot be satisfied
  - Report specific conflicts:
    - Student X has 3 exams on Day Y (violates max 2 per day)
    - Course Z has 50 students but max classroom capacity is 40
    - Students A, B, C have consecutive exam conflicts
- **Recommendations:**
  - Extend exam period (add more days/slots)
  - Add classrooms with higher capacity
  - Reduce course enrollments
  - Alternative: Release consecutive exam constraint (not recommended)

#### FR12: Schedule History
- **View previous schedules** with metadata:
  - Creation date and time
  - Optimization strategy used
  - Number of days/slots configured
  - Course/student counts
- **Operations:**
  - Load and view any historical schedule
  - Compare different schedule versions
  - Export historical schedules

### Non-Functional Requirements

#### NFR1: Usability
- **Learning curve:** New users can generate first schedule within 30 minutes
- **Interface clarity:** Intuitive labels, clear navigation, logical workflow
- **Help system:** Built-in user manual with screenshots and examples
- **Error messages:** Clear, actionable, non-technical language

#### NFR2: Error Messaging Standards
- **Format:** "[Context] Problem detected: [Specific issue]. [Suggested fix]"
- **Examples:**
  - ✅ GOOD: "Import failed: Duplicate student ID 'Std_ID_123' found on lines 45 and 67. Remove duplicate entry and try again."
  - ❌ BAD: "Error: Duplicate key violation in students table"
- **Guidelines:**
  - Always include line numbers for file import errors
  - Suggest specific corrective actions
  - Avoid technical jargon (no SQL errors, stack traces to users)

#### NFR3: Performance
- **Small datasets:** <10 seconds for 50 courses, 500 students
- **Responsiveness:** UI remains responsive during generation (background thread)
- **Database queries:** Optimized with proper indexes (<100ms for typical queries)

#### NFR4: Reliability
- **No crashes:** Graceful error handling for all edge cases
- **Data integrity:** ACID transactions for all database operations
- **Validation:** All user inputs validated before processing
- **Backup:** Export capability serves as backup mechanism

#### NFR6: Maintainability
- **Code organization:** Clear package structure following MVC pattern
- **Naming conventions:** Java standard conventions (camelCase, PascalCase)
- **Documentation:** JavaDoc for all public methods and classes
- **Comments:** Explain "why" for complex algorithms, not obvious "what"
- **Extensibility:** Easy to add new optimization strategies, export formats, constraints

---

## DATABASE SCHEMA

### Entity-Relationship Model

```
┌─────────────┐         ┌──────────────┐         ┌──────────────┐
│   Student   │         │  Enrollment  │         │    Course    │
├─────────────┤         ├──────────────┤         ├──────────────┤
│ student_id  │◄───────►│ student_id   │◄───────►│ course_code  │
│             │   (M:N) │ course_code  │   (M:N)│              │
└─────────────┘         └──────────────┘         └──────────────┘
                                                         │
                                                         │ (1:M)
                                                         ▼
                        ┌──────────────┐         ┌─────────────────┐
                        │  Classroom   │◄────────│ ExamAssignment  │
                        ├──────────────┤  (1:M)  ├─────────────────┤
                        │ classroom_id │         │ exam_id         │
                        │ capacity     │         │ course_code     │
                        └──────────────┘         │ classroom_id    │
                                                 │ day             │
                                                 │ time_slot       │
                        ┌──────────────┐         │ schedule_id     │
                        │   Schedule   │◄────────│                 │
                        ├──────────────┤  (1:M)  └─────────────────┘
                        │ schedule_id  │
                        │ created_date │
                        │ strategy     │
                        │ num_days     │
                        │ slots_per_day│
                        └──────────────┘
```

### Table Definitions (SQLite)

#### 1. students
```sql
CREATE TABLE students (
    student_id TEXT PRIMARY KEY NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
- **Purpose:** Store all student records
- **Sample data:** Std_ID_001, Std_ID_002, ..., Std_ID_250

#### 2. courses
```sql
CREATE TABLE courses (
    course_code TEXT PRIMARY KEY NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
- **Purpose:** Store all course records
- **Sample data:** CourseCode_01, CourseCode_02, ..., CourseCode_20

#### 3. classrooms
```sql
CREATE TABLE classrooms (
    classroom_id TEXT PRIMARY KEY NOT NULL,
    capacity INTEGER NOT NULL CHECK(capacity > 0),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
- **Purpose:** Store classroom information with seating capacity
- **Sample data:** Classroom_01 (capacity: 40), Classroom_02 (capacity: 40), etc.
- **Validation:** Capacity must be positive integer

#### 4. enrollments
```sql
CREATE TABLE enrollments (
    enrollment_id INTEGER PRIMARY KEY AUTOINCREMENT,
    student_id TEXT NOT NULL,
    course_code TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (student_id) REFERENCES students(student_id) ON DELETE CASCADE,
    FOREIGN KEY (course_code) REFERENCES courses(course_code) ON DELETE CASCADE,
    UNIQUE(student_id, course_code)
);

CREATE INDEX idx_enrollments_student ON enrollments(student_id);
CREATE INDEX idx_enrollments_course ON enrollments(course_code);
```
- **Purpose:** Many-to-many relationship between students and courses
- **Uniqueness:** One enrollment record per student-course pair
- **Indexes:** Fast lookups for "which courses for student X" and "which students in course Y"

#### 5. schedules
```sql
CREATE TABLE schedules (
    schedule_id INTEGER PRIMARY KEY AUTOINCREMENT,
    created_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP NOT NULL,
    optimization_strategy TEXT NOT NULL,
    num_days INTEGER NOT NULL CHECK(num_days > 0),
    slots_per_day INTEGER NOT NULL CHECK(slots_per_day > 0),
    status TEXT DEFAULT 'generated' CHECK(status IN ('generated', 'saved', 'archived'))
);
```
- **Purpose:** Store metadata for each generated schedule
- **Optimization strategies:** 'minimize_days', 'balance_distribution', 'minimize_classrooms', 'balance_classrooms'
- **Status values:**
  - 'generated' - Just created
  - 'saved' - User explicitly saved
  - 'archived' - Old schedule kept for history

#### 6. exam_assignments
```sql
CREATE TABLE exam_assignments (
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
    UNIQUE(schedule_id, course_code),
    UNIQUE(schedule_id, classroom_id, day, time_slot)
);

CREATE INDEX idx_assignments_schedule ON exam_assignments(schedule_id);
CREATE INDEX idx_assignments_course ON exam_assignments(course_code);
CREATE INDEX idx_assignments_classroom_time ON exam_assignments(classroom_id, day, time_slot);
```
- **Purpose:** Store exam scheduling assignments (which course in which room at what time)
- **Uniqueness constraints:**
  - Each course appears once per schedule
  - Each classroom-day-timeslot combination used once per schedule
- **Indexes:** Fast lookups for schedule queries, course lookups, and classroom availability checks

### Sample Data Statistics

Based on provided sample data:
- **Students:** 250 (Std_ID_001 to Std_ID_250)
- **Courses:** 20 (CourseCode_01 to CourseCode_20)
- **Classrooms:** 10 (Classroom_01 to Classroom_10, each capacity 40)
- **Average enrollments per course:** ~40 students
- **Total enrollment records:** ~800 (20 courses × ~40 students)

### Database File Location

- **Development:** `./data/exam_scheduler.db`
- **Production:** User's Documents folder or configurable path
- **Backup:** Auto-export to CSV on schedule save

---

## ARCHITECTURE & DESIGN PATTERNS

### Overall Architecture: MVC (Model-View-Controller)

```
┌─────────────────────────────────────────────────────────────┐
│                         VIEW LAYER                          │
│  (JavaFX FXML + Scene Builder)                             │
│                                                             │
│  • ImportView.fxml          • ScheduleView.fxml            │
│  • ConfigurationView.fxml   • ClassroomView.fxml           │
│  • StudentView.fxml         • CourseView.fxml              │
│  • DayView.fxml             • ExportView.fxml              │
└──────────────────┬──────────────────────────────────────────┘
                   │ User Events (button clicks, etc.)
                   ▼
┌─────────────────────────────────────────────────────────────┐
│                     CONTROLLER LAYER                        │
│  (JavaFX Controllers)                                       │
│                                                             │
│  • ImportController         • ScheduleController           │
│  • ConfigurationController  • ViewControllers              │
│  • ExportController         • MainController               │
└──────────────────┬──────────────────────────────────────────┘
                   │ Method calls
                   ▼
┌─────────────────────────────────────────────────────────────┐
│                    BUSINESS LOGIC LAYER                     │
│  (Service Classes)                                          │
│                                                             │
│  • ScheduleService          • ImportService                │
│  • CSPSolver                • ExportService                │
│  • ConstraintValidator      • ConfigurationService         │
│  • OptimizationStrategy     • ConflictDetector             │
└──────────────────┬──────────────────────────────────────────┘
                   │ Data operations
                   ▼
┌─────────────────────────────────────────────────────────────┐
│                     DATA ACCESS LAYER                       │
│  (DAO Pattern)                                              │
│                                                             │
│  • StudentDAO               • ScheduleDAO                  │
│  • CourseDAO                • ExamAssignmentDAO            │
│  • ClassroomDAO             • EnrollmentDAO                │
│  • DatabaseConnection       • TransactionManager           │
└──────────────────┬──────────────────────────────────────────┘
                   │ SQL queries
                   ▼
┌─────────────────────────────────────────────────────────────┐
│                      DATA LAYER                             │
│  (SQLite Database)                                          │
│                                                             │
│  exam_scheduler.db                                          │
└─────────────────────────────────────────────────────────────┘
```

### Package Structure

```
src/main/java/
├── com.se302.examscheduler/
│   ├── model/                    # Domain Models (POJOs)
│   │   ├── Student.java
│   │   ├── Course.java
│   │   ├── Classroom.java
│   │   ├── Enrollment.java
│   │   ├── Schedule.java
│   │   ├── ExamAssignment.java
│   │   └── ScheduleConfiguration.java
│   │
│   ├── dao/                      # Data Access Objects
│   │   ├── StudentDAO.java
│   │   ├── CourseDAO.java
│   │   ├── ClassroomDAO.java
│   │   ├── EnrollmentDAO.java
│   │   ├── ScheduleDAO.java
│   │   ├── ExamAssignmentDAO.java
│   │   └── DatabaseConnection.java
│   │
│   ├── service/                  # Business Logic
│   │   ├── ScheduleService.java
│   │   ├── ImportService.java
│   │   ├── ExportService.java
│   │   ├── ConfigurationService.java
│   │   └── ValidationService.java
│   │
│   ├── algorithm/                # CSP Solver
│   │   ├── CSPSolver.java
│   │   ├── ConstraintValidator.java
│   │   ├── ConflictDetector.java
│   │   ├── strategy/
│   │   │   ├── OptimizationStrategy.java (interface)
│   │   │   ├── MinimizeDaysStrategy.java
│   │   │   ├── BalanceDistributionStrategy.java
│   │   │   ├── MinimizeClassroomsStrategy.java
│   │   │   └── BalanceClassroomsStrategy.java
│   │   └── heuristics/
│   │       ├── VariableOrderingHeuristic.java
│   │       └── ValueOrderingHeuristic.java
│   │
│   ├── controller/               # JavaFX Controllers
│   │   ├── MainController.java
│   │   ├── ImportController.java
│   │   ├── ConfigurationController.java
│   │   ├── ScheduleController.java
│   │   ├── ClassroomViewController.java
│   │   ├── StudentViewController.java
│   │   ├── CourseViewController.java
│   │   ├── DayViewController.java
│   │   └── ExportController.java
│   │
│   ├── util/                     # Utility Classes
│   │   ├── CSVParser.java
│   │   ├── ExcelExporter.java
│   │   ├── ValidationUtil.java
│   │   └── ProgressTracker.java
│   │
│   └── Main.java                 # Application Entry Point
│
src/main/resources/
├── fxml/                         # FXML Layout Files
│   ├── MainWindow.fxml
│   ├── ImportView.fxml
│   ├── ConfigurationView.fxml
│   ├── ScheduleView.fxml
│   ├── ClassroomView.fxml
│   ├── StudentView.fxml
│   ├── CourseView.fxml
│   ├── DayView.fxml
│   └── ExportView.fxml
│
├── css/                          # Stylesheets
│   └── styles.css
│
├── images/                       # Icons and images
│
└── database/
    └── schema.sql                # Database initialization script

src/test/java/
├── com.se302.examscheduler/
│   ├── dao/                      # DAO Tests
│   ├── service/                  # Service Tests
│   ├── algorithm/                # Algorithm Tests
│   └── integration/              # Integration Tests
```

### Design Patterns Used

#### 1. DAO (Data Access Object) Pattern
- **Purpose:** Separate data access logic from business logic
- **Implementation:** One DAO class per entity (StudentDAO, CourseDAO, etc.)
- **Benefits:** Easy to mock for testing, can swap database implementations

#### 2. Strategy Pattern (Optimization Strategies)
- **Purpose:** Allow different scheduling optimization algorithms to be swapped at runtime
- **Implementation:** `OptimizationStrategy` interface with concrete implementations
- **Benefits:** Easy to add new strategies without modifying solver code

#### 3. Singleton Pattern (Database Connection)
- **Purpose:** Ensure only one database connection pool exists
- **Implementation:** `DatabaseConnection.getInstance()`
- **Benefits:** Resource efficiency, consistent connection management

#### 4. Observer Pattern (Progress Tracking)
- **Purpose:** UI observes long-running algorithm execution
- **Implementation:** `ProgressTracker` with listeners, JavaFX `Task<T>`
- **Benefits:** Responsive UI, can show progress and allow cancellation

#### 5. Factory Pattern (DAO Creation)
- **Purpose:** Centralize DAO instance creation
- **Implementation:** `DAOFactory` class
- **Benefits:** Easier testing, dependency injection

---

## ALGORITHM DESIGN

### Backtracking CSP Solver Architecture

#### Problem Definition (CSP Formulation)

**Variables:**
- Each **course** is a variable that needs assignment
- Example: `CourseCode_01`, `CourseCode_02`, ..., `CourseCode_20`

**Domain (Possible Values):**
- Each variable can be assigned to a **{Classroom, Day, TimeSlot}** triple
- Example domain for a course with 35 students:
  ```
  {(Classroom_01, 1, 1), (Classroom_01, 1, 2), ...,
   (Classroom_02, 1, 1), (Classroom_02, 1, 2), ...,
   (Classroom_10, 5, 4)}
  ```
- Domain is pre-filtered to remove impossible assignments (e.g., classroom too small)

**Constraints:**
1. **No consecutive exams for any student** (Hard)
2. **Max 2 exams per student per day** (Hard)
3. **Classroom capacity must not be exceeded** (Hard)
4. **Each course scheduled exactly once** (Implicit in CSP formulation)
5. **No classroom double-booking** (Implicit - each {classroom, day, slot} used once)

#### Backtracking Algorithm Pseudocode

```
function BACKTRACK-CSP(assignment, csp):
    if assignment is complete:
        return assignment

    var = SELECT-UNASSIGNED-VARIABLE(csp, assignment)

    for each value in ORDER-DOMAIN-VALUES(var, assignment, csp):
        if value is consistent with assignment (satisfies all constraints):
            add {var = value} to assignment

            # Constraint propagation (forward checking)
            inferences = INFER(csp, var, value)

            if inferences ≠ failure:
                add inferences to assignment
                result = BACKTRACK-CSP(assignment, csp)

                if result ≠ failure:
                    return result

            remove {var = value} and inferences from assignment

    return failure
```

#### Heuristic 1: Variable Ordering (SELECT-UNASSIGNED-VARIABLE)

**Strategy: Minimum Remaining Values (MRV)**
- Select the course with the **fewest valid domain values** remaining
- Rationale: Fail fast on difficult assignments, prune search space early
- Tie-breaking: Choose course with most students (harder to place)

```java
public Course selectUnassignedVariable(Assignment assignment) {
    Course minCourse = null;
    int minDomainSize = Integer.MAX_VALUE;

    for (Course course : unassignedCourses) {
        int domainSize = calculateValidDomainSize(course, assignment);
        if (domainSize < minDomainSize) {
            minDomainSize = domainSize;
            minCourse = course;
        }
    }
    return minCourse;
}
```

#### Heuristic 2: Value Ordering (ORDER-DOMAIN-VALUES)

**Strategy: Least Constraining Value**
- Select the {classroom, day, slot} that **leaves most flexibility** for remaining courses
- Rationale: Maximize future options, avoid dead-ends

**Optimization Strategy Influence:**
- **Minimize Days:** Prefer earlier days, earlier slots
- **Balance Distribution:** Prefer days/slots with fewer assignments
- **Minimize Classrooms:** Prefer already-used classrooms
- **Balance Classrooms:** Prefer classrooms with fewer assignments

```java
public List<Assignment> orderDomainValues(Course course, OptimizationStrategy strategy) {
    List<Assignment> domain = getValidAssignments(course);

    // Sort based on optimization strategy
    domain.sort((a1, a2) -> strategy.compareAssignments(a1, a2));

    return domain;
}
```

#### Constraint Checking Functions

**1. No Consecutive Exams Constraint**
```java
public boolean checkNoConsecutiveExams(Course course, int day, int slot, Assignment assignment) {
    Set<Student> enrolledStudents = getEnrolledStudents(course);

    for (Student student : enrolledStudents) {
        // Check if student has exam in previous slot
        if (slot > 1 && hasExamInSlot(student, day, slot - 1, assignment)) {
            return false;
        }
        // Check if student has exam in next slot
        if (slot < slotsPerDay && hasExamInSlot(student, day, slot + 1, assignment)) {
            return false;
        }
        // Check boundary with previous day
        if (slot == 1 && day > 1 && hasExamInSlot(student, day - 1, slotsPerDay, assignment)) {
            return false;
        }
        // Check boundary with next day
        if (slot == slotsPerDay && day < numDays && hasExamInSlot(student, day + 1, 1, assignment)) {
            return false;
        }
    }
    return true;
}
```

**2. Max 2 Exams Per Day Constraint**
```java
public boolean checkMaxTwoExamsPerDay(Course course, int day, Assignment assignment) {
    Set<Student> enrolledStudents = getEnrolledStudents(course);

    for (Student student : enrolledStudents) {
        int examsOnDay = countExamsForStudentOnDay(student, day, assignment);
        if (examsOnDay >= 2) {
            return false; // Would cause student to have 3+ exams on this day
        }
    }
    return true;
}
```

**3. Classroom Capacity Constraint**
```java
public boolean checkClassroomCapacity(Course course, Classroom classroom) {
    int enrolledCount = getEnrolledStudentCount(course);
    return enrolledCount <= classroom.getCapacity();
}
```

#### Forward Checking (Constraint Propagation)

After assigning a course, reduce domains of unassigned courses:
- Remove {classroom, day, slot} if it's now occupied
- Remove consecutive slots for students in this course
- Remove same-day slots if students already have 2 exams that day

```java
public Map<Course, Set<Assignment>> forwardCheck(Course assigned, Assignment value) {
    Map<Course, Set<Assignment>> removedValues = new HashMap<>();
    Set<Student> students = getEnrolledStudents(assigned);

    for (Course unassigned : unassignedCourses) {
        Set<Assignment> toRemove = new HashSet<>();
        Set<Student> unassignedStudents = getEnrolledStudents(unassigned);

        // If courses share students, check constraints
        Set<Student> overlap = intersection(students, unassignedStudents);
        if (!overlap.isEmpty()) {
            for (Assignment a : getDomain(unassigned)) {
                if (violatesConstraintWith(a, value, overlap)) {
                    toRemove.add(a);
                }
            }
        }

        if (!toRemove.isEmpty()) {
            removedValues.put(unassigned, toRemove);
            getDomain(unassigned).removeAll(toRemove);
        }
    }

    return removedValues;
}
```

#### Conflict Detection (When No Solution Exists)

If backtracking exhausts all possibilities without finding a solution:

```java
public ConflictReport analyzeFailure() {
    ConflictReport report = new ConflictReport();

    // Find students with too many courses for available slots
    for (Student student : students) {
        int courseCount = getEnrolledCourseCount(student);
        int maxPossibleExams = numDays * 2; // Max 2 per day
        if (courseCount > maxPossibleExams) {
            report.addConflict(student, "Enrolled in " + courseCount +
                " courses but only " + maxPossibleExams + " slots available");
        }
    }

    // Find courses with insufficient classroom capacity
    for (Course course : courses) {
        int enrolledCount = getEnrolledStudentCount(course);
        int maxCapacity = getMaxClassroomCapacity();
        if (enrolledCount > maxCapacity) {
            report.addConflict(course, "Enrolled count " + enrolledCount +
                " exceeds max classroom capacity " + maxCapacity);
        }
    }

    // Find groups of students with too many overlapping enrollments
    // (complex graph analysis - detect cliques in conflict graph)

    return report;
}
```

#### Performance Optimizations

1. **Pre-compute enrollment mappings:**
   - `Map<Course, Set<Student>>` - students in each course
   - `Map<Student, Set<Course>>` - courses for each student
   - Avoids repeated database queries during backtracking

2. **Domain pre-filtering:**
   - Eliminate classroom assignments where capacity < enrolled count
   - Reduce domain size before backtracking starts

3. **Early termination checks:**
   - If any course has empty domain, fail immediately
   - If student has more courses than max possible slots, fail immediately

4. **Memoization:**
   - Cache constraint check results for repeated {student, day, slot} combinations

5. **Parallel domain evaluation:**
   - Use Java streams to evaluate constraint checks in parallel
   - Useful for large student sets

#### Progress Tracking

```java
public class CSPSolver {
    private ProgressTracker progressTracker;
    private volatile boolean cancelled = false;

    public Schedule solve(ScheduleConfiguration config) {
        int totalCourses = courses.size();
        int assignedCourses = 0;

        while (assignedCourses < totalCourses && !cancelled) {
            // Backtracking logic
            assignedCourses++;
            progressTracker.updateProgress(assignedCourses, totalCourses);
        }

        if (cancelled) {
            throw new CancelledException();
        }

        return buildSchedule(assignment);
    }

    public void cancel() {
        this.cancelled = true;
    }
}
```

---

## CONSTRAINTS & BUSINESS RULES

### Hard Constraints (CANNOT be violated)

#### Constraint 1: No Consecutive Exams for Students

**Definition:**
If a student has an exam in time slot N, they CANNOT have another exam in time slot N+1 (immediately following).

**Rationale:**
Students need break time between exams to rest, review, and travel between exam locations.

**Edge Cases:**
- **Day boundaries:** If a student has an exam in the last slot of Day 1, they CAN have an exam in the first slot of Day 2 (not considered consecutive because of overnight break)
  - **UPDATE:** Actually, slots are numbered sequentially across days, so slot 4 of Day 1 and slot 1 of Day 2 ARE consecutive. Need to check this in implementation.
- **First/last slots:** No previous/next slot to check

**Validation Logic:**
```java
public boolean validateNoConsecutiveExams(Student student, int day, int slot, Assignment assignment) {
    // Calculate absolute slot number (for sequential numbering across days)
    int absoluteSlot = (day - 1) * slotsPerDay + slot;

    // Check previous slot
    if (absoluteSlot > 1) {
        int prevDay = (absoluteSlot - 2) / slotsPerDay + 1;
        int prevSlot = (absoluteSlot - 2) % slotsPerDay + 1;
        if (hasExam(student, prevDay, prevSlot, assignment)) {
            return false;
        }
    }

    // Check next slot
    int totalSlots = numDays * slotsPerDay;
    if (absoluteSlot < totalSlots) {
        int nextDay = absoluteSlot / slotsPerDay + 1;
        int nextSlot = absoluteSlot % slotsPerDay + 1;
        if (hasExam(student, nextDay, nextSlot, assignment)) {
            return false;
        }
    }

    return true;
}
```

**Error Message Template:**
"Cannot assign [Course] to [Day] [Slot]: Student [StudentID] already has an exam in the consecutive slot [Day] [Slot]."

---

#### Constraint 2: Maximum 2 Exams Per Student Per Day

**Definition:**
A student cannot have more than 2 exams on any single day.

**Rationale:**
More than 2 exams per day is excessively stressful and reduces exam quality/fairness.

**Edge Cases:**
- Student enrolled in only 1-2 courses total: Always satisfiable
- Student enrolled in 10+ courses over 5 days: May be impossible to satisfy both this constraint and consecutive constraint

**Validation Logic:**
```java
public boolean validateMaxTwoExamsPerDay(Student student, int day, Assignment assignment) {
    int examsOnDay = 0;

    for (int slot = 1; slot <= slotsPerDay; slot++) {
        if (hasExam(student, day, slot, assignment)) {
            examsOnDay++;
        }
    }

    return examsOnDay < 2; // Currently has 0 or 1, so can add one more
}
```

**Error Message Template:**
"Cannot assign [Course] to [Day]: Student [StudentID] already has 2 exams on this day."

---

#### Constraint 3: Classroom Capacity Must Not Be Exceeded

**Definition:**
The number of students enrolled in a course (who will take the exam) cannot exceed the seating capacity of the assigned classroom.

**Rationale:**
Physical limitation - cannot fit more students than seats available.

**Edge Cases:**
- Course with 0 students enrolled: Can be assigned to any classroom (but shouldn't appear in schedule at all)
- Multiple classrooms needed: Current design doesn't support splitting one exam across multiple classrooms (would need extension)

**Validation Logic:**
```java
public boolean validateClassroomCapacity(Course course, Classroom classroom) {
    int enrolledCount = getEnrolledStudentCount(course);
    return enrolledCount <= classroom.getCapacity();
}
```

**Error Message Template:**
"Cannot assign [Course] to [Classroom]: Course has [X] students but classroom capacity is only [Y]."

---

#### Constraint 4: Each Course Scheduled Exactly Once (Implicit)

**Definition:**
Every course must appear in the schedule exactly one time (one exam per course).

**Rationale:**
Business requirement - each course needs one exam during the exam period.

**Implementation:**
- CSP formulation ensures this (each course is a variable assigned exactly one value)
- Database uniqueness constraint: `UNIQUE(schedule_id, course_code)`

---

#### Constraint 5: No Classroom Double-Booking (Implicit)

**Definition:**
A classroom cannot host two exams at the same time (same day + time slot).

**Rationale:**
Physical limitation - one room can only hold one exam at a time.

**Implementation:**
- CSP domain ensures each {classroom, day, slot} used at most once
- Database uniqueness constraint: `UNIQUE(schedule_id, classroom_id, day, time_slot)`

---

### Soft Constraints (Optimization Objectives)

These are preferences, not requirements. They guide the solver's heuristics but can be violated if necessary.

#### Soft Constraint 1: Minimize Days Used
- **Objective:** Pack exams into as few days as possible
- **Heuristic:** Prefer earlier days when ordering domain values
- **Use case:** Shorter exam period, reduce facility costs

#### Soft Constraint 2: Balance Distribution Across Days
- **Objective:** Spread exams evenly across all available days
- **Heuristic:** Prefer days with fewer assigned exams
- **Use case:** Reduce student stress, balance proctoring workload

#### Soft Constraint 3: Minimize Classrooms Used
- **Objective:** Use as few different classrooms as possible
- **Heuristic:** Prefer already-assigned classrooms
- **Use case:** Reduce setup costs, centralize exam locations

#### Soft Constraint 4: Balance Classrooms Per Day
- **Objective:** Use similar number of classrooms each day
- **Heuristic:** Prefer days with fewer classrooms in use
- **Use case:** Balance proctoring staff assignments

---

### Business Rules

#### Rule 1: Data Import Validation
- No duplicate student IDs in imported file
- No duplicate course codes in imported file
- No duplicate classroom IDs in imported file
- Enrollment records must reference existing students and courses
- Classroom capacity must be positive integer
- CSV files must have correct format (specific delimiters, expected columns)

#### Rule 2: Configuration Validation
- Number of days must be positive integer (1-30 reasonable range)
- Number of slots per day must be positive integer (1-6 reasonable range)
- Total available slots (days × slots) must be ≥ number of courses
- At least one classroom must exist
- At least one student must exist
- At least one course must exist

#### Rule 3: Schedule Generation Preconditions
- All required data imported (students, courses, classrooms, enrollments)
- Configuration set (days, slots, optimization strategy)
- Database connection available

#### Rule 4: Manual Edit Validation
- Can only edit existing schedules (cannot edit during generation)
- All hard constraints must still be satisfied after edit
- Cannot change student enrollments via manual edit (data integrity)
- Cannot change classroom capacity via manual edit

#### Rule 5: Export Requirements
- Schedule must be fully generated (all courses assigned)
- User must specify output file path and format (CSV or Excel)
- File path must be writable

---

## USER INTERFACE DESIGN

### Main Window Layout

```
┌────────────────────────────────────────────────────────────────┐
│  Exam Scheduler                                    [_] [□] [X] │
├────────────────────────────────────────────────────────────────┤
│  File  Edit  View  Help                                        │
├────────────────────────────────────────────────────────────────┤
│                                                                │
│  ┌─────────────────┐  ┌────────────────────────────────────┐ │
│  │                 │  │                                    │ │
│  │  NAVIGATION     │  │       CONTENT AREA                │ │
│  │  PANEL          │  │                                    │ │
│  │                 │  │  (Dynamic - changes based on      │ │
│  │ • Import Data   │  │   selected navigation item)       │ │
│  │ • Configure     │  │                                    │ │
│  │ • Generate      │  │                                    │ │
│  │ • View Schedule │  │                                    │ │
│  │ • Export        │  │                                    │ │
│  │ • History       │  │                                    │ │
│  │                 │  │                                    │ │
│  │                 │  │                                    │ │
│  └─────────────────┘  └────────────────────────────────────┘ │
│                                                                │
├────────────────────────────────────────────────────────────────┤
│  Status: Ready                                                 │
└────────────────────────────────────────────────────────────────┘
```

### View 1: Import Data View

**Purpose:** Import CSV files with validation feedback

**Layout:**
```
Import Data
───────────────────────────────────────────────────────
Students File:      [/path/to/students.csv    ] [Browse...]
Courses File:       [/path/to/courses.csv     ] [Browse...]
Classrooms File:    [/path/to/classrooms.csv  ] [Browse...]
Enrollments File:   [/path/to/enrollments.csv ] [Browse...]

                                    [Import All]  [Clear]

Import Results:
┌─────────────────────────────────────────────────────┐
│ ✓ Students: 250 imported, 0 duplicates rejected    │
│ ✓ Courses: 20 imported, 0 duplicates rejected      │
│ ✓ Classrooms: 10 imported, 0 duplicates rejected   │
│ ✓ Enrollments: 800 imported, 0 errors              │
└─────────────────────────────────────────────────────┘
```

**Key Components:**
- File path text fields (read-only)
- Browse buttons (open file dialog)
- Import All button (validates and imports all files)
- Results text area (shows success/error messages with line numbers)

---

### View 2: Configuration View

**Purpose:** Set exam period parameters and optimization strategy

**Layout:**
```
Schedule Configuration
───────────────────────────────────────────────────────
Exam Period:
  Number of Days:        [5    ] days
  Time Slots Per Day:    [4    ] slots

  Total Available Slots: 20 slots
  Total Courses:         20 courses

  ✓ Sufficient slots available

Optimization Strategy:
  ○ Minimize Days Used
  ● Balance Distribution Across Days
  ○ Minimize Classrooms Needed
  ○ Balance Classrooms Per Day

                           [Save Configuration]
```

**Key Components:**
- Number spinners for days and slots (with validation)
- Calculated field for total slots
- Radio buttons for optimization strategy selection
- Save button (enables Generate view)

---

### View 3: Generate Schedule View

**Purpose:** Run scheduling algorithm with progress feedback

**Layout:**
```
Generate Schedule
───────────────────────────────────────────────────────
Configuration:
  Exam Period: 5 days × 4 slots = 20 total slots
  Strategy: Balance Distribution Across Days
  Courses: 20
  Students: 250

[                                                    ]
                 Generating schedule...
             12 / 20 courses assigned (60%)

             Estimated time remaining: 8 seconds


                      [Cancel]


Recent Generations:
┌─────────────────────────────────────────────────────┐
│ 2025-11-26 10:30 - Balance Distribution - Success  │
│ 2025-11-26 09:15 - Minimize Days - Success         │
└─────────────────────────────────────────────────────┘
```

**Key Components:**
- Configuration summary (read-only)
- Progress bar (animated during generation)
- Status text (updates in real-time)
- Cancel button (aborts generation)
- Recent generations list (clickable to load previous schedules)

**Success State:**
```
✓ Schedule Generated Successfully!

  All 20 courses assigned
  All constraints satisfied
  Generation time: 3.2 seconds

              [View Schedule]  [Export]
```

**Failure State:**
```
✗ Schedule Generation Failed

  Unable to satisfy all constraints.

  Conflicts detected:
  • Student Std_ID_042 enrolled in 15 courses (max 10 possible)
  • CourseCode_15 has 55 students (max classroom capacity: 40)

  Recommendations:
  • Extend exam period to 7 days
  • Add classroom with capacity ≥55
  • Reduce enrollments for affected students

              [View Conflicts]  [Adjust Configuration]
```

---

### View 4: Schedule Views (4 Sub-Views)

#### 4A: Classroom View

**Purpose:** Show which exams are in which classrooms

**Layout:**
```
Schedule - Classroom View
───────────────────────────────────────────────────────
Filter: [All Classrooms ▼]    Day: [All Days ▼]

┌────────────┬─────┬──────┬────────────┬─────────────┐
│ Classroom  │ Day │ Slot │ Course     │ Students    │
├────────────┼─────┼──────┼────────────┼─────────────┤
│ Room_01    │  1  │  1   │ Course_01  │ 40          │
│ Room_01    │  1  │  2   │ Course_05  │ 38          │
│ Room_01    │  2  │  1   │ Course_12  │ 40          │
│ Room_02    │  1  │  1   │ Course_02  │ 35          │
│ ...        │ ... │ ...  │ ...        │ ...         │
└────────────┴─────┴──────┴────────────┴─────────────┘

                    [Export View]  [Edit]
```

#### 4B: Student View

**Purpose:** Show personal exam schedule for each student

**Layout:**
```
Schedule - Student View
───────────────────────────────────────────────────────
Search Student: [Std_ID_042        ] [Search]

┌─────────────┬────────────┬─────┬──────┬────────────┐
│ Student ID  │ Course     │ Day │ Slot │ Classroom  │
├─────────────┼────────────┼─────┼──────┼────────────┤
│ Std_ID_042  │ Course_01  │  1  │  1   │ Room_01    │
│ Std_ID_042  │ Course_07  │  1  │  3   │ Room_03    │
│ Std_ID_042  │ Course_12  │  2  │  1   │ Room_01    │
│ Std_ID_042  │ Course_18  │  3  │  2   │ Room_05    │
└─────────────┴────────────┴─────┴──────┴────────────┘

                    [Export Student Schedule]
```

#### 4C: Course View

**Purpose:** Show exam details for each course

**Layout:**
```
Schedule - Course View
───────────────────────────────────────────────────────
Sort by: [Course Code ▼]

┌────────────┬─────┬──────┬────────────┬─────────────┐
│ Course     │ Day │ Slot │ Classroom  │ Enrolled    │
├────────────┼─────┼──────┼────────────┼─────────────┤
│ Course_01  │  1  │  1   │ Room_01    │ 40          │
│ Course_02  │  1  │  1   │ Room_02    │ 35          │
│ Course_03  │  1  │  2   │ Room_04    │ 38          │
│ Course_04  │  1  │  3   │ Room_02    │ 42          │
│ ...        │ ... │ ...  │ ...        │ ...         │
└────────────┴─────┴──────┴────────────┴─────────────┘

                    [Export View]
```

#### 4D: Day View (Grid Layout)

**Purpose:** Visual grid showing all exams by day and time slot

**Layout:**
```
Schedule - Day View
───────────────────────────────────────────────────────
┌──────┬──────────────┬──────────────┬──────────────┬──────────────┐
│      │   Slot 1     │   Slot 2     │   Slot 3     │   Slot 4     │
├──────┼──────────────┼──────────────┼──────────────┼──────────────┤
│ Day 1│ Course_01    │ Course_05    │ Course_07    │ Course_11    │
│      │ (Room_01)    │ (Room_01)    │ (Room_03)    │ (Room_02)    │
│      │ 40 students  │ 38 students  │ 35 students  │ 40 students  │
│      │              │              │              │              │
│      │ Course_02    │ Course_06    │ Course_08    │ Course_12    │
│      │ (Room_02)    │ (Room_04)    │ (Room_05)    │ (Room_06)    │
│      │ 35 students  │ 40 students  │ 38 students  │ 42 students  │
├──────┼──────────────┼──────────────┼──────────────┼──────────────┤
│ Day 2│ ...          │ ...          │ ...          │ ...          │
└──────┴──────────────┴──────────────┴──────────────┴──────────────┘

                    [Export View]
```

---

### View 5: Export View

**Purpose:** Export schedule to CSV or Excel

**Layout:**
```
Export Schedule
───────────────────────────────────────────────────────
Export Format:
  ○ CSV (Comma-Separated Values)
  ● Excel (.xlsx)

Views to Export:
  ☑ Classroom View
  ☑ Student View
  ☑ Course View
  ☑ Day View

  (Excel will create separate sheet for each view)

Output Location:
  [C:\Users\Student\Documents\exam_schedule.xlsx] [Browse...]

                           [Export]  [Cancel]


Export Status:
┌─────────────────────────────────────────────────────┐
│ ✓ Export completed successfully!                   │
│   File saved to: C:\Users\Student\Documents\...    │
│   File size: 24 KB                                  │
└─────────────────────────────────────────────────────┘
```

---

### View 6: History View

**Purpose:** View and load previously generated schedules

**Layout:**
```
Schedule History
───────────────────────────────────────────────────────
┌─────────────┬──────────────────┬─────────┬──────────┬────────┐
│ Date/Time   │ Strategy         │ Days    │ Courses  │ Status │
├─────────────┼──────────────────┼─────────┼──────────┼────────┤
│ 11/26 10:30 │ Balance Distrib. │ 5×4=20  │ 20       │ Saved  │
│ 11/26 09:15 │ Minimize Days    │ 5×4=20  │ 20       │ Saved  │
│ 11/25 14:22 │ Minimize Rooms   │ 6×3=18  │ 18       │ Saved  │
│ ...         │ ...              │ ...     │ ...      │ ...    │
└─────────────┴──────────────────┴─────────┴──────────┴────────┘

                [Load Selected]  [Delete]  [Export]
```

---

### UI/UX Guidelines

#### Color Scheme
- **Primary:** Blue (#2196F3) for action buttons, links
- **Success:** Green (#4CAF50) for success messages, checkmarks
- **Error:** Red (#F44336) for error messages, warnings
- **Neutral:** Gray (#9E9E9E) for disabled elements, borders
- **Background:** Light gray (#FAFAFA) for content areas

#### Typography
- **Font:** System default (Segoe UI on Windows)
- **Headers:** 18pt bold
- **Body text:** 12pt regular
- **Labels:** 11pt regular

#### Interaction Patterns
- **Buttons:** Hover state (darker shade), click state (pressed effect)
- **Tables:** Row hover highlight, sortable columns (click header)
- **Forms:** Validation on blur (lose focus), red border for invalid fields
- **Progress:** Animated progress bar, percentage text, time remaining estimate

#### Accessibility
- **Keyboard navigation:** Tab order follows logical flow, Enter activates buttons
- **Tooltips:** Hover over fields shows help text
- **Error messages:** Red border + icon + text description
- **Focus indicators:** Visible blue outline on focused elements

---

## API CONTRACTS BETWEEN LAYERS

### Controller → Service Layer Contracts

#### ImportService Interface

```java
public interface ImportService {
    /**
     * Import students from CSV file
     * @param filePath Absolute path to CSV file
     * @return ImportResult with success/failure counts and error messages
     * @throws FileNotFoundException if file doesn't exist
     * @throws InvalidFormatException if CSV format is incorrect
     */
    ImportResult importStudents(String filePath)
        throws FileNotFoundException, InvalidFormatException;

    /**
     * Import courses from CSV file
     */
    ImportResult importCourses(String filePath)
        throws FileNotFoundException, InvalidFormatException;

    /**
     * Import classrooms from CSV file
     * Format: ClassroomID;Capacity
     */
    ImportResult importClassrooms(String filePath)
        throws FileNotFoundException, InvalidFormatException;

    /**
     * Import enrollments (student-course mappings) from CSV file
     */
    ImportResult importEnrollments(String filePath)
        throws FileNotFoundException, InvalidFormatException;

    /**
     * Validate all imported data for consistency
     * @return ValidationResult with list of issues found
     */
    ValidationResult validateImportedData();
}

public class ImportResult {
    private int successCount;
    private int errorCount;
    private List<ImportError> errors;
    private boolean success;

    public static class ImportError {
        private int lineNumber;
        private String message;
        private String rawLine;
    }
}
```

---

#### ScheduleService Interface

```java
public interface ScheduleService {
    /**
     * Generate schedule using CSP solver
     * @param config Schedule configuration (days, slots, strategy)
     * @param progressTracker Callback for progress updates
     * @return Generated Schedule object
     * @throws NoSolutionException if constraints cannot be satisfied
     * @throws CancelledException if user cancels during generation
     */
    Schedule generateSchedule(ScheduleConfiguration config, ProgressTracker progressTracker)
        throws NoSolutionException, CancelledException;

    /**
     * Validate manual edit against constraints
     * @param scheduleId ID of schedule to modify
     * @param courseCode Course to move
     * @param newDay New day assignment
     * @param newSlot New time slot assignment
     * @param newClassroom New classroom assignment
     * @return ValidationResult indicating if change is valid
     */
    ValidationResult validateEdit(
        int scheduleId,
        String courseCode,
        int newDay,
        int newSlot,
        String newClassroom
    );

    /**
     * Apply manual edit to schedule
     * @throws ConstraintViolationException if edit violates constraints
     */
    void applyEdit(int scheduleId, ExamAssignment newAssignment)
        throws ConstraintViolationException;

    /**
     * Save current schedule to database
     * @return Saved schedule ID
     */
    int saveSchedule(Schedule schedule);

    /**
     * Load schedule from database
     */
    Schedule loadSchedule(int scheduleId)
        throws ScheduleNotFoundException;

    /**
     * Get all saved schedules (metadata only)
     */
    List<ScheduleMetadata> getScheduleHistory();

    /**
     * Delete schedule from database
     */
    void deleteSchedule(int scheduleId);
}

public class ScheduleConfiguration {
    private int numDays;
    private int slotsPerDay;
    private OptimizationStrategy strategy;
}

public enum OptimizationStrategy {
    MINIMIZE_DAYS,
    BALANCE_DISTRIBUTION,
    MINIMIZE_CLASSROOMS,
    BALANCE_CLASSROOMS
}
```

---

#### ExportService Interface

```java
public interface ExportService {
    /**
     * Export schedule to CSV files (one file per view)
     * @param schedule Schedule to export
     * @param outputDirectory Directory to save CSV files
     * @param views Which views to export (CLASSROOM, STUDENT, COURSE, DAY)
     * @return ExportResult with file paths and success status
     */
    ExportResult exportToCSV(
        Schedule schedule,
        String outputDirectory,
        Set<ViewType> views
    ) throws IOException;

    /**
     * Export schedule to Excel workbook (one sheet per view)
     * @param schedule Schedule to export
     * @param outputFilePath Path to save .xlsx file
     * @param views Which views to export
     * @return ExportResult with file path and success status
     */
    ExportResult exportToExcel(
        Schedule schedule,
        String outputFilePath,
        Set<ViewType> views
    ) throws IOException;
}

public enum ViewType {
    CLASSROOM,
    STUDENT,
    COURSE,
    DAY
}

public class ExportResult {
    private boolean success;
    private List<String> filePaths;
    private String message;
    private long fileSizeBytes;
}
```

---

### Service → DAO Layer Contracts

#### StudentDAO Interface

```java
public interface StudentDAO {
    /**
     * Insert single student
     */
    void insert(Student student) throws SQLException;

    /**
     * Batch insert multiple students (more efficient)
     */
    void insertBatch(List<Student> students) throws SQLException;

    /**
     * Get student by ID
     */
    Optional<Student> findById(String studentId);

    /**
     * Get all students
     */
    List<Student> findAll();

    /**
     * Check if student exists
     */
    boolean exists(String studentId);

    /**
     * Delete all students (for re-import)
     */
    void deleteAll() throws SQLException;

    /**
     * Get students enrolled in specific course
     */
    List<Student> findByCourse(String courseCode);
}
```

#### CourseDAO Interface

```java
public interface CourseDAO {
    void insert(Course course) throws SQLException;
    void insertBatch(List<Course> courses) throws SQLException;
    Optional<Course> findById(String courseCode);
    List<Course> findAll();
    boolean exists(String courseCode);
    void deleteAll() throws SQLException;

    /**
     * Get enrollment count for course
     */
    int getEnrollmentCount(String courseCode);
}
```

#### EnrollmentDAO Interface

```java
public interface EnrollmentDAO {
    void insert(Enrollment enrollment) throws SQLException;
    void insertBatch(List<Enrollment> enrollments) throws SQLException;

    /**
     * Get all courses for a student
     */
    List<Course> getCoursesForStudent(String studentId);

    /**
     * Get all students for a course
     */
    List<Student> getStudentsForCourse(String courseCode);

    /**
     * Check if enrollment exists
     */
    boolean exists(String studentId, String courseCode);

    /**
     * Get total enrollment count
     */
    int getTotalEnrollmentCount();

    void deleteAll() throws SQLException;
}
```

#### ScheduleDAO Interface

```java
public interface ScheduleDAO {
    /**
     * Insert new schedule (returns generated ID)
     */
    int insert(Schedule schedule) throws SQLException;

    /**
     * Update existing schedule
     */
    void update(Schedule schedule) throws SQLException;

    /**
     * Get schedule by ID (includes all exam assignments)
     */
    Optional<Schedule> findById(int scheduleId);

    /**
     * Get all schedules (metadata only, no assignments)
     */
    List<ScheduleMetadata> findAllMetadata();

    /**
     * Delete schedule and all associated assignments (cascade)
     */
    void delete(int scheduleId) throws SQLException;
}
```

#### ExamAssignmentDAO Interface

```java
public interface ExamAssignmentDAO {
    /**
     * Insert single assignment
     */
    void insert(ExamAssignment assignment) throws SQLException;

    /**
     * Batch insert all assignments for a schedule
     */
    void insertBatch(List<ExamAssignment> assignments) throws SQLException;

    /**
     * Get all assignments for a schedule
     */
    List<ExamAssignment> findBySchedule(int scheduleId);

    /**
     * Get assignment for specific course in a schedule
     */
    Optional<ExamAssignment> findByCourse(int scheduleId, String courseCode);

    /**
     * Update single assignment (for manual edits)
     */
    void update(ExamAssignment assignment) throws SQLException;

    /**
     * Delete all assignments for a schedule
     */
    void deleteBySchedule(int scheduleId) throws SQLException;

    /**
     * Check if classroom-day-slot is occupied in schedule
     */
    boolean isSlotOccupied(int scheduleId, String classroomId, int day, int slot);
}
```

---

### Service → Algorithm Layer Contracts

#### CSPSolver Interface

```java
public interface CSPSolver {
    /**
     * Solve CSP to generate schedule
     * @param courses List of courses to schedule
     * @param classrooms Available classrooms
     * @param enrollments Student-course enrollment data
     * @param config Exam period configuration
     * @param strategy Optimization strategy to use
     * @param progressTracker Progress callback
     * @return Map of course → {classroom, day, slot} assignments
     * @throws NoSolutionException if no valid schedule exists
     * @throws CancelledException if solve() is cancelled
     */
    Map<Course, ExamAssignment> solve(
        List<Course> courses,
        List<Classroom> classrooms,
        List<Enrollment> enrollments,
        ScheduleConfiguration config,
        OptimizationStrategy strategy,
        ProgressTracker progressTracker
    ) throws NoSolutionException, CancelledException;

    /**
     * Cancel ongoing solve operation
     */
    void cancel();
}
```

#### ConstraintValidator Interface

```java
public interface ConstraintValidator {
    /**
     * Check if assignment satisfies all constraints
     * @param course Course being assigned
     * @param classroom Classroom to assign to
     * @param day Day to assign to
     * @param slot Time slot to assign to
     * @param currentAssignment Partial assignment so far
     * @param enrollments Enrollment data
     * @return true if assignment is valid
     */
    boolean isValid(
        Course course,
        Classroom classroom,
        int day,
        int slot,
        Map<Course, ExamAssignment> currentAssignment,
        List<Enrollment> enrollments
    );

    /**
     * Get detailed constraint violations for an assignment
     * @return List of violated constraints with explanations
     */
    List<ConstraintViolation> getViolations(
        Course course,
        Classroom classroom,
        int day,
        int slot,
        Map<Course, ExamAssignment> currentAssignment,
        List<Enrollment> enrollments
    );
}

public class ConstraintViolation {
    private ConstraintType type;
    private String message;
    private List<String> affectedStudents; // For student-related violations
}

public enum ConstraintType {
    CONSECUTIVE_EXAMS,
    MAX_TWO_PER_DAY,
    CLASSROOM_CAPACITY,
    CLASSROOM_DOUBLE_BOOKING
}
```

#### ConflictDetector Interface

```java
public interface ConflictDetector {
    /**
     * Analyze why no solution exists
     * @return Report detailing conflicts and recommendations
     */
    ConflictReport analyzeFailure(
        List<Course> courses,
        List<Classroom> classrooms,
        List<Enrollment> enrollments,
        ScheduleConfiguration config
    );
}

public class ConflictReport {
    private List<StudentConflict> studentConflicts;
    private List<CourseConflict> courseConflicts;
    private List<String> recommendations;
}

public class StudentConflict {
    private Student student;
    private String description; // "Enrolled in 15 courses, max 10 possible with constraints"
    private int courseCount;
    private int maxPossible;
}

public class CourseConflict {
    private Course course;
    private String description; // "55 students enrolled, max classroom capacity 40"
    private int enrolledCount;
    private int maxCapacity;
}
```

---

## TESTING STRATEGY

### Unit Testing

#### DAO Layer Tests
**Framework:** JUnit 5
**Database:** In-memory SQLite (`:memory:`)
**Coverage targets:** >90%

**Test categories:**
1. **CRUD operations:** Insert, update, delete, select
2. **Batch operations:** Batch insert performance and correctness
3. **Transactions:** Rollback on error, commit on success
4. **Uniqueness constraints:** Duplicate prevention
5. **Foreign key constraints:** Referential integrity
6. **Index usage:** Query performance

**Example test:**
```java
@Test
public void testBatchInsertStudents_ShouldInsertAllStudents() {
    // Arrange
    List<Student> students = createTestStudents(100);
    StudentDAO dao = new StudentDAOImpl(testConnection);

    // Act
    dao.insertBatch(students);

    // Assert
    assertEquals(100, dao.findAll().size());
    assertTrue(dao.exists("Std_ID_001"));
}

@Test
public void testInsertDuplicateStudent_ShouldThrowException() {
    // Arrange
    StudentDAO dao = new StudentDAOImpl(testConnection);
    Student student = new Student("Std_ID_001");
    dao.insert(student);

    // Act & Assert
    assertThrows(SQLException.class, () -> dao.insert(student));
}
```

---

#### Algorithm Layer Tests
**Framework:** JUnit 5
**Test data:** Small synthetic datasets with known solutions

**Test categories:**
1. **Constraint validation:** Each constraint checked individually
2. **Simple solvable cases:** 3-5 courses with obvious solution
3. **Unsolvable cases:** Known conflicts (student with 15 courses, etc.)
4. **Edge cases:**
   - Single course
   - All students in one course
   - Minimum/maximum capacity classrooms
   - Consecutive slots at day boundaries
5. **Optimization strategies:** Verify strategy affects solution
6. **Performance:** Small dataset (<10 courses) solves in <1 second

**Example tests:**
```java
@Test
public void testNoConsecutiveExams_StudentWithTwoCourses_ShouldNotBeInConsecutiveSlots() {
    // Arrange
    CSPSolver solver = new BacktrackingSolver();
    Student student = new Student("S1");
    Course course1 = new Course("C1");
    Course course2 = new Course("C2");
    Enrollment e1 = new Enrollment(student, course1);
    Enrollment e2 = new Enrollment(student, course2);
    Classroom classroom = new Classroom("R1", 50);
    ScheduleConfiguration config = new ScheduleConfiguration(2, 2); // 2 days, 2 slots

    // Act
    Map<Course, ExamAssignment> solution = solver.solve(
        Arrays.asList(course1, course2),
        Arrays.asList(classroom),
        Arrays.asList(e1, e2),
        config,
        OptimizationStrategy.MINIMIZE_DAYS,
        null
    );

    // Assert
    assertNotNull(solution);
    ExamAssignment a1 = solution.get(course1);
    ExamAssignment a2 = solution.get(course2);
    assertFalse(areConsecutive(a1, a2), "Courses should not be in consecutive slots");
}

@Test
public void testUnsolvableCase_StudentWith15Courses_ShouldThrowNoSolutionException() {
    // Arrange: Student enrolled in 15 courses, only 5 days × 2 slots = 10 possible
    CSPSolver solver = new BacktrackingSolver();
    Student student = createStudentWithNCourses(15);
    ScheduleConfiguration config = new ScheduleConfiguration(5, 2); // Only 10 slots

    // Act & Assert
    assertThrows(NoSolutionException.class, () ->
        solver.solve(courses, classrooms, enrollments, config, strategy, null)
    );
}
```

---

#### Service Layer Tests
**Framework:** JUnit 5 + Mockito
**Mocking:** Mock DAO and Algorithm layers

**Test categories:**
1. **Business logic validation:** Configuration validation, data consistency checks
2. **Transaction handling:** Rollback on failure, commit on success
3. **Error propagation:** Service catches and wraps DAO/algorithm exceptions appropriately
4. **Null handling:** Graceful handling of null inputs

**Example test:**
```java
@Test
public void testGenerateSchedule_WithValidConfig_ShouldReturnSchedule() {
    // Arrange
    ScheduleService service = new ScheduleServiceImpl(mockDAO, mockSolver);
    ScheduleConfiguration config = new ScheduleConfiguration(5, 4, OptimizationStrategy.BALANCE_DISTRIBUTION);

    when(mockDAO.findAllCourses()).thenReturn(createTestCourses(20));
    when(mockDAO.findAllClassrooms()).thenReturn(createTestClassrooms(10));
    when(mockDAO.findAllEnrollments()).thenReturn(createTestEnrollments());
    when(mockSolver.solve(any(), any(), any(), any(), any(), any())).thenReturn(createValidSolution());

    // Act
    Schedule schedule = service.generateSchedule(config, null);

    // Assert
    assertNotNull(schedule);
    assertEquals(20, schedule.getAssignments().size());
    verify(mockDAO, times(1)).saveSchedule(any());
}
```

---

### Integration Testing

#### Database Integration Tests
**Setup:** Real SQLite file (not in-memory)
**Cleanup:** Delete database file after each test class

**Test categories:**
1. **End-to-end import:** CSV → Database → Verify
2. **Schedule generation → save → load:** Full round-trip
3. **Manual edit → validation → save:** Edit workflow
4. **Concurrent access:** Multiple connections (if applicable)

**Example test:**
```java
@Test
public void testFullImportWorkflow_ShouldPersistAllData() {
    // Arrange
    ImportService importService = new ImportServiceImpl(realDAO);
    String studentsFile = "src/test/resources/test_students.csv";
    String coursesFile = "src/test/resources/test_courses.csv";
    String classroomsFile = "src/test/resources/test_classrooms.csv";
    String enrollmentsFile = "src/test/resources/test_enrollments.csv";

    // Act
    ImportResult r1 = importService.importStudents(studentsFile);
    ImportResult r2 = importService.importCourses(coursesFile);
    ImportResult r3 = importService.importClassrooms(classroomsFile);
    ImportResult r4 = importService.importEnrollments(enrollmentsFile);

    // Assert
    assertTrue(r1.isSuccess());
    assertTrue(r2.isSuccess());
    assertTrue(r3.isSuccess());
    assertTrue(r4.isSuccess());

    // Verify data persisted
    assertEquals(250, realDAO.findAllStudents().size());
    assertEquals(20, realDAO.findAllCourses().size());
    assertEquals(10, realDAO.findAllClassrooms().size());
    assertEquals(800, realDAO.getTotalEnrollmentCount());
}
```

---

#### UI Integration Tests
**Framework:** TestFX (JavaFX testing framework)
**Approach:** Simulate user interactions

**Test categories:**
1. **Navigation:** Click navigation items, verify view changes
2. **Import workflow:** Select files, click import, verify results
3. **Generation workflow:** Configure, generate, view schedule
4. **Export workflow:** Select format, export, verify file created

**Example test:**
```java
@Test
public void testImportWorkflow_UserSelectsFilesAndImports_ShouldShowSuccessMessage(FxRobot robot) {
    // Arrange: Start application

    // Act
    robot.clickOn("#importNavButton");
    robot.clickOn("#browseStudentsButton");
    // (File dialog interaction - may need mocking)
    robot.clickOn("#importAllButton");

    // Assert
    verifyThat("#importResultsText", hasText(containsString("250 imported")));
}
```

---

### Performance Testing

#### Benchmark Tests
**Framework:** JMH (Java Microbenchmark Harness) or simple timing

**Test categories:**
1. **Small dataset:** 50 courses, 500 students (target: <10 seconds)
2. **Medium dataset:** 100 courses, 1000 students (target: <2 minutes)
3. **Large dataset:** 200 courses, 2000 students (acceptance: <10 minutes)
4. **Database queries:** All common queries <100ms

**Example test:**
```java
@Test
public void testSmallDatasetPerformance_ShouldCompleteUnder10Seconds() {
    // Arrange
    CSPSolver solver = new BacktrackingSolver();
    List<Course> courses = createTestCourses(50);
    List<Student> students = createTestStudents(500);
    List<Enrollment> enrollments = createRandomEnrollments(students, courses, 10); // 10 courses per student avg
    List<Classroom> classrooms = createTestClassrooms(15);
    ScheduleConfiguration config = new ScheduleConfiguration(10, 4); // 40 slots

    // Act
    long startTime = System.currentTimeMillis();
    Map<Course, ExamAssignment> solution = solver.solve(courses, classrooms, enrollments, config, OptimizationStrategy.BALANCE_DISTRIBUTION, null);
    long endTime = System.currentTimeMillis();

    // Assert
    assertNotNull(solution);
    long duration = endTime - startTime;
    assertTrue(duration < 10000, "Solving took " + duration + "ms, expected <10000ms");
}
```

---

### Test Data Management

#### Test Fixtures
**Location:** `src/test/resources/`

**Files:**
- `test_students_10.csv` - 10 students
- `test_students_100.csv` - 100 students
- `test_students_250.csv` - 250 students (matches sample data)
- `test_courses_5.csv` - 5 courses
- `test_courses_20.csv` - 20 courses (matches sample data)
- `test_classrooms_small.csv` - 3 classrooms, capacity 20
- `test_classrooms_medium.csv` - 10 classrooms, capacity 40 (matches sample data)
- `test_enrollments_simple.csv` - Simple enrollment pattern for easy testing
- `test_enrollments_complex.csv` - Complex pattern to stress-test solver

#### Test Data Builders
Use builder pattern for creating test objects:

```java
public class TestDataBuilder {
    public static List<Student> createStudents(int count) {
        List<Student> students = new ArrayList<>();
        for (int i = 1; i <= count; i++) {
            students.add(new Student(String.format("Std_ID_%03d", i)));
        }
        return students;
    }

    public static List<Course> createCourses(int count) {
        List<Course> courses = new ArrayList<>();
        for (int i = 1; i <= count; i++) {
            courses.add(new Course(String.format("Course_%02d", i)));
        }
        return courses;
    }

    // ... similar methods for classrooms, enrollments
}
```

---

### Continuous Integration Testing

**Platform:** GitHub Actions

**Workflow:**
1. On every push to feature branch: Run unit tests
2. On PR to `develop`: Run unit + integration tests
3. On merge to `develop`: Run full test suite + performance tests
4. On merge to `main`: Run all tests + generate coverage report

**Coverage Requirements:**
- **Overall:** >70% code coverage
- **Critical paths (algorithm, constraints):** >90% coverage
- **DAO layer:** >85% coverage

**Example GitHub Actions workflow:**
```yaml
name: Java CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          java-version: '17'
      - name: Build with Maven
        run: mvn clean compile
      - name: Run tests
        run: mvn test
      - name: Generate coverage report
        run: mvn jacoco:report
      - name: Upload coverage to Codecov
        uses: codecov/codecov-action@v3
```

---

## DEVELOPMENT WORKFLOW

### Git Branching Strategy

**Branch structure:**
```
main
  └── develop
       ├── feature/database-schema
       ├── feature/csv-import
       ├── feature/csp-solver
       ├── feature/ui-main-window
       ├── feature/classroom-view
       └── bugfix/constraint-validation-issue
```

**Branch naming conventions:**
- `feature/short-description` - New features
- `bugfix/issue-description` - Bug fixes
- `hotfix/critical-issue` - Urgent production fixes (from main)
- `refactor/component-name` - Code refactoring
- `test/component-name` - Test additions

**Workflow:**
1. **Create feature branch** from `develop`:
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/csv-import
   ```

2. **Work on feature** with regular commits:
   ```bash
   git add .
   git commit -m "Add CSV parser for student data"
   git push origin feature/csv-import
   ```

3. **Create Pull Request** to `develop`:
   - Title: Brief description (e.g., "Add CSV import functionality")
   - Description: Detailed changes, testing done, screenshots (for UI)
   - Assign reviewer (at least one other team member)
   - Link to any related issues/tasks

4. **Code Review:**
   - Reviewer checks code quality, test coverage, documentation
   - Leaves comments for improvements
   - Approves when satisfied

5. **Merge to develop:**
   - Use "Squash and Merge" for clean history
   - Delete feature branch after merge

6. **Release to main:**
   - When develop is stable and ready for release
   - Create PR from `develop` to `main`
   - Requires approval from Documentation & Integration Lead
   - Tag release with version number (e.g., `v1.0.0`)

---

### Commit Message Guidelines

**Format:**
```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `refactor`: Code refactoring (no functional change)
- `test`: Adding or updating tests
- `docs`: Documentation changes
- `style`: Code style changes (formatting, no logic change)
- `chore`: Build process, dependency updates

**Examples:**
```
feat(import): Add CSV import for student data

Implemented CSVParser utility class to read student IDs from CSV file.
Includes validation for duplicates and empty lines.

Closes #15
```

```
fix(csp-solver): Correct consecutive exam constraint checking

Previous implementation didn't handle day boundaries correctly.
Now checks if last slot of day N and first slot of day N+1 are consecutive.

Fixes #42
```

---

### Pull Request Template

Create `.github/PULL_REQUEST_TEMPLATE.md`:

```markdown
## Description
<!-- Brief description of changes -->

## Type of Change
- [ ] New feature
- [ ] Bug fix
- [ ] Refactoring
- [ ] Documentation
- [ ] Testing

## Changes Made
<!-- List of specific changes -->
-
-
-

## Testing Done
<!-- How did you test these changes? -->
-
-

## Screenshots (if applicable)
<!-- For UI changes -->

## Checklist
- [ ] Code follows Java naming conventions
- [ ] Added JavaDoc for public methods
- [ ] Added unit tests (or not applicable)
- [ ] All tests pass locally
- [ ] No new compiler warnings
- [ ] Updated documentation (if needed)

## Related Issues
<!-- Link to issues: Closes #123, Fixes #456 -->
```

---

### Code Review Checklist

**For reviewers:**

#### Code Quality
- [ ] Follows Java naming conventions (camelCase for variables, PascalCase for classes)
- [ ] No magic numbers (use named constants)
- [ ] Meaningful variable/method names
- [ ] Appropriate use of access modifiers (private, public, protected)
- [ ] No code duplication

#### Functionality
- [ ] Code does what PR description says
- [ ] Edge cases handled
- [ ] Error handling appropriate (try-catch where needed)
- [ ] No obvious bugs

#### Architecture
- [ ] Follows MVC pattern (code in correct layer)
- [ ] No tight coupling (DAO doesn't call UI, etc.)
- [ ] Uses appropriate design patterns

#### Testing
- [ ] Unit tests added for new methods
- [ ] Tests cover edge cases
- [ ] All existing tests still pass

#### Documentation
- [ ] JavaDoc added for public methods
- [ ] Complex algorithms have explanatory comments
- [ ] README updated if needed

#### Performance
- [ ] No obvious performance issues (e.g., nested loops with O(n²) that could be O(n))
- [ ] Database queries are efficient (use indexes)

---

### Weekly Development Cycle

**Monday:**
- **Team standup** (15 min): What did you do last week? What will you do this week? Any blockers?
- **Integration review**: Discuss PRs that need merging, resolve conflicts

**Wednesday:**
- **Mid-week check-in** (30 min): Progress update, pair programming sessions scheduled

**Friday:**
- **End-of-week review** (30 min): Demo completed features, retrospective (what went well, what to improve)
- **Planning for next week**: Assign new tasks based on roadmap

---

### Development Environment Setup

**Required tools:**
1. **Java JDK 17+** (download from Oracle or adopt OpenJDK)
2. **Maven or Gradle** (build tool)
3. **IDE:** IntelliJ IDEA (recommended) or Eclipse
4. **Git** (version control)
5. **Scene Builder** (for FXML visual editing)
6. **SQLite Browser** (for database inspection)

**Setup steps:**
```bash
# Clone repository
git clone https://github.com/your-team/exam-scheduler.git
cd exam-scheduler

# Checkout develop branch
git checkout develop

# Build project
mvn clean install

# Run tests
mvn test

# Run application
mvn javafx:run
```

**IDE configuration:**
- Install Checkstyle plugin (for code style enforcement)
- Configure Java 17 as project SDK
- Enable "Optimize imports on the fly"
- Set code formatter to Java standard style

---

## KEY TECHNICAL DECISIONS

### Decision Log

#### Decision 1: Why Backtracking CSP Solver?

**Context:** Need to choose scheduling algorithm approach.

**Options considered:**
1. Greedy heuristic (fast but may not find solution)
2. Genetic algorithm (good for optimization but probabilistic)
3. Integer Linear Programming (optimal but complex to implement)
4. Backtracking CSP solver (systematic, guaranteed to find solution if one exists)

**Decision:** Backtracking CSP solver with constraint propagation

**Rationale:**
- **Correctness:** Guarantees finding solution or proving none exists
- **Constraint handling:** Natural fit for hard constraints
- **Extensibility:** Easy to add new constraints or optimization strategies
- **Performance:** With good heuristics (MRV, least constraining value), performs well on small-medium datasets
- **Understandable:** Algorithm is well-documented and teachable

**Trade-offs:**
- May be slower than greedy for very large datasets (acceptable given requirements)
- Not guaranteed to find *optimal* solution (finds *valid* solution, optimizes via heuristics)

---

#### Decision 2: Why SQLite?

**Context:** Need to choose database for local persistence.

**Options considered:**
1. Flat files (CSV/JSON)
2. Embedded H2 database
3. SQLite
4. MySQL/PostgreSQL (external server)

**Decision:** SQLite

**Rationale:**
- **Zero configuration:** No server setup, no connection strings, just a file
- **Cross-platform:** Works on Windows, Mac, Linux
- **Lightweight:** Small footprint, fast for small-medium datasets
- **ACID transactions:** Full database features (transactions, foreign keys, indexes)
- **No dependencies:** No external process needed
- **Easy backup:** Just copy the .db file

**Trade-offs:**
- Not suitable for concurrent multi-user access (but this is single-user desktop app)
- Limited scalability compared to server databases (but dataset sizes are small)

---

#### Decision 3: Why JavaFX with FXML + MVC?

**Context:** Need to choose UI framework and architecture.

**Options considered:**
1. Swing (older Java UI framework)
2. JavaFX with pure Java code
3. JavaFX with FXML + MVC
4. JavaFX with FXML + MVVM

**Decision:** JavaFX with FXML + MVC

**Rationale:**
- **Modern:** JavaFX is the modern Java UI framework (Swing is legacy)
- **Separation of concerns:** FXML separates UI layout from logic
- **Visual design:** Scene Builder provides WYSIWYG editor for FXML
- **MVC familiarity:** Team likely familiar with MVC pattern from coursework
- **Testability:** Can test controllers independently of views

**Trade-offs:**
- MVVM might be more testable, but MVC is simpler for team to learn
- Pure Java code (no FXML) might be more flexible, but FXML provides better separation

---

#### Decision 4: Why Maven (or Gradle)?

**Context:** Need to choose build tool.

**Options considered:**
1. Manual compilation (javac)
2. Ant
3. Maven
4. Gradle

**Decision:** Maven (or Gradle, team can choose)

**Rationale:**
- **Dependency management:** Automatically downloads libraries (SQLite JDBC, JUnit, etc.)
- **Standardized structure:** Convention over configuration
- **Build lifecycle:** Compile, test, package (JAR), run all automated
- **CI/CD integration:** Easy to integrate with GitHub Actions
- **Plugin ecosystem:** JavaFX plugin, testing plugins, coverage plugins

**Trade-offs:**
- Adds complexity compared to manual compilation
- Gradle is more flexible but has steeper learning curve

---

#### Decision 5: Why Feature Branching (Not Trunk-Based Development)?

**Context:** Need to choose Git workflow for team of 6.

**Options considered:**
1. Everyone commits to main (chaos)
2. Trunk-based development (frequent merges to main)
3. Git Flow (complex with release branches)
4. Feature branching with develop branch

**Decision:** Feature branching with `main` and `develop` branches

**Rationale:**
- **Isolation:** Each feature developed independently without breaking others
- **Code review:** PRs enable peer review before merging
- **Stable main:** Main branch always has working code
- **Learning:** Teaches industry-standard Git workflow
- **Flexibility:** Can work on multiple features in parallel

**Trade-offs:**
- More complex than trunk-based development
- Risk of long-lived branches and merge conflicts (mitigated by frequent integration)

---

#### Decision 6: Test Coverage Target: 70%

**Context:** Need to set realistic test coverage goal.

**Options considered:**
1. No specific target (risky)
2. 50% coverage (too low for quality)
3. 70% coverage (balanced)
4. 90%+ coverage (too time-consuming)

**Decision:** 70% overall coverage, 90% for critical paths (algorithm, constraints)

**Rationale:**
- **Balanced:** Provides confidence without excessive test maintenance
- **Focus on critical:** Algorithm correctness is most important
- **Achievable:** Team of 6 can reach this in 10-week timeline
- **Industry standard:** 70% is common target for projects

**Trade-offs:**
- Not 100% coverage (but diminishing returns after 70%)
- Some UI code may be hard to test (acceptable)

---

#### Decision 7: Progress Tracking via JavaFX Task

**Context:** Need to show progress during long-running schedule generation.

**Options considered:**
1. No progress indication (bad UX)
2. Custom threading with callbacks
3. JavaFX Task<T> with progress updates
4. Background service with polling

**Decision:** JavaFX Task<T> with ProgressTracker callback

**Rationale:**
- **Built-in:** JavaFX Task provides progress, cancellation, error handling
- **Thread-safe:** Updates UI from background thread safely
- **Cancellable:** User can cancel long-running operations
- **Standard pattern:** Well-documented JavaFX approach

**Implementation:**
```java
public class ScheduleGenerationTask extends Task<Schedule> {
    @Override
    protected Schedule call() throws Exception {
        updateMessage("Initializing solver...");
        updateProgress(0, 100);

        // Solver updates progress via callback
        Schedule schedule = solver.solve(..., (current, total) -> {
            updateProgress(current, total);
            updateMessage("Assigned " + current + " / " + total + " courses");
        });

        return schedule;
    }
}
```

---

#### Decision 8: CSV Format for Import (Not JSON/XML)

**Context:** Sample data provided in CSV format.

**Options considered:**
1. CSV (simple text format)
2. JSON (structured data)
3. XML (verbose but structured)
4. Excel (.xlsx) directly

**Decision:** CSV for import, both CSV and Excel for export

**Rationale:**
- **Simplicity:** CSV is easiest to create/edit for users (Excel, Google Sheets, text editor)
- **Sample data:** Already provided in CSV format
- **Lightweight:** No XML/JSON parsing libraries needed
- **Universal:** All spreadsheet programs support CSV

**Trade-offs:**
- Less structured than JSON/XML (but data is simple enough)
- Encoding issues possible (mitigated by using UTF-8)

---

## GLOSSARY

**Terms used throughout project:**

- **CSP (Constraint Satisfaction Problem):** A problem where variables must be assigned values subject to constraints
- **Backtracking:** Algorithm that tries assignments and "backs up" when constraints are violated
- **Domain:** Set of possible values a variable can take
- **Hard Constraint:** Constraint that CANNOT be violated (vs soft constraint = preference)
- **Forward Checking:** Constraint propagation technique that reduces domains after each assignment
- **Heuristic:** Rule of thumb to guide search (e.g., MRV, least constraining value)
- **MRV (Minimum Remaining Values):** Choose variable with fewest valid domain values
- **DAO (Data Access Object):** Design pattern separating data access from business logic
- **POJO (Plain Old Java Object):** Simple Java class with no special requirements
- **FXML:** JavaFX XML-based language for describing UI layouts
- **MVC (Model-View-Controller):** Architecture pattern separating data, UI, and logic
- **JDBC (Java Database Connectivity):** Java API for database operations
- **ORM (Object-Relational Mapping):** Mapping between objects and database tables
- **ACID (Atomicity, Consistency, Isolation, Durability):** Database transaction properties
- **PR (Pull Request):** Request to merge code changes into a branch
- **CI/CD (Continuous Integration/Continuous Deployment):** Automated testing and deployment
- **Enrollment:** Student-course relationship (which students are in which courses)
- **Assignment (in CSP context):** Mapping of variable to value (course → {classroom, day, slot})
- **Slot:** Time period for an exam (e.g., Slot 1 = 9:00-11:00 AM)
- **Optimization Strategy:** Preference for how to schedule (minimize days, balance load, etc.)

---

## VERSION HISTORY

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-26 | Claude Code | Initial memory bank creation |

---

## CONTACT & RESOURCES

**Team Communication:**
- Primary channel: [Specify - WhatsApp group, Discord, Slack, etc.]
- Code repository: [GitHub URL once created]
- Documentation: This memory bank + inline JavaDoc

**External Resources:**
- JavaFX Documentation: https://openjfx.io/javadoc/17/
- SQLite Documentation: https://www.sqlite.org/docs.html
- JUnit 5 User Guide: https://junit.org/junit5/docs/current/user-guide/
- Constraint Satisfaction (AI textbook): Russell & Norvig, Chapter 6

**Course Materials:**
- SE302 course website: [URL from instructor]
- Professor contact: [Email]
- Office hours: [Schedule]

---

## NOTES & ASSUMPTIONS

**Assumptions made:**
1. Exams are single-session (no multi-part exams spanning multiple slots)
2. All students in a course take exam at same time (no separate sections)
3. One exam per course (no midterm + final during same exam period)
4. Classrooms are generic (no special equipment requirements)
5. All exams have same duration (fit in one time slot)
6. Students are physically able to take 2 exams per day (no disability accommodations modeled)
7. Desktop application for single user (Student Affairs staff person), not multi-user
8. Windows OS is primary target (but Java ensures cross-platform compatibility)
9. Exam period is contiguous (no gaps, e.g., no exams on weekends if those days aren't configured)
10. Consecutive = immediately following slot number (whether same day or across day boundary)

**Open questions:**
- [To be filled in during development as questions arise]

---

**END OF MEMORY BANK**

---

This memory bank should be treated as a living document. Update it as:
- Decisions are made
- Requirements are clarified
- Implementation details are finalized
- Issues are discovered and resolved

All team members should review this document at start of project and reference it throughout development.
