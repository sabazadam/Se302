# TEAM ASSIGNMENTS - EXAM SCHEDULING SYSTEM

**Project:** Exam Planner Desktop Application
**Course:** SE302 - Software Engineering
**Team:** Team 3, Section 1
**Date:** November 26, 2025

---

## TEAM ROSTER

| Name | Student ID | Primary Role | Secondary Responsibilities |
|------|-----------|--------------|---------------------------|
| **Kerem Bozdag** | 20200602012 | Database & Data Layer Lead | Data validation, CSV import/export |
| **Milena Larissa Ünal** | 20230602107 | Algorithm Specialist | CSP solver, constraint validation, optimization |
| **Rabia Tülay Gokdag** | 20210602029 | UI/FXML Specialist | JavaFX views, FXML layouts, Scene Builder |
| **Mehmet Sefa Keskin** | 20230602047 | Controller Layer Lead | MVC controllers, UI-business integration |
| **Feyza Gereme** | 20230602028 | Testing & QA Lead | JUnit tests, integration testing, CI/CD |
| **Emin Arslan** | 20230602007 | Documentation & Integration Lead | JavaDoc, user manual, code reviews |

---

## DETAILED ROLE RESPONSIBILITIES

### 1. Kerem Bozdag - Database & Data Layer Lead

#### Primary Responsibilities

**Database Design & Implementation:**
- Design and implement SQLite database schema
- Create ER diagrams for all entities and relationships
- Write SQL schema creation script (`schema.sql`)
- Define all tables with proper constraints (foreign keys, uniqueness, indexes)
- Optimize database performance with appropriate indexes

**DAO Layer (Data Access Objects):**
- Implement all DAO classes:
  - `StudentDAO` - CRUD operations for students
  - `CourseDAO` - CRUD operations for courses
  - `ClassroomDAO` - CRUD operations for classrooms
  - `EnrollmentDAO` - Manage student-course relationships
  - `ScheduleDAO` - Save, load, list, delete schedules
  - `ExamAssignmentDAO` - Manage exam assignments
- Implement batch operations for performance (bulk inserts)
- Write transaction management utility (`TransactionManager`)
- Create database connection pool/singleton (`DatabaseConnection`)

**CSV Import/Export:**
- Implement `CSVParser` utility class
- Create `ImportService` for all 4 CSV file types:
  1. Student IDs
  2. Course codes
  3. Classroom capacities (format: `ClassroomID;Capacity`)
  4. Enrollment/attendance lists
- Implement data validation during import:
  - Duplicate detection
  - Format validation
  - Referential integrity checks
- Create detailed error reporting with line numbers
- Implement CSV export functionality for schedule views

**Data Validation:**
- Validate all user inputs before database insertion
- Ensure referential integrity (enrollments reference valid students/courses)
- Implement constraint checks at database level (CHECK constraints)
- Create validation utility methods used by service layer

#### Testing Responsibilities
- Write unit tests for all DAO methods (target: >90% coverage)
- Test batch operations for correctness and performance
- Test transaction rollback on errors
- Test duplicate detection
- Create test CSV files (valid and invalid cases)
- Integration testing for CSV import workflow

#### Collaboration Points
- **With Milena:** Provide efficient query methods for CSP solver (enrollment lookups, student-course mappings)
- **With Sefa:** Design clean DAO interfaces for controller layer to use
- **With Feyza:** Create testable DAO methods, support integration testing
- **With Emin:** Document database schema, DAO API, CSV formats

#### Deliverables
- Week 1: Database schema script and ER diagram
- Week 2: All DAO classes with unit tests, CSV import working
- Week 3: Schedule and exam assignment persistence
- Week 7: CSV export functionality
- Week 9: Database optimization and performance tuning

---

### 2. Milena Larissa Ünal - Algorithm Specialist

#### Primary Responsibilities

**Domain Model Design:**
- Create all POJO (Plain Old Java Object) classes:
  - `Student` - Student entity
  - `Course` - Course entity
  - `Classroom` - Classroom entity with capacity
  - `Enrollment` - Student-course relationship
  - `Schedule` - Schedule metadata
  - `ExamAssignment` - Course assignment to {classroom, day, slot}
  - `ScheduleConfiguration` - Exam period configuration (days, slots, strategy)
- Ensure proper encapsulation, getters/setters, equals/hashCode, toString

