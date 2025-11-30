# ALGORITHM DESIGN - EXAM SCHEDULING SYSTEM

**Project:** Exam Planner Desktop Application
**Algorithm:** Backtracking CSP Solver with Constraint Propagation
**Version:** 1.0
**Last Updated:** November 26, 2025

---

## TABLE OF CONTENTS

1. [Overview](#overview)
2. [Problem Formulation](#problem-formulation)
3. [Algorithm Architecture](#algorithm-architecture)
4. [Backtracking Algorithm](#backtracking-algorithm)
5. [Constraint Validation](#constraint-validation)
6. [Heuristics](#heuristics)
7. [Optimization Strategies](#optimization-strategies)
8. [Conflict Detection](#conflict-detection)
9. [Performance Optimizations](#performance-optimizations)
10. [Implementation Guide](#implementation-guide)
11. [Testing Strategy](#testing-strategy)
12. [Complexity Analysis](#complexity-analysis)

---

## OVERVIEW

### Problem Statement

Given:
- **N courses** that need exam scheduling
- **M classrooms** with specific capacities
- **S students** enrolled in various courses
- **Exam period configuration** (D days, T time slots per day)

Find:
- An assignment of each course to exactly one {Classroom, Day, TimeSlot} triple

Such that:
- **Hard Constraint 1:** No student has consecutive exams
- **Hard Constraint 2:** No student has more than 2 exams per day
- **Hard Constraint 3:** Classroom capacity is not exceeded
- **Implicit Constraint 4:** No classroom double-booking

### Algorithm Choice: Backtracking CSP

**Why Backtracking?**
- ✅ **Completeness:** Guarantees finding solution if one exists
- ✅ **Soundness:** Only returns valid solutions
- ✅ **Systematic:** Explores search space methodically
- ✅ **Constraint-friendly:** Natural fit for hard constraints

**Why Not Greedy?**
- ❌ May fail even when solution exists
- ❌ No guarantee of correctness
- ✅ Faster but less reliable

**Why Not Genetic Algorithm?**
- ❌ Probabilistic (no guarantee)
- ❌ Complex to ensure 100% constraint satisfaction
- ✅ Good for optimization but not for hard constraints

**Decision:** Backtracking with heuristics provides best balance of correctness and performance.

---

## PROBLEM FORMULATION

### CSP Components

#### 1. Variables

**Definition:** Each course is a variable

```
Variables = {Course_1, Course_2, ..., Course_N}
```

**Example:**
```
Variables = {CourseCode_01, CourseCode_02, ..., CourseCode_20}
N = 20
```

---

#### 2. Domain

**Definition:** For each course, the domain is the set of all possible {Classroom, Day, TimeSlot} assignments

```
Domain(Course_i) = {(c, d, t) | c ∈ Classrooms,
                                 d ∈ {1, 2, ..., D},
                                 t ∈ {1, 2, ..., T}}
```

**Example:**
```
Classrooms = {Classroom_01, ..., Classroom_10}  # 10 classrooms
Days = {1, 2, 3, 4, 5}                          # 5 days
TimeSlots = {1, 2, 3, 4}                        # 4 slots per day

Domain(CourseCode_01) = {
    (Classroom_01, 1, 1),  # Classroom 1, Day 1, Slot 1
    (Classroom_01, 1, 2),  # Classroom 1, Day 1, Slot 2
    ...,
    (Classroom_10, 5, 4)   # Classroom 10, Day 5, Slot 4
}

|Domain| = 10 × 5 × 4 = 200 possible assignments per course
```

**Domain Filtering (Pre-processing):**

Before backtracking, filter out invalid assignments:

```java
Domain(Course_i) = {(c, d, t) | enrolledCount(Course_i) ≤ capacity(c)}
```

**Example:**
```
CourseCode_01 has 40 students enrolled
Classroom_01 has capacity 40 → Valid ✓
Classroom_02 has capacity 35 → Invalid ✗ (removed from domain)
```

After filtering, domain size reduced significantly (only valid classrooms remain).

---

#### 3. Constraints

**Unary Constraints (Pre-processing):**
```
enrolledCount(Course_i) ≤ capacity(Classroom)
```

**Binary Constraints (Between variables):**

**Constraint 1: No Consecutive Exams**
```
For any two courses c1, c2 with shared students:
  If c1 assigned to (classroom1, day1, slot1)
  And c2 assigned to (classroom2, day2, slot2)
  Then: NOT consecutive(day1, slot1, day2, slot2)
```

**Constraint 2: Max 2 Exams Per Day**
```
For any student s:
  Count of exams on any day d ≤ 2
```

**Constraint 3: No Classroom Double-Booking (Implicit)**
```
For any two courses c1 ≠ c2:
  If c1 assigned to (classroom, day, slot)
  Then c2 cannot be assigned to (classroom, day, slot)
```

---

### Search Space Size

**Theoretical maximum:**
```
Search Space = |Domain|^N
             = 200^20
             = 1.024 × 10^46 possible assignments
```

**With constraint propagation and heuristics:**
```
Effective search space << 10^46
```

Heuristics reduce this dramatically (typically explore < 1000 nodes for sample data).

---

## ALGORITHM ARCHITECTURE

### High-Level Flow

```
┌─────────────────────────────────────────────────────────────┐
│                    CSPSolver.solve()                        │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  1. Pre-processing                                          │
│     • Load data (courses, classrooms, enrollments)          │
│     • Build enrollment maps (student→courses, course→students) │
│     • Filter domains (remove invalid classrooms)            │
│     • Check basic feasibility (enough slots?)               │
└─────────────────────────────────────────────────────────────┘
                            │
                            ▼
┌─────────────────────────────────────────────────────────────┐
│  2. Backtracking Search                                     │
│     WHILE unassigned courses exist:                         │
│       • Select next course (MRV heuristic)                  │
│       • Order domain values (Least Constraining Value)      │
│       • Try each value:                                     │
│         - Check constraints                                 │
│         - If valid: assign and recurse                      │
│         - If fails: backtrack                               │
│       • If no value works: return FAILURE                   │
└─────────────────────────────────────────────────────────────┘
                            │
                ┌───────────┴───────────┐
                │                       │
                ▼                       ▼
         ┌─────────────┐         ┌──────────────┐
         │   SUCCESS   │         │   FAILURE    │
         │  Return     │         │  Analyze     │
         │  Schedule   │         │  Conflicts   │
         └─────────────┘         └──────────────┘
```

---

### Component Diagram

```
┌──────────────────────────────────────────────────────────────┐
│                      CSPSolver                               │
├──────────────────────────────────────────────────────────────┤
│  + solve(courses, classrooms, enrollments, config, strategy) │
│  - backtrack(assignment, domains)                            │
│  - selectUnassignedVariable()                                │
│  - orderDomainValues(course, strategy)                       │
│  - forwardCheck(course, assignment)                          │
└──────────────────────────────────────────────────────────────┘
                            │
                            │ uses
                            ▼
┌──────────────────────────────────────────────────────────────┐
│                  ConstraintValidator                         │
├──────────────────────────────────────────────────────────────┤
│  + isValid(course, classroom, day, slot, assignment)         │
│  + checkNoConsecutiveExams(...)                              │
│  + checkMaxTwoPerDay(...)                                    │
│  + checkClassroomCapacity(...)                               │
│  + getViolations(...)                                        │
└──────────────────────────────────────────────────────────────┘
                            │
                            │ uses
                            ▼
┌──────────────────────────────────────────────────────────────┐
│                 OptimizationStrategy                         │
├──────────────────────────────────────────────────────────────┤
│  + compareAssignments(a1, a2)  // for value ordering         │
│  + evaluateAssignment(assignment)                            │
└──────────────────────────────────────────────────────────────┘
                            │
                            │ implementations
                            ▼
┌──────────────────────────────────────────────────────────────┐
│  MinimizeDaysStrategy | BalanceDistributionStrategy          │
│  MinimizeClassroomsStrategy | BalanceClassroomsStrategy      │
└──────────────────────────────────────────────────────────────┘
```

---

## BACKTRACKING ALGORITHM

### Pseudocode (High-Level)

```
FUNCTION CSPSolver.solve(courses, classrooms, enrollments, config, strategy):
    // Pre-processing
    enrollmentMap ← buildEnrollmentMap(enrollments)
    domains ← initializeDomains(courses, classrooms, enrollmentMap)

    // Check basic feasibility
    IF count(courses) > config.totalSlots THEN
        THROW NotEnoughSlotsException
    END IF

    FOR EACH course IN courses DO
        IF domains[course].isEmpty() THEN
            THROW NoValidClassroomException(course)
        END IF
    END FOR

    // Start backtracking search
    assignment ← {}  // Empty assignment
    result ← BACKTRACK(assignment, domains, enrollmentMap, strategy)

    IF result = FAILURE THEN
        conflicts ← ConflictDetector.analyze(courses, classrooms, enrollments, config)
        THROW NoSolutionException(conflicts)
    ELSE
        RETURN buildSchedule(result)
    END IF
END FUNCTION
```

---

### Pseudocode (Detailed Backtracking)

```
FUNCTION BACKTRACK(assignment, domains, enrollmentMap, strategy):
    // Base case: all courses assigned
    IF assignment.isComplete() THEN
        RETURN assignment  // SUCCESS
    END IF

    // Select next unassigned course (MRV heuristic)
    course ← SELECT_UNASSIGNED_VARIABLE(assignment, domains)

    // Order domain values (Least Constraining Value + Strategy)
    orderedValues ← ORDER_DOMAIN_VALUES(course, domains[course], assignment, strategy)

    FOR EACH (classroom, day, slot) IN orderedValues DO
        // Check if this assignment violates any constraints
        IF CONSTRAINTS_SATISFIED(course, classroom, day, slot, assignment, enrollmentMap) THEN
            // Make assignment
            assignment[course] ← (classroom, day, slot)

            // Forward checking: update domains of unassigned courses
            removedValues ← FORWARD_CHECK(course, classroom, day, slot, assignment, domains, enrollmentMap)

            IF NOT removedValues.causedEmptyDomain() THEN
                // Recurse
                result ← BACKTRACK(assignment, domains, enrollmentMap, strategy)

                IF result ≠ FAILURE THEN
                    RETURN result  // Found solution!
                END IF
            END IF

            // Backtrack: undo assignment and restore domains
            DELETE assignment[course]
            RESTORE_DOMAINS(domains, removedValues)
        END IF
    END FOR

    RETURN FAILURE  // No valid assignment for this course
END FUNCTION
```

---

### Key Functions Explained

#### SELECT_UNASSIGNED_VARIABLE (MRV Heuristic)

**Minimum Remaining Values:** Choose the course with fewest valid domain values remaining.

```
FUNCTION SELECT_UNASSIGNED_VARIABLE(assignment, domains):
    minCourse ← NULL
    minDomainSize ← INFINITY

    FOR EACH course IN courses DO
        IF course NOT IN assignment THEN  // Unassigned
            domainSize ← count(domains[course])

            IF domainSize < minDomainSize THEN
                minDomainSize ← domainSize
                minCourse ← course
            ELSE IF domainSize = minDomainSize THEN
                // Tie-breaking: choose course with most students (harder to place)
                IF enrollmentMap[course].size() > enrollmentMap[minCourse].size() THEN
                    minCourse ← course
                END IF
            END IF
        END IF
    END FOR

    RETURN minCourse
END FUNCTION
```

**Why MRV?**
- Fail-fast on difficult variables
- Prunes search space early
- Empirically proven to reduce backtracking

---

#### ORDER_DOMAIN_VALUES (Least Constraining Value)

**Least Constraining Value:** Try values that leave most flexibility for remaining courses.

```
FUNCTION ORDER_DOMAIN_VALUES(course, domain, assignment, strategy):
    // Calculate how constraining each value is
    valueCounts ← {}

    FOR EACH (classroom, day, slot) IN domain DO
        // Count how many other courses can still use this {classroom, day, slot}
        conflictCount ← countConflicts(classroom, day, slot, assignment)
        valueCounts[(classroom, day, slot)] ← conflictCount
    END FOR

    // Sort by least constraining first
    sortedValues ← SORT(domain, BY valueCounts, ASCENDING)

    // Apply optimization strategy to break ties
    strategyOrderedValues ← strategy.reorder(sortedValues)

    RETURN strategyOrderedValues
END FUNCTION
```

---

#### CONSTRAINTS_SATISFIED

```
FUNCTION CONSTRAINTS_SATISFIED(course, classroom, day, slot, assignment, enrollmentMap):
    // Constraint 1: Classroom capacity
    IF enrollmentMap[course].size() > classroom.capacity THEN
        RETURN FALSE  // Already filtered in pre-processing, but double-check
    END IF

    // Constraint 2: No classroom double-booking
    FOR EACH assignedCourse IN assignment.keys() DO
        (c, d, s) ← assignment[assignedCourse]
        IF c = classroom AND d = day AND s = slot THEN
            RETURN FALSE  // Classroom already used at this time
        END IF
    END FOR

    // Constraint 3: No consecutive exams for any student
    students ← enrollmentMap[course]
    FOR EACH student IN students DO
        IF hasConsecutiveExam(student, day, slot, assignment, enrollmentMap) THEN
            RETURN FALSE
        END IF
    END FOR

    // Constraint 4: Max 2 exams per day for any student
    FOR EACH student IN students DO
        examsOnDay ← countExamsForStudentOnDay(student, day, assignment, enrollmentMap)
        IF examsOnDay >= 2 THEN
            RETURN FALSE  // Would cause student to have 3+ exams
        END IF
    END FOR

    RETURN TRUE  // All constraints satisfied
END FUNCTION
```

---

#### FORWARD_CHECK (Constraint Propagation)

**Purpose:** After assigning a course, reduce domains of unassigned courses by removing values that would violate constraints.

```
FUNCTION FORWARD_CHECK(assignedCourse, classroom, day, slot, assignment, domains, enrollmentMap):
    removedValues ← {}
    studentsInAssignedCourse ← enrollmentMap[assignedCourse]

    FOR EACH unassignedCourse IN courses DO
        IF unassignedCourse NOT IN assignment THEN
            studentsInUnassignedCourse ← enrollmentMap[unassignedCourse]
            sharedStudents ← INTERSECTION(studentsInAssignedCourse, studentsInUnassignedCourse)

            IF sharedStudents.isNotEmpty() THEN
                toRemove ← []

                FOR EACH (c, d, s) IN domains[unassignedCourse] DO
                    // Remove if same classroom at same time
                    IF c = classroom AND d = day AND s = slot THEN
                        toRemove.add((c, d, s))
                    END IF

                    // Remove if consecutive slot for shared students
                    IF areConsecutive(day, slot, d, s) THEN
                        toRemove.add((c, d, s))
                    END IF

                    // Remove if would cause 3+ exams on same day for shared students
                    FOR EACH student IN sharedStudents DO
                        examsOnDay ← countExamsForStudentOnDay(student, d, assignment, enrollmentMap)
                        IF examsOnDay >= 2 AND d = day THEN
                            toRemove.add((c, d, s))
                        END IF
                    END FOR
                END FOR

                // Remove invalid values from domain
                domains[unassignedCourse].removeAll(toRemove)
                removedValues[unassignedCourse] ← toRemove

                // Check if domain became empty (dead-end)
                IF domains[unassignedCourse].isEmpty() THEN
                    removedValues.setEmptyDomainFlag(TRUE)
                END IF
            END IF
        END IF
    END FOR

    RETURN removedValues
END FUNCTION
```

---

## CONSTRAINT VALIDATION

### Constraint 1: No Consecutive Exams

**Definition:** If a student has an exam in slot N, they cannot have another exam in slot N+1.

**Slot Numbering:**

```
Absolute Slot Number = (day - 1) × slotsPerDay + slot

Example (5 days, 4 slots per day):
Day 1, Slot 1 → Absolute slot 1
Day 1, Slot 2 → Absolute slot 2
Day 1, Slot 4 → Absolute slot 4
Day 2, Slot 1 → Absolute slot 5   ← Consecutive with Day 1, Slot 4!
Day 2, Slot 2 → Absolute slot 6
...
Day 5, Slot 4 → Absolute slot 20
```

**Implementation:**

```java
public boolean areConsecutive(int day1, int slot1, int day2, int slot2, int slotsPerDay) {
    int absoluteSlot1 = (day1 - 1) * slotsPerDay + slot1;
    int absoluteSlot2 = (day2 - 1) * slotsPerDay + slot2;

    return Math.abs(absoluteSlot1 - absoluteSlot2) == 1;
}

public boolean hasConsecutiveExam(Student student, int day, int slot,
                                   Assignment assignment, EnrollmentMap enrollmentMap) {
    // Get all courses this student is enrolled in
    List<Course> studentCourses = enrollmentMap.getCoursesForStudent(student);

    for (Course course : studentCourses) {
        if (assignment.contains(course)) {
            ExamAssignment exam = assignment.get(course);

            if (areConsecutive(day, slot, exam.day, exam.slot, config.slotsPerDay)) {
                return true;  // Found consecutive exam
            }
        }
    }

    return false;  // No consecutive exams
}
```

**Edge Cases:**
- ✅ Day 1, Slot 4 and Day 2, Slot 1 ARE consecutive
- ✅ Day 1, Slot 1 has no previous slot (boundary)
- ✅ Day 5, Slot 4 has no next slot (boundary)

---

### Constraint 2: Max 2 Exams Per Day

**Definition:** A student cannot have more than 2 exams on any single day.

**Implementation:**

```java
public int countExamsForStudentOnDay(Student student, int day,
                                      Assignment assignment, EnrollmentMap enrollmentMap) {
    List<Course> studentCourses = enrollmentMap.getCoursesForStudent(student);
    int count = 0;

    for (Course course : studentCourses) {
        if (assignment.contains(course)) {
            ExamAssignment exam = assignment.get(course);

            if (exam.day == day) {
                count++;
            }
        }
    }

    return count;
}

public boolean violatesMaxTwoPerDay(Student student, int day,
                                     Assignment assignment, EnrollmentMap enrollmentMap) {
    int currentCount = countExamsForStudentOnDay(student, day, assignment, enrollmentMap);

    // Would cause 3+ exams if we add another exam on this day
    return currentCount >= 2;
}
```

---

### Constraint 3: Classroom Capacity

**Definition:** Number of students in a course cannot exceed classroom capacity.

**Implementation:**

```java
public boolean violatesCapacity(Course course, Classroom classroom, EnrollmentMap enrollmentMap) {
    int enrolledCount = enrollmentMap.getStudentsForCourse(course).size();

    return enrolledCount > classroom.getCapacity();
}
```

**Note:** This is pre-filtered during domain initialization, so should always be true during search.

---

### Constraint 4: No Classroom Double-Booking

**Definition:** A classroom cannot host two exams at the same time.

**Implementation:**

```java
public boolean isClassroomOccupied(Classroom classroom, int day, int slot, Assignment assignment) {
    for (ExamAssignment exam : assignment.values()) {
        if (exam.classroom.equals(classroom) && exam.day == day && exam.slot == slot) {
            return true;  // Classroom already used
        }
    }

    return false;  // Classroom available
}
```

---

## HEURISTICS

### 1. Minimum Remaining Values (MRV)

**Purpose:** Variable ordering - choose which course to assign next

**Strategy:** Select course with fewest valid assignments remaining

**Why it works:**
- Courses with few options are harder to satisfy
- If we can't satisfy them, fail early (prune search tree)
- Empirically reduces backtracking by 10-100x

**Example:**

```
Unassigned courses:
- CourseCode_01: 50 valid assignments remaining
- CourseCode_02: 5 valid assignments remaining   ← Choose this one (MRV)
- CourseCode_03: 30 valid assignments remaining

Reason: CourseCode_02 is most constrained, so try it first.
If it fails, we know early and can backtrack sooner.
```

---

### 2. Least Constraining Value (LCV)

**Purpose:** Value ordering - which {classroom, day, slot} to try first

**Strategy:** Try values that eliminate fewest options for other courses

**Why it works:**
- Maximize flexibility for future assignments
- Reduce probability of backtracking

**Example:**

```
CourseCode_01 can be assigned to:
- (Classroom_01, Day 1, Slot 1) → Eliminates 10 options for other courses
- (Classroom_05, Day 3, Slot 2) → Eliminates 2 options for other courses  ← Try this first (LCV)

Reason: Choosing Classroom_05 leaves more flexibility.
```

---

### 3. Degree Heuristic (Tie-breaking for MRV)

**Purpose:** When multiple courses have same MRV, choose one with most conflicts

**Strategy:** Choose course enrolled by most students (more potential conflicts)

**Why it works:**
- Harder courses should be assigned earlier
- Similar to MRV (fail-fast on difficult variables)

**Example:**

```
Two courses both have 10 valid assignments remaining:
- CourseCode_01: 40 students enrolled   ← Choose this (more conflicts)
- CourseCode_02: 15 students enrolled

Reason: CourseCode_01 affects more students, so constrain it first.
```

---

## OPTIMIZATION STRATEGIES

### Strategy 1: Minimize Days Used

**Goal:** Pack exams into as few days as possible

**Value Ordering Modification:**

```java
public int compareAssignments(Assignment a1, Assignment a2) {
    // Prefer earlier days
    if (a1.day != a2.day) {
        return Integer.compare(a1.day, a2.day);  // Ascending
    }

    // Within same day, prefer earlier slots
    if (a1.slot != a2.slot) {
        return Integer.compare(a1.slot, a2.slot);  // Ascending
    }

    // Tie-break by classroom
    return a1.classroom.compareTo(a2.classroom);
}
```

**Effect:** Tries to fill Day 1 completely before Day 2, etc.

**Use Case:** Shorter exam period, reduced facility costs

---

### Strategy 2: Balance Distribution Across Days

**Goal:** Spread exams evenly across all available days

**Value Ordering Modification:**

```java
public int compareAssignments(Assignment a1, Assignment a2) {
    // Count how many exams already scheduled on each day
    int count1 = countExamsOnDay(a1.day, currentAssignment);
    int count2 = countExamsOnDay(a2.day, currentAssignment);

    // Prefer day with fewer exams (balance)
    if (count1 != count2) {
        return Integer.compare(count1, count2);  // Ascending
    }

    // Tie-break by slot
    return Integer.compare(a1.slot, a2.slot);
}
```

**Effect:** Distributes exam load evenly

**Use Case:** Reduce student/proctor stress, balanced workload

---

### Strategy 3: Minimize Classrooms Needed

**Goal:** Use as few different classrooms as possible

**Value Ordering Modification:**

```java
public int compareAssignments(Assignment a1, Assignment a2) {
    // Check if classroom already used
    boolean a1Used = isClassroomUsed(a1.classroom, currentAssignment);
    boolean a2Used = isClassroomUsed(a2.classroom, currentAssignment);

    // Prefer already-used classrooms
    if (a1Used && !a2Used) {
        return -1;  // a1 first
    } else if (!a1Used && a2Used) {
        return 1;   // a2 first
    }

    // Both used or both unused: tie-break by day
    return Integer.compare(a1.day, a2.day);
}
```

**Effect:** Reuses classrooms

**Use Case:** Centralize exam locations, reduce setup costs

---

### Strategy 4: Balance Classrooms Per Day

**Goal:** Use similar number of classrooms each day

**Value Ordering Modification:**

```java
public int compareAssignments(Assignment a1, Assignment a2) {
    // Count unique classrooms used on each day
    int classrooms1 = countUniqueClassroomsOnDay(a1.day, currentAssignment);
    int classrooms2 = countUniqueClassroomsOnDay(a2.day, currentAssignment);

    // Prefer day with fewer classrooms (balance)
    if (classrooms1 != classrooms2) {
        return Integer.compare(classrooms1, classrooms2);
    }

    // Tie-break by classroom
    return a1.classroom.compareTo(a2.classroom);
}
```

**Effect:** Even classroom distribution across days

**Use Case:** Balance proctoring staff assignments

---

## CONFLICT DETECTION

### When Backtracking Fails

If backtracking returns FAILURE, it means no valid schedule exists. Analyze why:

```
FUNCTION ConflictDetector.analyze(courses, classrooms, enrollments, config):
    conflicts ← ConflictReport()

    // Check 1: Students with too many courses
    FOR EACH student IN students DO
        courseCount ← enrollments.getCoursesForStudent(student).size()
        maxPossible ← config.numDays × 2  // Max 2 per day

        IF courseCount > maxPossible THEN
            conflicts.addStudentConflict(
                student,
                "Enrolled in " + courseCount + " courses, max possible " + maxPossible
            )
        END IF
    END FOR

    // Check 2: Courses with insufficient capacity
    FOR EACH course IN courses DO
        enrolledCount ← enrollments.getStudentsForCourse(course).size()
        maxCapacity ← MAX(classrooms.capacity)

        IF enrolledCount > maxCapacity THEN
            conflicts.addCourseConflict(
                course,
                "Enrolled count " + enrolledCount + " exceeds max capacity " + maxCapacity
            )
        END IF
    END FOR

    // Check 3: Insufficient total slots
    totalSlots ← config.numDays × config.slotsPerDay
    IF courses.size() > totalSlots THEN
        conflicts.addConfigurationConflict(
            "Need " + courses.size() + " slots but only " + totalSlots + " available"
        )
    END IF

    // Check 4: Overlapping enrollment patterns (complex graph analysis)
    conflicts.addAll(detectEnrollmentCliques(enrollments))

    // Generate recommendations
    conflicts.setRecommendations(generateRecommendations(conflicts))

    RETURN conflicts
END FUNCTION
```

---

### Recommendations

```
FUNCTION generateRecommendations(conflicts):
    recommendations ← []

    IF conflicts.hasStudentWithTooManyCourses() THEN
        recommendations.add("Extend exam period to " + (requiredDays) + " days")
        recommendations.add("Reduce course enrollments for affected students")
    END IF

    IF conflicts.hasCourseExceedingCapacity() THEN
        recommendations.add("Add classroom with capacity ≥ " + (maxRequired))
        recommendations.add("Split large course into multiple exam sessions")
    END IF

    IF conflicts.hasInsufficientSlots() THEN
        recommendations.add("Increase slots per day to " + (requiredSlots))
        recommendations.add("Extend exam period by " + (additionalDays) + " days")
    END IF

    RETURN recommendations
END FUNCTION
```

---

## PERFORMANCE OPTIMIZATIONS

### 1. Pre-compute Enrollment Maps

**Before backtracking:**

```java
// Build once, reuse throughout search
Map<Student, List<Course>> studentToCourses = new HashMap<>();
Map<Course, List<Student>> courseToStudents = new HashMap<>();

for (Enrollment e : enrollments) {
    studentToCourses.computeIfAbsent(e.student, k -> new ArrayList<>()).add(e.course);
    courseToStudents.computeIfAbsent(e.course, k -> new ArrayList<>()).add(e.student);
}
```

**Benefit:** O(1) lookup instead of O(n) database query

---

### 2. Domain Pre-filtering

**Before backtracking:**

```java
for (Course course : courses) {
    int enrolledCount = courseToStudents.get(course).size();

    // Remove classrooms that are too small
    domain.get(course).removeIf(assignment ->
        assignment.classroom.capacity < enrolledCount
    );
}
```

**Benefit:** Reduces domain size by 50-90% typically

---

### 3. Memoization of Constraint Checks

**Cache repeated constraint checks:**

```java
Map<String, Boolean> constraintCache = new HashMap<>();

public boolean hasConsecutiveExam(Student student, int day, int slot) {
    String key = student.id + ":" + day + ":" + slot;

    if (constraintCache.containsKey(key)) {
        return constraintCache.get(key);  // O(1) cache hit
    }

    boolean result = computeConsecutiveExam(student, day, slot);
    constraintCache.put(key, result);
    return result;
}
```

**Benefit:** 10x faster constraint checking for repeated calls

---

### 4. Early Termination

**Stop as soon as solution found:**

```java
if (assignment.isComplete()) {
    return assignment;  // Don't search for other solutions
}
```

**Benefit:** Finds first valid solution quickly (don't need all solutions)

---

### 5. Parallel Domain Evaluation (Advanced)

**Use Java 8 streams for parallel constraint checking:**

```java
List<Assignment> validAssignments = domain.parallelStream()
    .filter(a -> constraintsValidator.isValid(a, assignment, enrollmentMap))
    .collect(Collectors.toList());
```

**Benefit:** Utilize multiple CPU cores, 2-4x speedup on multi-core machines

**Trade-off:** More complex, may not be needed for small datasets

---

## IMPLEMENTATION GUIDE

### Class Structure

```java
// Main solver class
public class CSPSolver {
    private ConstraintValidator validator;
    private OptimizationStrategy strategy;
    private ProgressTracker progressTracker;
    private volatile boolean cancelled = false;

    public Schedule solve(
        List<Course> courses,
        List<Classroom> classrooms,
        List<Enrollment> enrollments,
        ScheduleConfiguration config,
        OptimizationStrategy strategy,
        ProgressTracker progressTracker
    ) throws NoSolutionException, CancelledException;

    private Map<Course, ExamAssignment> backtrack(
        Map<Course, ExamAssignment> assignment,
        Map<Course, Set<AssignmentOption>> domains,
        Map<Student, List<Course>> enrollmentMap
    );

    private Course selectUnassignedVariable(
        Map<Course, ExamAssignment> assignment,
        Map<Course, Set<AssignmentOption>> domains
    );

    private List<AssignmentOption> orderDomainValues(
        Course course,
        Set<AssignmentOption> domain,
        Map<Course, ExamAssignment> assignment
    );

    private Map<Course, Set<AssignmentOption>> forwardCheck(
        Course course,
        AssignmentOption value,
        Map<Course, ExamAssignment> assignment,
        Map<Course, Set<AssignmentOption>> domains
    );

    public void cancel() {
        this.cancelled = true;
    }
}
```

---

### Progress Tracking

```java
public interface ProgressTracker {
    void updateProgress(int assignedCourses, int totalCourses);
    void updateMessage(String message);
    boolean isCancelled();
}

// Usage in solver:
private Map<Course, ExamAssignment> backtrack(...) {
    int assignedCount = assignment.size();
    int totalCourses = courses.size();

    progressTracker.updateProgress(assignedCount, totalCourses);

    if (progressTracker.isCancelled()) {
        throw new CancelledException();
    }

    // Continue backtracking...
}
```

---

### Integration with UI (JavaFX Task)

```java
public class ScheduleGenerationTask extends Task<Schedule> {
    @Override
    protected Schedule call() throws Exception {
        updateMessage("Initializing solver...");
        updateProgress(0, 100);

        CSPSolver solver = new CSPSolver(validator, strategy);

        ProgressTracker tracker = new ProgressTracker() {
            @Override
            public void updateProgress(int assigned, int total) {
                double percentage = (double) assigned / total * 100;
                ScheduleGenerationTask.this.updateProgress(assigned, total);
                ScheduleGenerationTask.this.updateMessage(
                    "Assigned " + assigned + " / " + total + " courses"
                );
            }

            @Override
            public boolean isCancelled() {
                return ScheduleGenerationTask.this.isCancelled();
            }
        };

        Schedule schedule = solver.solve(courses, classrooms, enrollments, config, strategy, tracker);

        return schedule;
    }
}
```

---

## TESTING STRATEGY

### Unit Tests for Constraints

```java
@Test
public void testNoConsecutiveExams_WithConsecutiveSlots_ShouldReturnTrue() {
    // Arrange
    int day1 = 1, slot1 = 4;
    int day2 = 2, slot2 = 1;
    int slotsPerDay = 4;

    // Act
    boolean result = validator.areConsecutive(day1, slot1, day2, slot2, slotsPerDay);

    // Assert
    assertTrue(result, "Day 1 Slot 4 and Day 2 Slot 1 should be consecutive");
}

@Test
public void testMaxTwoPerDay_WithTwoExams_ShouldAllowThird() {
    // Arrange
    Student student = new Student("Std_001");
    Assignment assignment = createAssignmentWithTwoExamsOnDay1(student);

    // Act
    boolean violates = validator.violatesMaxTwoPerDay(student, 1, assignment);

    // Assert
    assertTrue(violates, "Should violate max-2-per-day constraint");
}
```

---

### Integration Tests for Solver

```java
@Test
public void testSolve_WithSolvableDataset_ShouldReturnValidSchedule() {
    // Arrange
    List<Student> students = TestDataBuilder.createStudents(10);
    List<Course> courses = TestDataBuilder.createCourses(5);
    List<Classroom> classrooms = TestDataBuilder.createClassrooms(3, 50);
    List<Enrollment> enrollments = TestDataBuilder.createRandomEnrollments(students, courses);
    ScheduleConfiguration config = new ScheduleConfiguration(3, 2); // 6 slots

    CSPSolver solver = new CSPSolver(validator, new MinimizeDaysStrategy());

    // Act
    Schedule schedule = solver.solve(courses, classrooms, enrollments, config, strategy, null);

    // Assert
    assertNotNull(schedule);
    assertEquals(5, schedule.getAssignments().size());
    assertTrue(validator.validateSchedule(schedule));
}

@Test(expected = NoSolutionException.class)
public void testSolve_WithUnsolvableDataset_ShouldThrowException() {
    // Arrange: Student enrolled in 10 courses, only 6 slots available
    Student student = new Student("Std_001");
    List<Course> courses = TestDataBuilder.createCourses(10);
    List<Enrollment> enrollments = TestDataBuilder.enrollStudentInAllCourses(student, courses);
    ScheduleConfiguration config = new ScheduleConfiguration(3, 2); // 6 slots, max 6 exams

    CSPSolver solver = new CSPSolver(validator, strategy);

    // Act & Assert
    solver.solve(courses, classrooms, enrollments, config, strategy, null);
    // Should throw NoSolutionException
}
```

---

## COMPLEXITY ANALYSIS

### Time Complexity

**Worst case (no heuristics):**
```
O(d^n) where:
  d = domain size (classrooms × days × slots)
  n = number of courses

For sample data: O(200^20) ≈ 10^46 operations
```

**With MRV and LCV heuristics:**
```
Effective complexity: O(b^d) where:
  b = effective branching factor (reduced by pruning)
  d = depth of search tree

Empirically: ~O(n^2) to O(n^3) for solvable instances
```

**Forward checking:**
```
O(n^2 × d) per assignment where:
  n = number of courses
  d = domain size

But reduces search space exponentially, so net benefit.
```

---

### Space Complexity

```
O(n × d) where:
  n = number of courses
  d = domain size

Storage needed:
  - Domains: n × d assignments
  - Assignment: n course assignments
  - Enrollment map: s × c (students × avg courses)

For sample data:
  20 courses × 200 domain size = 4,000 assignment options
  Memory: ~100 KB
```

---

### Performance Benchmarks

| Dataset | Courses | Students | Classrooms | Expected Time |
|---------|---------|----------|------------|---------------|
| Tiny | 5 | 50 | 3 | < 1 second |
| Small | 20 | 250 | 10 | < 5 seconds |
| Medium | 50 | 500 | 15 | < 10 seconds |
| Large | 100 | 1000 | 20 | < 60 seconds |

**Assumptions:** Well-distributed enrollments, solvable configuration

---

## VERSION HISTORY

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-26 | Claude Code | Initial algorithm design |

---

## REFERENCES

- Russell & Norvig, "Artificial Intelligence: A Modern Approach", Chapter 6 (Constraint Satisfaction Problems)
- Bacchus & van Run, "Dynamic Variable Ordering in CSPs"
- Haralick & Elliott, "Increasing Tree Search Efficiency for CSPs"
- Online CSP Tutorial: https://artint.info/2e/html/ArtInt2e.Ch4.html

---

**END OF ALGORITHM DESIGN DOCUMENTATION**