**Constraint Validation:**
- Implement `ConstraintValidator` class with methods for each hard constraint:
  1. **No consecutive exams:** `checkNoConsecutiveExams(student, day, slot, assignment)`
  2. **Max 2 exams per day:** `checkMaxTwoExamsPerDay(student, day, assignment)`
  3. **Classroom capacity:** `checkClassroomCapacity(course, classroom)`
- Create detailed constraint violation messages
- Implement `ValidationResult` class with error details
- Handle edge cases (day boundaries for consecutive constraint)

**CSP Solver Implementation:**
- Implement backtracking algorithm with constraint propagation
- Core algorithm in `CSPSolver` class:
  ```java
  public Map<Course, ExamAssignment> solve(
      List<Course> courses,
      List<Classroom> classrooms,
      List<Enrollment> enrollments,
      ScheduleConfiguration config,
      OptimizationStrategy strategy,
      ProgressTracker progressTracker
  ) throws NoSolutionException, CancelledException
  ```
- Implement variable ordering heuristic (Minimum Remaining Values - MRV)
- Implement value ordering heuristic (Least Constraining Value)
- Implement forward checking for constraint propagation
- Add progress tracking callback mechanism
- Implement cancellation support (check flag during backtracking)

**Optimization Strategies:**
- Create `OptimizationStrategy` interface
- Implement 4 concrete strategies:
  1. `MinimizeDaysStrategy` - Pack exams into fewest days
  2. `BalanceDistributionStrategy` - Spread exams evenly across days
  3. `MinimizeClassroomsStrategy` - Use fewest classrooms
  4. `BalanceClassroomsStrategy` - Balance classroom usage per day
- Integrate strategies with value ordering heuristic
- Document when to use each strategy

**Conflict Detection:**
- Implement `ConflictDetector` class
- Analyze why no solution exists when solver fails:
  - Students with too many courses for available slots
  - Courses with more students than max classroom capacity
  - Overlapping enrollment patterns creating conflicts
- Generate `ConflictReport` with:
  - List of students causing conflicts
  - List of courses causing conflicts
  - Specific recommendations (extend period, add classrooms, reduce enrollments)

**Performance Optimization:**
- Pre-compute enrollment mappings (student→courses, course→students)
- Implement domain pre-filtering (eliminate impossible assignments)
- Add memoization for repeated constraint checks
- Use efficient data structures (HashSet, HashMap for fast lookups)
- Optimize for small-medium datasets (20-50 courses, 250-500 students)

#### Testing Responsibilities
- Write unit tests for each constraint validator (>95% coverage target)
- Create test cases with known solutions (3-5 courses with obvious valid schedule)
- Create test cases with known conflicts (unsolvable scenarios)
- Test all 4 optimization strategies produce different results
- Performance testing:
  - Small dataset (50 courses, 500 students): <10 seconds ✓
  - Sample dataset (20 courses, 250 students): <5 seconds ✓
- Test edge cases:
  - Single course
  - All students in one course
  - Consecutive slots at day boundaries
  - Maximum enrollments per student

#### Collaboration Points
- **With Kerem:** Use DAO queries efficiently, understand data structure
- **With Sefa:** Design `ScheduleService` API for controllers to call
- **With Feyza:** Make algorithm testable, provide test data generators
- **With Emin:** Document algorithm design, create flowcharts

#### Deliverables
- Week 1: Algorithm design document and pseudocode
- Week 3: Domain models and constraint validators (fully tested)
- Week 4: Working CSP solver for simple cases (3-5 courses)
- Week 5: All optimization strategies, solver handles sample data (<10 sec)
- Week 5: Conflict detector for unsolvable cases
- Week 9: Performance optimization and final algorithm polish

---

### 3. Rabia Tülay Gokdag - UI/FXML Specialist

#### Primary Responsibilities

**UI Design & Wireframing:**
- Design wireframes for all application screens
- Create color scheme and visual design guidelines
- Design application icon and branding
- Ensure consistent UI/UX across all views
- Plan responsive layouts (different screen resolutions)

**FXML Layout Creation:**
Create FXML files for all views using Scene Builder:
1. `MainWindow.fxml` - Main application window with navigation
2. `ImportView.fxml` - CSV file import screen
3. `ConfigurationView.fxml` - Exam period configuration
4. `ScheduleView.fxml` - Schedule generation with progress
5. `ClassroomView.fxml` - Classroom schedule view (table)
6. `StudentView.fxml` - Student schedule view (table with search)
7. `CourseView.fxml` - Course schedule view (sortable table)
8. `DayView.fxml` - Day-by-day grid view
9. `ExportView.fxml` - Export options screen
10. `HistoryView.fxml` - Schedule history list
11. Dialogs: Edit dialog, Conflict report dialog, Error dialogs

**UI Components:**
- Design table layouts (columns, headers, alternating rows)
- Create form inputs (text fields, spinners, combo boxes)
- Design buttons with consistent styling
- Add icons and visual feedback (success checkmarks, error icons)
- Implement progress indicators (progress bar, spinner)
- Design filter controls (dropdowns, search boxes)

**Styling (CSS):**
- Create `styles.css` with consistent theme
- Define color palette:
  - Primary: Blue (#2196F3)
  - Success: Green (#4CAF50)
  - Error: Red (#F44336)
  - Neutral: Gray (#9E9E9E)
- Style all UI components (buttons, tables, inputs, labels)
- Add hover effects and focus states
- Ensure accessibility (sufficient color contrast, clear focus indicators)

**Visual Polish:**
- Add icons for all buttons and actions
- Design loading spinners and progress animations
- Create success/error message displays
- Add tooltips for user guidance
- Ensure proper alignment and spacing
- Test on different screen sizes

#### Testing Responsibilities
- Test UI layouts on different screen resolutions
- Verify all FXML files load correctly
- Test keyboard navigation (tab order)
- Verify CSS styling is consistent
- Test accessibility features
- Ensure no UI elements overlap or clip

#### Collaboration Points
- **With Sefa:** Provide FXML files with proper fx:id for controller binding
- **With Emin:** Provide screenshots for documentation, create visual guide
- **With Feyza:** Support TestFX UI testing
- **With all:** Get feedback on UI designs, iterate based on usability

#### Deliverables
- Week 1: Wireframes and visual design guidelines
- Week 2: Import View FXML
- Week 3: Configuration View FXML
- Week 4: Schedule generation view with progress UI
- Week 6: All 4 schedule views (Classroom, Student, Course, Day)
- Week 7: Export View and edit dialog
- Week 8: History View and conflict report display
- Week 9: Final UI polish and consistency check

---

### 4. Mehmet Sefa Keskin - Controller Layer Lead

#### Primary Responsibilities

**Application Setup:**
- Create `Main.java` - JavaFX application entry point
- Set up application lifecycle (start, stop)
- Load main window FXML
- Initialize application resources

**Main Controller:**
- Implement `MainController` for main window
- Handle navigation between views
- Manage view switching logic
- Implement menu bar actions (File, Edit, View, Help)

**Feature Controllers:**
Implement controllers for all views:
1. `ImportController` - CSV file import workflow
   - File browse dialogs (4 file choosers for each CSV type)
   - Call `ImportService` to process files
   - Display import results (success/error messages with line numbers)
   - Handle import errors gracefully
2. `ConfigurationController` - Exam period setup
   - Input validation (positive integers only)
   - Calculate total slots (days × slots per day)
   - Validate sufficient slots for courses
   - Save configuration to database
3. `ScheduleController` - Schedule generation
   - Create JavaFX Task for background solver execution
   - Update progress bar in real-time
   - Handle cancellation (interrupt solver)
   - Display success or failure results
   - Show conflict report on failure
4. `ClassroomViewController`, `StudentViewController`, `CourseViewController`, `DayViewController` - Schedule display
   - Populate tables/grids with schedule data
   - Implement filtering (dropdown filters, search boxes)
   - Implement sorting (sortable table columns)
   - Handle view switching
5. `ExportController` - Export workflow
   - Format selection (CSV or Excel radio buttons)
   - View selection (checkboxes for which views to export)
   - File save dialog
   - Call export service
   - Display export success/error messages
6. `HistoryController` - Schedule history
   - Load schedule list from database
   - Display schedule metadata (date, strategy, counts)
   - Load selected schedule
   - Delete schedules with confirmation dialog

**Business Logic Integration:**
- Connect controllers to service layer (ImportService, ScheduleService, ExportService)
- Handle service exceptions and display user-friendly errors
- Manage application state (current schedule, configuration)
- Implement proper resource cleanup (close database connections)

**Error Handling:**
- Catch all exceptions from service/DAO layers
- Convert technical errors to user-friendly messages
- Display errors in dialog boxes or status labels
- Log errors for debugging

**Threading:**
- Use JavaFX Task for long-running operations (solver, import, export)
- Keep UI responsive during background tasks
- Update UI from background threads safely (Platform.runLater)
- Handle task cancellation properly

#### Testing Responsibilities
- Write unit tests for controller logic (mock service layer)
- Test error handling for all exception cases
- Test threading (background tasks update UI correctly)
- Ensure no memory leaks (resources cleaned up)
- Test with TestFX for UI integration

#### Collaboration Points
- **With Rabia:** Get FXML files with fx:id set, provide feedback on UI design
- **With Kerem/Milena:** Use service layer APIs, handle exceptions
- **With Feyza:** Make controllers testable, support TestFX testing
- **With Emin:** Document controller architecture and event flows

#### Deliverables
- Week 1: Main controller with navigation
- Week 2: Import controller (integrate with ImportService)
- Week 3: Configuration controller
- Week 4: Schedule controller with background task and progress
- Week 6: All 4 view controllers (Classroom, Student, Course, Day)
- Week 7: Export controller and edit dialog controller
- Week 8: History controller
- Week 9: Final controller polish and error handling improvements

---

### 5. Feyza Gereme - Testing & QA Lead

#### Primary Responsibilities

**Test Infrastructure Setup:**
- Configure JUnit 5 in Maven/Gradle
- Set up test directory structure (`src/test/java`)
- Create test utility classes (`TestDataBuilder`, `TestDatabase`)
- Configure test coverage reporting (JaCoCo)
- Set up CI/CD pipeline (GitHub Actions)

**Unit Testing:**
Write comprehensive unit tests for all layers:
- **DAO Layer Tests:**
  - Test CRUD operations (insert, update, delete, select)
  - Test batch operations
  - Test transaction rollback on error
  - Test uniqueness constraints
  - Test foreign key constraints
  - Target: >90% coverage for DAO layer
- **Algorithm Layer Tests:**
  - Test each constraint validator individually
  - Test CSP solver with known solutions (3-5 courses)
  - Test unsolvable cases (known conflicts)
  - Test all 4 optimization strategies
  - Test edge cases (single course, max enrollments, day boundaries)
  - Target: >95% coverage for algorithm layer
- **Service Layer Tests:**
  - Test business logic validation
  - Test error propagation (DAO → Service → Controller)
  - Mock DAO and algorithm layers using Mockito
  - Test transaction handling
  - Target: >80% coverage for service layer
- **Controller Layer Tests:**
  - Mock service layer
  - Test error handling in controllers
  - Test state management
  - Target: >70% coverage for controller layer

**Integration Testing:**
- **Database Integration:**
  - Test end-to-end import (CSV → Database → Verify)
  - Test schedule save → load round-trip
  - Test manual edit → validation → save
- **UI Integration (TestFX):**
  - Test navigation workflow
  - Test import workflow (select files → import → verify results)
  - Test generation workflow (configure → generate → view schedule)
  - Test export workflow (select format → export → verify file)
- **End-to-End Testing:**
  - Complete user workflows from start to finish
  - Test with sample data (20 courses, 250 students, 10 classrooms)

**Performance Testing:**
- Create benchmark tests for solver performance:
  - Small dataset (50 courses, 500 students): Must complete <10 seconds
  - Sample dataset (20 courses, 250 students): Must complete <5 seconds
- Test database query performance (all queries <100ms)
- Profile application memory usage
- Identify and report performance bottlenecks

**Test Data Management:**
- Create test CSV files:
  - `test_students_10.csv` - 10 students
  - `test_students_100.csv` - 100 students
  - `test_students_250.csv` - 250 students (matches sample)
  - `test_courses_5.csv` - 5 courses
  - `test_courses_20.csv` - 20 courses (matches sample)
  - `test_classrooms_small.csv` - 3 classrooms, capacity 20
  - `test_classrooms_medium.csv` - 10 classrooms, capacity 40
  - `test_enrollments_simple.csv` - Simple enrollment pattern
  - `test_enrollments_complex.csv` - Complex pattern for stress testing
- Create test data builders for easy test object creation
- Maintain test fixtures (consistent test data for repeatable tests)

**CI/CD Pipeline:**
- Set up GitHub Actions workflow:
  - Run all tests on every commit
  - Generate coverage report
  - Upload coverage to Codecov (or similar)
  - Fail build if coverage drops below 70%
  - Fail build if any test fails
- Configure test execution in Maven/Gradle
- Set up test reporting (JUnit XML reports, HTML reports)

**Bug Tracking & Quality Assurance:**
- Create bug report template
- Track all bugs found (use GitHub Issues or similar)
- Prioritize bugs (critical, major, minor)
- Verify bug fixes (regression tests)
- Monitor code quality metrics (coverage, complexity)

#### Testing Responsibilities
- All testing tasks are primary responsibility!
- Coordinate with other team members to ensure testable code
- Review all PRs for test coverage before approval
- Generate weekly test coverage reports
- Identify untested code paths and create tests

#### Collaboration Points
- **With Kerem:** Get testable DAO interfaces, create database test utilities
- **With Milena:** Get test data generators for algorithm, create solvable/unsolvable test cases
- **With Rabia/Sefa:** Write TestFX UI tests, verify UI behavior
- **With Emin:** Document testing strategy, contribute to quality assurance documentation

#### Deliverables
- Week 1: CI/CD pipeline setup, test infrastructure
- Week 2: DAO layer tests (>90% coverage)
- Week 3: Constraint validation tests
- Week 4: Solver unit tests, performance benchmarks
- Week 5: All optimization strategy tests
- Week 6: View controller TestFX tests
- Week 7: Export functionality tests
- Week 8: Integration tests, end-to-end tests
- Week 9: Full test suite complete, coverage report >70%
- Week 10: Final QA report

---

### 6. Emin Arslan - Documentation & Integration Lead

#### Primary Responsibilities

**Memory Bank Maintenance:**
- Maintain and update `MEMORY_BANK.md` as project evolves
- Document all design decisions and rationale
- Track assumptions and open questions
- Update API contracts when interfaces change
- Add discovered edge cases and solutions

**Code Documentation:**
- Ensure all public methods have JavaDoc comments
- Review JavaDoc for clarity and completeness
- Set up JavaDoc generation in Maven/Gradle
- Generate JavaDoc HTML documentation
- Create package-level documentation (`package-info.java`)
- Document complex algorithms with explanatory comments

**Technical Documentation:**
- Create/maintain technical documents:
  - `DATABASE_SCHEMA.md` - Database documentation with ER diagrams
  - `ALGORITHM_DESIGN.md` - Algorithm pseudocode and flowcharts
  - `GIT_WORKFLOW.md` - Git branching strategy and PR process
  - `API_DOCUMENTATION.md` - Service layer API reference
  - `UI_COMPONENT_GUIDE.md` - FXML components and styling guide
  - `TESTING_GUIDE.md` - How to run tests, write tests, interpret coverage
- Create architecture diagrams (use draw.io, PlantUML, or similar):
  - System architecture (MVC layers)
  - Class diagrams (domain models, DAO, services)
  - Sequence diagrams (import workflow, generation workflow)
  - ER diagram (database schema)

**User Documentation:**
- Write comprehensive **User Manual** (15-25 pages) with:
  - Introduction and overview
  - Installation guide (step-by-step with screenshots)
  - Getting started tutorial
  - Feature reference (all features explained with screenshots)
  - Troubleshooting guide (common issues and solutions)
  - FAQ (frequently asked questions)
  - Appendix (sample CSV formats, configuration options)
- Create **Quick Start Guide** (1-2 pages)
- Write **Installation Guide** (standalone document)
- Write **Troubleshooting Guide** with common errors and fixes

**Code Review & Integration:**
- Review all Pull Requests for:
  - Code quality and style
  - Documentation completeness (JavaDoc, comments)
  - Adherence to architecture (MVC separation)
  - Naming conventions
  - No obvious bugs
- Coordinate integration of features from different team members
- Resolve merge conflicts when needed
- Ensure clean Git history (meaningful commit messages)
- Conduct weekly integration review meetings

**Presentation & Delivery:**
- Create presentation slides for final demo (15-20 slides)
- Prepare demo script for presentation
- Coordinate demo video recording (optional but recommended)
- Compile final deliverable package:
  - Source code (clean, organized)
  - User manual (PDF)
  - Developer documentation (JavaDoc + technical docs)
  - Executable JAR
  - Sample database
  - Sample CSV files
  - Installation guide
  - README

**Git Workflow Management:**
- Enforce Git branching strategy (main, develop, feature branches)
- Review commit messages for clarity
- Create and maintain Pull Request template
- Manage release tagging (v1.0.0, etc.)
- Coordinate merges to `develop` and `main`

**Quality Assurance Support:**
- Work with Feyza on quality standards
- Review test coverage reports
- Identify documentation gaps
- Ensure consistency across all documentation
- Create checklists (final delivery checklist, PR review checklist)

#### Testing Responsibilities
- Review test documentation
- Ensure tests have clear descriptions
- Document test execution procedures
- Create user acceptance test scenarios

#### Collaboration Points
- **With ALL:** Review all code, provide feedback, coordinate integration
- **With Kerem:** Document database schema and DAO APIs
- **With Milena:** Document algorithm design and optimization strategies
- **With Rabia:** Take screenshots for user manual, document UI components
- **With Sefa:** Document controller architecture and event flows
- **With Feyza:** Document testing strategy and procedures

#### Deliverables
- Week 1: MEMORY_BANK.md, ROADMAP.md, TEAM_ASSIGNMENTS.md, GIT_WORKFLOW.md
- Week 2: DATABASE_SCHEMA.md with ER diagram
- Week 3: Domain model documentation
- Week 4: ALGORITHM_DESIGN.md with pseudocode and flowcharts
- Week 5: API documentation for service layer
- Week 6: UI component guide with screenshots
- Week 8: Architecture diagrams (system, class, sequence)
- Week 9: Testing guide and quality assurance documentation
- Week 10: User Manual (complete), Installation Guide, Final deliverable package, Presentation slides

---

## TEAM COLLABORATION GUIDELINES

### Weekly Meetings

**Monday Sprint Planning (30 min):**
- Each person reports: What did you complete last week?
- Discuss blockers and issues
- Assign tasks for current week from roadmap
- Set weekly goals

**Wednesday Mid-Week Sync (15-30 min):**
- Quick round-robin status update
- Identify anyone who is blocked
- Schedule pair programming sessions if needed
- Adjust week's plan if necessary

**Friday Demo & Retrospective (30-45 min):**
- Each person demos their work for the week
- Celebrate completed features
- Retrospective: What went well? What to improve?
- Brief planning for next week

### Pair Programming Sessions

**Recommended pairings for integration:**
- **Kerem + Sefa:** Integrate DAO with controllers
- **Milena + Sefa:** Integrate CSP solver with UI
- **Rabia + Sefa:** Connect FXML views with controllers
- **Kerem + Milena:** Optimize database queries for solver
- **Feyza + [anyone]:** Write tests together (test-driven development)

**Schedule:** As needed, typically 1-2 hours, Wednesday or Thursday

### Code Review Process

**Every Pull Request Must:**
- Have at least 1 approval (from someone other than author)
- Pass all CI/CD checks (tests, coverage)
- Have no merge conflicts
- Follow PR template (description, changes made, testing done)

**Review Turnaround Time:**
- PRs should be reviewed within 24 hours
- Small PRs preferred (easier to review)
- Urgent PRs can be flagged in WhatsApp/Discord

**Who Reviews:**
- Emin reviews all PRs for documentation and code quality
- Feature-specific specialist reviews related PRs (e.g., Kerem reviews DB changes)
- Feyza reviews for test coverage

### Communication Channels

**Synchronous (Real-time):**
- Weekly meetings (Monday, Wednesday, Friday)
- Pair programming sessions
- Emergency sync if someone is blocked

**Asynchronous:**
- WhatsApp/Discord - Daily updates, quick questions, coordination
- GitHub - Code reviews, PR discussions, issue tracking
- Email - Formal communication with professor

### Conflict Resolution

**If technical disagreement arises:**
1. Discuss in team meeting (not in PR comments)
2. Each person presents their approach and rationale
3. Team votes (majority wins)
4. Emin documents decision in MEMORY_BANK.md with rationale

**If someone is blocked:**
1. Post in group chat immediately (don't wait)
2. If not resolved in 2 hours, schedule sync call
3. Pair programming session to unblock

**If someone is behind schedule:**
1. Notify team in Monday meeting
2. Other team members offer to help
3. Re-distribute tasks if necessary
4. Update roadmap if needed

---

## ACCOUNTABILITY & EXPECTATIONS

### Individual Commitments

**Each team member commits to:**
- Attend all weekly meetings (or send written update if absent)
- Complete assigned tasks by Friday demo
- Review assigned PRs within 24 hours
- Write tests for their own code
- Document their own code (JavaDoc, comments)
- Communicate blockers early
- Help teammates when they're stuck

### Quality Standards

**All code must:**
- Follow Java naming conventions
- Have JavaDoc for public methods
- Have meaningful variable/method names
- Have no compiler warnings
- Pass all tests
- Have test coverage (per layer targets)

### Definition of "Done"

**A task is only "done" when:**
- Code is written and works correctly
- Tests are written and passing
- JavaDoc is complete
- Code is reviewed and approved
- Merged to `develop` branch
- Documentation is updated (if needed)

---

## ESCALATION & SUPPORT

### When to Ask for Help

**Ask your teammate for help if:**
- Stuck for >2 hours on a technical problem
- Unsure about design decision
- Need code review or feedback
- Want to pair program

**Ask Emin (Integration Lead) if:**
- Merge conflicts arise
- Architecture question (which layer should this code go in?)
- Documentation question
- Git workflow question

**Ask the whole team if:**
- Major technical decision needed
- Significant roadmap change proposed
- Need to reprioritize tasks

**Ask the professor if:**
- Requirements are unclear
- Need clarification on constraints
- Considering major scope change
- Need project extension

---

## SUCCESS METRICS

### Individual Performance

**Each team member will be evaluated on:**
- Completion of assigned tasks (on time)
- Code quality (clean, documented, tested)
- Collaboration (helping teammates, pair programming)
- Communication (attending meetings, timely updates)
- Testing (writing tests, achieving coverage targets)
- Documentation (JavaDoc, comments, technical docs)

### Team Performance

**Team success measured by:**
- Meeting weekly milestones from roadmap
- Passing all tests (>70% coverage overall)
- All functional requirements implemented (FR1-FR12)
- All non-functional requirements met (NFR1-NFR6)
- Complete and polished deliverable package
- Successful final presentation

---

## APPENDICES

### Appendix A: Tools & Technologies

**Required Tools (All Team Members):**
- Java JDK 17+
- IntelliJ IDEA (recommended) or Eclipse
- Git (for version control)
- Maven or Gradle (build tool)
- SQLite Browser (for database inspection)

**Role-Specific Tools:**
- **Kerem:** DB Browser for SQLite
- **Milena:** Profiling tools (JProfiler or VisualVM)
- **Rabia:** Scene Builder (for FXML editing)
- **Sefa:** JavaFX Scene Builder
- **Feyza:** JaCoCo (coverage), TestFX (UI testing)
- **Emin:** Draw.io (diagrams), Markdown editor

### Appendix B: Learning Resources

**JavaFX:**
- Official docs: https://openjfx.io/javadoc/17/
- Tutorial: https://openjfx.io/openjfx-docs/
- Scene Builder: https://gluonhq.com/products/scene-builder/

**SQLite:**
- Tutorial: https://www.sqlitetutorial.net/
- JDBC: https://www.sqlitetutorial.net/sqlite-java/

**CSP Algorithms:**
- Russell & Norvig "Artificial Intelligence: A Modern Approach" - Chapter 6
- Online: https://artint.info/2e/html/ArtInt2e.Ch4.html

**Testing:**
- JUnit 5: https://junit.org/junit5/docs/current/user-guide/
- Mockito: https://javadoc.io/doc/org.mockito/mockito-core/latest/org/mockito/Mockito.html
- TestFX: https://github.com/TestFX/TestFX

### Appendix C: Contact Information Template

| Team Member | Email | Phone | Preferred Contact Method |
|------------|-------|-------|-------------------------|
| Kerem Bozdag | [fill in] | [fill in] | WhatsApp / Discord |
| Milena Larissa Ünal | [fill in] | [fill in] | WhatsApp / Discord |
| Rabia Tülay Gokdag | [fill in] | [fill in] | WhatsApp / Discord |
| Mehmet Sefa Keskin | [fill in] | [fill in] | WhatsApp / Discord |
| Feyza Gereme | [fill in] | [fill in] | WhatsApp / Discord |
| Emin Arslan | [fill in] | [fill in] | WhatsApp / Discord |

**Group Chat:** [WhatsApp/Discord link]
**Repository:** [GitHub URL]
**Professor:** [Name, Email, Office Hours]

---

## CHANGELOG

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-26 | Claude Code | Initial team assignments document |

---

**END OF TEAM ASSIGNMENTS**

---

**Note:** This document defines roles but emphasizes collaboration. While each person has a specialty, everyone helps each other to ensure project success. Cross-functional collaboration is encouraged and expected.
