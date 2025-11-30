# EXAM SCHEDULING SYSTEM - DEVELOPMENT ROADMAP

**Project:** Exam Planner Desktop Application
**Team:** Team 3, Section 1
**Timeline:** 10 weeks (November 26, 2025 - February 4, 2026)
**Technology Stack:** Java 17+, JavaFX, SQLite, Maven

---

## OVERVIEW

This roadmap provides a week-by-week plan for developing the Exam Scheduling System. The project is structured into 5 major phases over 10 weeks, with clear deliverables and milestones for each week.

### Team Structure (Specialist Roles)

| Team Member | Role | Primary Focus Areas |
|------------|------|-------------------|
| Kerem Bozdag | Database & Data Layer Lead | SQLite schema, DAO pattern, CSV import/export, data validation |
| Milena Larissa Ünal | Algorithm Specialist | CSP solver, constraint validation, optimization strategies |
| Rabia Tülay Gokdag | UI/FXML Specialist | JavaFX views, FXML layouts, Scene Builder, UI components |
| Mehmet Sefa Keskin | Controller Layer Lead | MVC controllers, event handling, UI-business integration |
| Feyza Gereme | Testing & QA Lead | JUnit tests, integration testing, CI/CD, test automation |
| Emin Arslan | Documentation & Integration Lead | JavaDoc, user manual, code reviews, feature integration |

---

## PHASE 1: PROJECT FOUNDATION (WEEKS 1-3)

### Week 1: Setup & Infrastructure (Nov 26 - Dec 2)

#### Goals
- ✅ Project repository initialized
- ✅ Development environment setup for all team members
- ✅ Memory bank and documentation framework created
- ✅ Maven/Gradle project structure established

#### Tasks by Role

**Database Lead (Kerem):**
- [ ] Create SQLite database schema script (`schema.sql`)
- [ ] Design ER diagram (use draw.io, Lucidchart, or similar)
- [ ] Set up database connection utility class
- [ ] Create basic DAO interfaces (StudentDAO, CourseDAO, ClassroomDAO, EnrollmentDAO)
- [ ] Write unit tests for database connection and schema creation

**Algorithm Specialist (Milena):**
- [ ] Research backtracking CSP algorithms (review textbooks, papers)
- [ ] Design high-level algorithm architecture
- [ ] Define constraint validator interfaces
- [ ] Create pseudocode for core backtracking algorithm
- [ ] Document algorithm design in `ALGORITHM_DESIGN.md`

**UI/FXML Specialist (Rabia):**
- [ ] Set up Scene Builder on development machine
- [ ] Design wireframes for main window and navigation
- [ ] Create basic FXML layout for main window (`MainWindow.fxml`)
- [ ] Design color scheme and styling guidelines
- [ ] Create initial CSS stylesheet (`styles.css`)

**Controller Lead (Sefa):**
- [ ] Set up JavaFX application entry point (`Main.java`)
- [ ] Create `MainController` skeleton
- [ ] Implement basic navigation between views
- [ ] Design controller architecture and interfaces
- [ ] Document MVC layer interactions

**Testing Lead (Feyza):**
- [ ] Set up JUnit 5 in Maven/Gradle
- [ ] Configure test directory structure
- [ ] Set up GitHub Actions for CI (basic workflow)
- [ ] Create test utility classes (`TestDataBuilder`)
- [ ] Write first integration test (database schema creation)

**Documentation Lead (Emin):**
- [ ] Review and finalize `MEMORY_BANK.md`
- [ ] Create `ROADMAP.md` (this document)
- [ ] Create `TEAM_ASSIGNMENTS.md` with detailed responsibilities
- [ ] Set up JavaDoc configuration in Maven/Gradle
- [ ] Create Git workflow documentation (`GIT_WORKFLOW.md`)

#### Deliverables
- ✅ Git repository with initial project structure
- ✅ Memory bank document (comprehensive)
- ✅ Roadmap document (this file)
- [ ] Database schema script
- [ ] Algorithm design document
- [ ] Basic UI wireframes
- [ ] CI/CD pipeline (basic)
- [ ] Team assignments document

#### Meeting Schedule
- **Monday (Nov 26):** Kickoff meeting - review requirements, assign roles, setup environments
- **Wednesday (Nov 28):** Mid-week check-in - troubleshoot setup issues, review initial designs
- **Friday (Nov 30):** Week 1 review - demo database schema, UI wireframes, algorithm pseudocode

---

### Week 2: Core Data Layer (Dec 3 - Dec 9)

#### Goals
- ✅ Database fully implemented with all tables
- ✅ DAO layer complete with CRUD operations
- ✅ CSV import functionality working
- ✅ Data validation implemented

#### Tasks by Role

**Database Lead (Kerem):** *PRIMARY FOCUS THIS WEEK*
- [ ] Implement all DAO classes (StudentDAO, CourseDAO, ClassroomDAO, EnrollmentDAO)
- [ ] Add batch insert methods for performance
- [ ] Implement transaction management (`TransactionManager` utility)
- [ ] Create CSV parser utility (`CSVParser.java`)
- [ ] Implement import service for all 4 CSV types
- [ ] Add data validation (duplicates, format checks, referential integrity)
- [ ] Write comprehensive DAO unit tests (target: >90% coverage)
- [ ] Test with sample data files

**Algorithm Specialist (Milena):**
- [ ] Create domain model classes (Student, Course, Classroom, Enrollment, etc.)
- [ ] Implement constraint validator skeleton
- [ ] Write unit tests for constraint validation logic
- [ ] Begin research on heuristics (MRV, least constraining value)

**UI/FXML Specialist (Rabia):**
- [ ] Create Import View FXML layout (`ImportView.fxml`)
- [ ] Design file browse dialogs
- [ ] Create results display area with styling
- [ ] Add icons and visual feedback elements

**Controller Lead (Sefa):**
- [ ] Implement `ImportController`
- [ ] Connect file browse buttons to file chooser dialogs
- [ ] Integrate import service with UI
- [ ] Display import results (success/error messages)
- [ ] Handle import errors gracefully

**Testing Lead (Feyza):**
- [ ] Create test CSV files (valid and invalid cases)
- [ ] Write integration tests for CSV import workflow
- [ ] Test duplicate detection
- [ ] Test error handling for malformed CSV files
- [ ] Add test coverage reporting to CI pipeline

**Documentation Lead (Emin):**
- [ ] Review all DAO code and provide feedback
- [ ] Ensure JavaDoc is complete for DAO layer
- [ ] Create database documentation with examples
- [ ] Document CSV file formats expected
- [ ] Review and merge PRs for database layer

#### Deliverables
- [ ] Complete DAO layer with unit tests
- [ ] CSV import functionality (all 4 file types)
- [ ] Import View UI (functional)
- [ ] Data validation with error reporting
- [ ] Test coverage report (>85% for DAO layer)

#### Meeting Schedule
- **Monday (Dec 3):** Sprint planning - focus on data layer
- **Wednesday (Dec 5):** Pair programming session - Kerem + Sefa (integrate DAO with UI)
- **Friday (Dec 7):** Week 2 demo - import sample CSV files, show data in database browser

---

### Week 3: Configuration & Data Models (Dec 10 - Dec 16)

#### Goals
- ✅ Configuration view implemented
- ✅ All domain model classes complete
- ✅ Schedule and ExamAssignment DAOs implemented
- ✅ Basic UI navigation working

#### Tasks by Role

**Database Lead (Kerem):**
- [ ] Implement ScheduleDAO (save, load, list schedules)
- [ ] Implement ExamAssignmentDAO (batch operations)
- [ ] Add database indexes for performance
- [ ] Create configuration service for saving user preferences
- [ ] Test schedule persistence (save → load → verify)

**Algorithm Specialist (Milena):** *PRIMARY FOCUS THIS WEEK*
- [ ] Implement all domain model classes with proper encapsulation
- [ ] Create ScheduleConfiguration class
- [ ] Implement constraint validator methods:
  - `checkNoConsecutiveExams()`
  - `checkMaxTwoExamsPerDay()`
  - `checkClassroomCapacity()`
- [ ] Write comprehensive unit tests for each constraint
- [ ] Create test cases with known violations

**UI/FXML Specialist (Rabia):**
- [ ] Create Configuration View FXML (`ConfigurationView.fxml`)
- [ ] Add number spinners for days and slots
- [ ] Create radio buttons for optimization strategies
- [ ] Design calculated fields (total slots display)
- [ ] Add validation feedback (sufficient slots check)

**Controller Lead (Sefa):**
- [ ] Implement `ConfigurationController`
- [ ] Add input validation (positive numbers only)
- [ ] Calculate and display total slots
- [ ] Enable/disable Generate button based on configuration validity
- [ ] Save configuration to database

**Testing Lead (Feyza):**
- [ ] Write integration tests for configuration workflow
- [ ] Test configuration validation logic
- [ ] Create TestFX tests for configuration UI
- [ ] Test navigation between Import and Configuration views

**Documentation Lead (Emin):**
- [ ] Document constraint validation logic
- [ ] Create diagrams for data models (UML class diagrams)
- [ ] Review algorithm design document
- [ ] Ensure all model classes have JavaDoc
- [ ] Create user guide for configuration

#### Deliverables
- [ ] Configuration view (fully functional)
- [ ] All domain model classes
- [ ] Constraint validation (tested)
- [ ] Schedule persistence (DAO)
- [ ] Navigation between views

#### Meeting Schedule
- **Monday (Dec 10):** Sprint planning - focus on models and configuration
- **Wednesday (Dec 12):** Code review session - review constraint validation logic
- **Friday (Dec 14):** Week 3 demo - configure exam period, show validation

---

## PHASE 2: CORE ALGORITHM (WEEKS 4-5)

### Week 4: CSP Solver Implementation (Dec 17 - Dec 23)

#### Goals
- ✅ Backtracking algorithm implemented
- ✅ Basic solver working for simple cases
- ✅ Constraint propagation (forward checking) implemented
- ✅ Progress tracking integrated

#### Tasks by Role

**Algorithm Specialist (Milena):** *PRIMARY FOCUS THIS WEEK*
- [ ] Implement `CSPSolver` class with backtracking algorithm
- [ ] Implement variable ordering (MRV heuristic)
- [ ] Implement value ordering (least constraining value)
- [ ] Add forward checking for constraint propagation
- [ ] Implement `ProgressTracker` callback mechanism
- [ ] Test with small datasets (3-5 courses)
- [ ] Measure performance and optimize if needed

**Database Lead (Kerem):**
- [ ] Create efficient query methods for solver:
  - `getStudentsForCourse(courseCode)`
  - `getCoursesForStudent(studentId)`
  - Pre-compute enrollment mappings for performance
- [ ] Optimize database indexes for solver queries
- [ ] Support Milena with data access needs

**UI/FXML Specialist (Rabia):**
- [ ] Create Generate View FXML (`ScheduleView.fxml`)
- [ ] Design progress bar and status display
- [ ] Create success/failure result screens
- [ ] Add cancel button for long-running operations

**Controller Lead (Sefa):**
- [ ] Implement `ScheduleController`
- [ ] Integrate CSP solver with UI using JavaFX Task
- [ ] Update progress bar in real-time
- [ ] Handle cancellation (interrupt solver)
- [ ] Display success or failure results
- [ ] Handle `NoSolutionException` gracefully

**Testing Lead (Feyza):**
- [ ] Create test datasets (solvable and unsolvable)
- [ ] Write unit tests for solver with known solutions
- [ ] Test constraint violations are detected
- [ ] Test cancellation functionality
- [ ] Benchmark solver performance (small dataset <10 seconds)

**Documentation Lead (Emin):**
- [ ] Document algorithm implementation details
- [ ] Create flowchart for backtracking process
- [ ] Review solver code for clarity and comments
- [ ] Document optimization strategies
- [ ] Update user guide with generation instructions

#### Deliverables
- [ ] Working CSP solver (basic cases)
- [ ] Constraint validation integrated
- [ ] Progress tracking UI
- [ ] Performance benchmarks
- [ ] Algorithm documentation

#### Meeting Schedule
- **Monday (Dec 17):** Sprint planning - focus on algorithm
- **Wednesday (Dec 19):** Pair programming - Milena + Sefa (integrate solver with UI)
- **Friday (Dec 21):** Week 4 demo - generate schedule for 5 courses

---

### Week 5: Optimization & Heuristics (Dec 24 - Dec 30)

*Note: This week includes holidays (Christmas). Adjust schedule as needed.*

#### Goals
- ✅ All 4 optimization strategies implemented
- ✅ Solver works for medium datasets (20 courses)
- ✅ Conflict detection implemented
- ✅ Performance optimized

#### Tasks by Role

**Algorithm Specialist (Milena):** *PRIMARY FOCUS THIS WEEK*
- [ ] Implement optimization strategy interface and 4 concrete strategies:
  1. MinimizeDaysStrategy
  2. BalanceDistributionStrategy
  3. MinimizeClassroomsStrategy
  4. BalanceClassroomsStrategy
- [ ] Integrate strategies with value ordering heuristic
- [ ] Implement `ConflictDetector` for unsolvable cases
- [ ] Test solver with sample data (20 courses, 250 students, 10 classrooms)
- [ ] Optimize performance (memoization, caching)

**Database Lead (Kerem):**
- [ ] Profile database queries during solving
- [ ] Add any missing indexes
- [ ] Optimize batch operations
- [ ] Support Milena with performance testing

**UI/FXML Specialist (Rabia):**
- [ ] Create conflict report display screen
- [ ] Design recommendations UI
- [ ] Improve progress display (time estimate)
- [ ] Add visual feedback for different strategies

**Controller Lead (Sefa):**
- [ ] Integrate optimization strategy selection
- [ ] Display conflict report when solver fails
- [ ] Show recommendations to user
- [ ] Add time estimate to progress display

**Testing Lead (Feyza):**
- [ ] Test all 4 optimization strategies
- [ ] Verify strategies produce different solutions
- [ ] Test performance with sample data (must complete <10 seconds)
- [ ] Test conflict detection on unsolvable cases
- [ ] Create performance benchmarks report

**Documentation Lead (Emin):**
- [ ] Document each optimization strategy
- [ ] Create comparison of strategies (when to use each)
- [ ] Review and improve code comments
- [ ] Update user guide with strategy selection
- [ ] Create troubleshooting guide for conflicts

#### Deliverables
- [ ] All 4 optimization strategies working
- [ ] Solver handles sample data (<10 sec)
- [ ] Conflict detection and reporting
- [ ] Performance optimization complete
- [ ] Strategy comparison documentation

#### Meeting Schedule
- **Monday (Dec 24):** *Holiday - No meeting*
- **Wednesday (Dec 26):** *Holiday - No meeting*
- **Friday (Dec 28):** Week 5 review (async/written update)

---

## PHASE 3: USER INTERFACE (WEEKS 6-7)

### Week 6: Schedule Views Implementation (Dec 31 - Jan 6)

*Note: New Year's week - adjust as needed*

#### Goals
- ✅ All 4 schedule views implemented (Classroom, Student, Course, Day)
- ✅ Data display working correctly
- ✅ Filtering and sorting implemented
- ✅ Views fully styled

#### Tasks by Role

**UI/FXML Specialist (Rabia):** *PRIMARY FOCUS THIS WEEK*
- [ ] Create FXML layouts for all 4 views:
  - `ClassroomView.fxml` (table view)
  - `StudentView.fxml` (table with search)
  - `CourseView.fxml` (sortable table)
  - `DayView.fxml` (grid layout)
- [ ] Design table column layouts
- [ ] Add filter controls (dropdowns, search boxes)
- [ ] Style tables (alternating rows, headers)
- [ ] Add icons and visual hierarchy

**Controller Lead (Sefa):** *PRIMARY FOCUS THIS WEEK*
- [ ] Implement controllers for all 4 views:
  - `ClassroomViewController`
  - `StudentViewController`
  - `CourseViewController`
  - `DayViewController`
- [ ] Populate tables with schedule data
- [ ] Implement filtering logic
- [ ] Implement sorting for table columns
- [ ] Handle view switching

**Database Lead (Kerem):**
- [ ] Create query methods for each view:
  - Classroom view query (JOIN exams, courses, enrollments)
  - Student view query (filter by student)
  - Course view query (all courses with assignments)
  - Day view query (grid format)
- [ ] Optimize queries for fast display (<100ms)

**Algorithm Specialist (Milena):**
- [ ] Support team with data transformation logic
- [ ] Help with grid layout calculations for Day view
- [ ] Review view logic for correctness

**Testing Lead (Feyza):**
- [ ] Write TestFX tests for each view
- [ ] Test filtering functionality
- [ ] Test sorting functionality
- [ ] Test view switching
- [ ] Test with various schedule sizes

**Documentation Lead (Emin):**
- [ ] Document view implementation
- [ ] Create user guide for viewing schedules
- [ ] Review UI code for consistency
- [ ] Take screenshots for documentation
- [ ] Create video walkthrough of views

#### Deliverables
- [ ] All 4 schedule views (functional)
- [ ] Filtering and sorting working
- [ ] Styled and polished UI
- [ ] View documentation with screenshots
- [ ] TestFX tests for views

#### Meeting Schedule
- **Monday (Dec 31):** *Holiday - No meeting*
- **Wednesday (Jan 2):** Mid-week sync - review view progress
- **Friday (Jan 4):** Week 6 demo - view generated schedule in all 4 views

---

### Week 7: Export & Manual Editing (Jan 7 - Jan 13)

#### Goals
- ✅ CSV export implemented
- ✅ Excel export implemented
- ✅ Manual editing with constraint validation
- ✅ Export view complete

#### Tasks by Role

**Database Lead (Kerem):**
- [ ] Implement export queries for all view types
- [ ] Create `ExportService` class
- [ ] Test data export accuracy
- [ ] Support export functionality development

**Algorithm Specialist (Milena):**
- [ ] Implement manual edit validation
- [ ] Create detailed error messages for constraint violations
- [ ] Test edit validation thoroughly
- [ ] Handle edge cases for edits

**UI/FXML Specialist (Rabia):**
- [ ] Create Export View FXML (`ExportView.fxml`)
- [ ] Design format selection (CSV/Excel)
- [ ] Add view selection checkboxes
- [ ] Create file destination chooser
- [ ] Design edit dialog for manual adjustments

**Controller Lead (Sefa):** *PRIMARY FOCUS THIS WEEK*
- [ ] Implement `ExportController`
- [ ] Integrate CSV export (using `CSVWriter` utility)
- [ ] Integrate Excel export (using Apache POI)
- [ ] Add file save dialog
- [ ] Display export success/error messages
- [ ] Implement manual edit dialog controller
- [ ] Validate edits before applying

**Testing Lead (Feyza):**
- [ ] Test CSV export (verify format, content)
- [ ] Test Excel export (verify multi-sheet workbook)
- [ ] Test manual edit validation (try violating each constraint)
- [ ] Test edit rejection with proper error messages
- [ ] Integration test: Generate → Edit → Export workflow

**Documentation Lead (Emin):**
- [ ] Document export file formats
- [ ] Create export user guide
- [ ] Document manual editing limitations
- [ ] Review export code
- [ ] Create example exported files

#### Deliverables
- [ ] CSV export (all 4 views)
- [ ] Excel export (multi-sheet)
- [ ] Manual editing (with validation)
- [ ] Export view (complete)
- [ ] Export documentation

#### Meeting Schedule
- **Monday (Jan 7):** Sprint planning - focus on export and editing
- **Wednesday (Jan 9):** Pair programming - Sefa + Rabia (export UI)
- **Friday (Jan 11):** Week 7 demo - export schedule, attempt manual edits

---

## PHASE 4: ADVANCED FEATURES (WEEK 8)

### Week 8: History, Conflict Detection, Polish (Jan 14 - Jan 20)

#### Goals
- ✅ Schedule history view implemented
- ✅ Load previous schedules working
- ✅ Conflict detection fully functional
- ✅ All features integrated and polished

#### Tasks by Role

**Database Lead (Kerem):**
- [ ] Implement schedule history queries
- [ ] Add metadata display for schedules
- [ ] Implement schedule deletion
- [ ] Test save/load/delete workflows

**Algorithm Specialist (Milena):**
- [ ] Finalize conflict detector
- [ ] Create detailed conflict reports
- [ ] Add recommendations for each conflict type
- [ ] Test with various unsolvable scenarios

**UI/FXML Specialist (Rabia):**
- [ ] Create History View FXML (`HistoryView.fxml`)
- [ ] Design schedule list with metadata
- [ ] Create conflict report display
- [ ] Polish all UI screens (consistency, alignment, styling)
- [ ] Add application icon and branding

**Controller Lead (Sefa):**
- [ ] Implement `HistoryController`
- [ ] Load selected schedule from history
- [ ] Delete schedules with confirmation
- [ ] Display conflict reports
- [ ] Polish all controller logic

**Testing Lead (Feyza):** *PRIMARY FOCUS THIS WEEK*
- [ ] Write integration tests for history workflow
- [ ] Test conflict detection comprehensively
- [ ] End-to-end testing (full workflow: import → configure → generate → view → export)
- [ ] Performance testing with larger datasets
- [ ] Create test report

**Documentation Lead (Emin):**
- [ ] Review all features for completeness
- [ ] Update user guide with history and conflicts
- [ ] Review all JavaDoc for completeness
- [ ] Create feature comparison matrix
- [ ] Conduct code review session with team

#### Deliverables
- [ ] History view (functional)
- [ ] Conflict detection (complete)
- [ ] All features polished
- [ ] Integration tests complete
- [ ] Feature documentation complete

#### Meeting Schedule
- **Monday (Jan 14):** Sprint planning - focus on polish and integration
- **Wednesday (Jan 16):** Integration review - test full workflows
- **Friday (Jan 18):** Week 8 demo - show all features working together

---

## PHASE 5: TESTING, DOCUMENTATION, POLISH (WEEKS 9-10)

### Week 9: Comprehensive Testing & Bug Fixes (Jan 21 - Jan 27)

#### Goals
- ✅ All unit tests complete (>70% coverage)
- ✅ All integration tests complete
- ✅ All bugs fixed
- ✅ Performance targets met

#### Tasks by Role

**Testing Lead (Feyza):** *PRIMARY FOCUS THIS WEEK*
- [ ] Run full test suite and generate coverage report
- [ ] Identify untested code paths
- [ ] Write additional tests to reach 70% coverage
- [ ] Create test documentation
- [ ] Verify performance benchmarks:
  - Small dataset (50 courses, 500 students): <10 sec ✓
  - Sample dataset (20 courses, 250 students): <5 sec ✓
- [ ] Create bug report template
- [ ] Track all bugs found

**All Team Members:**
- [ ] Fix assigned bugs
- [ ] Add tests for bug fixes (regression tests)
- [ ] Code cleanup (remove dead code, unused imports)
- [ ] Improve error handling
- [ ] Add logging where needed

**Database Lead (Kerem):**
- [ ] Final database optimization
- [ ] Add any missing indexes
- [ ] Test database performance under load
- [ ] Create database backup/restore utility

**Algorithm Specialist (Milena):**
- [ ] Final algorithm optimization
- [ ] Test edge cases thoroughly
- [ ] Fix any constraint validation bugs
- [ ] Optimize memory usage

**UI/FXML Specialist (Rabia):**
- [ ] Fix UI bugs (alignment, styling issues)
- [ ] Test on different screen resolutions
- [ ] Ensure consistent styling across all views
- [ ] Test keyboard navigation

**Controller Lead (Sefa):**
- [ ] Fix controller logic bugs
- [ ] Test error handling in all controllers
- [ ] Ensure proper resource cleanup (close connections, etc.)
- [ ] Test memory leaks

**Documentation Lead (Emin):**
- [ ] Review all bug fixes
- [ ] Update documentation with any changes
- [ ] Ensure JavaDoc is complete and accurate
- [ ] Create known issues document

#### Deliverables
- [ ] Test coverage report (>70%)
- [ ] All critical bugs fixed
- [ ] Performance benchmarks met
- [ ] Regression test suite
- [ ] Bug fix documentation

#### Meeting Schedule
- **Monday (Jan 21):** Testing sprint planning - assign bug fixes
- **Wednesday (Jan 23):** Bug triage meeting - prioritize remaining issues
- **Friday (Jan 25):** Week 9 review - verify all tests passing, bugs fixed

---

### Week 10: Final Documentation & Delivery (Jan 28 - Feb 4)

#### Goals
- ✅ User manual complete with screenshots
- ✅ Developer documentation complete
- ✅ Deliverable package prepared
- ✅ Final presentation ready

#### Tasks by Role

**Documentation Lead (Emin):** *PRIMARY FOCUS THIS WEEK*
- [ ] Finalize user manual with screenshots and step-by-step instructions
- [ ] Create installation guide
- [ ] Write troubleshooting guide
- [ ] Compile developer documentation (architecture, API, database schema)
- [ ] Create README for repository
- [ ] Prepare presentation slides

**All Team Members:**
- [ ] Review and proofread documentation
- [ ] Test installation guide on fresh machine
- [ ] Record demo video (optional but recommended)
- [ ] Prepare individual sections for presentation
- [ ] Final code cleanup and organization

**Database Lead (Kerem):**
- [ ] Prepare sample database file
- [ ] Document database schema fully
- [ ] Create ER diagram (final version)
- [ ] Write database maintenance guide

**Algorithm Specialist (Milena):**
- [ ] Document algorithm implementation fully
- [ ] Create algorithm flowcharts
- [ ] Write performance tuning guide
- [ ] Document optimization strategies

**UI/FXML Specialist (Rabia):**
- [ ] Take screenshots for user manual
- [ ] Create UI component guide
- [ ] Document styling approach
- [ ] Create FXML template documentation

**Controller Lead (Sefa):**
- [ ] Document controller architecture
- [ ] Create API documentation for controllers
- [ ] Document event handling approach
- [ ] Create integration guide for new features

**Testing Lead (Feyza):**
- [ ] Finalize test documentation
- [ ] Create test execution guide
- [ ] Document CI/CD pipeline
- [ ] Create quality assurance report

#### Deliverables
- [ ] **User Manual** (PDF, 15-25 pages with screenshots)
- [ ] **Developer Documentation** (JavaDoc + architecture docs)
- [ ] **Source Code** (clean, commented, organized)
- [ ] **Test Suite** (all tests passing, >70% coverage)
- [ ] **Sample Database** (with sample data loaded)
- [ ] **Executable JAR** (runnable application)
- [ ] **Installation Guide** (step-by-step)
- [ ] **Presentation Slides** (15-20 slides for final demo)
- [ ] **Demo Video** (5-10 minutes, optional)

#### Meeting Schedule
- **Monday (Jan 28):** Final sprint planning - documentation tasks
- **Wednesday (Jan 30):** Documentation review session
- **Friday (Feb 1):** Final review meeting - verify all deliverables complete
- **Tuesday (Feb 4):** Final presentation rehearsal

---

## FINAL DELIVERY CHECKLIST

### Code Quality
- [ ] All code follows Java naming conventions
- [ ] No compiler warnings
- [ ] No dead code or commented-out code
- [ ] All public methods have JavaDoc
- [ ] Complex algorithms have explanatory comments
- [ ] All TODOs resolved or documented

### Testing
- [ ] All unit tests passing
- [ ] All integration tests passing
- [ ] Test coverage >70% overall
- [ ] Critical paths (algorithm, constraints) >90% coverage
- [ ] Performance benchmarks met

### Documentation
- [ ] User manual complete with screenshots
- [ ] Installation guide tested on fresh machine
- [ ] JavaDoc generated and complete
- [ ] Architecture documentation complete
- [ ] Database schema documented
- [ ] Algorithm design documented
- [ ] README comprehensive

### Functionality
- [ ] All functional requirements (FR1-FR12) implemented
- [ ] All non-functional requirements (NFR1-NFR6) met
- [ ] Import workflow works end-to-end
- [ ] Schedule generation works for sample data
- [ ] All 4 views display correctly
- [ ] Export to CSV and Excel works
- [ ] Manual editing with validation works
- [ ] Schedule history works
- [ ] Conflict detection works

### User Experience
- [ ] Error messages are clear and actionable
- [ ] All buttons/controls have clear labels
- [ ] Navigation is intuitive
- [ ] Progress indicators work correctly
- [ ] Application doesn't freeze during long operations
- [ ] Cancellation works

### Deployment
- [ ] Executable JAR builds correctly
- [ ] JAR runs on Windows 10/11
- [ ] Sample database included
- [ ] Sample CSV files included
- [ ] All dependencies bundled

---

## RISK MANAGEMENT

### Identified Risks & Mitigation Strategies

#### Risk 1: Algorithm Performance Issues
**Risk:** CSP solver may be too slow for larger datasets
**Mitigation:**
- Start performance testing early (Week 5)
- Implement optimization (memoization, caching) proactively
- Have fallback: greedy heuristic if backtracking is too slow
- Test with sample data regularly

#### Risk 2: Team Member Availability During Holidays
**Risk:** Weeks 5 & 6 include holidays, reduced productivity
**Mitigation:**
- Plan lighter workload for those weeks
- Schedule async updates instead of meetings
- Front-load critical work (algorithm) to Week 4
- Build buffer time into schedule

#### Risk 3: Integration Issues Between Layers
**Risk:** DAO, algorithm, UI, controllers may not integrate smoothly
**Mitigation:**
- Define clear API contracts (see Memory Bank)
- Conduct weekly integration reviews
- Pair programming for integration points
- Integration tests written early

#### Risk 4: Testing Coverage Gap
**Risk:** May not reach 70% test coverage in time
**Mitigation:**
- Write tests alongside code (not at the end)
- Testing Lead monitors coverage weekly
- CI/CD shows coverage on every commit
- Dedicate Week 9 entirely to testing

#### Risk 5: Unclear Requirements
**Risk:** Ambiguity in requirements leads to rework
**Mitigation:**
- Memory Bank documents all decisions
- Ask professor for clarification early
- Team votes on technical decisions
- Document all assumptions

#### Risk 6: Scope Creep
**Risk:** Team adds features beyond requirements
**Mitigation:**
- Stick to roadmap strictly
- Any new features require team vote
- Focus on requirements FR1-FR12 only
- Save "nice to have" features for post-delivery

---

## SUCCESS METRICS

### Week-by-Week Progress Tracking

| Week | Expected Completion | Key Milestone |
|------|---------------------|---------------|
| 1 | 10% | Project setup, infrastructure, designs |
| 2 | 20% | Data layer complete, CSV import working |
| 3 | 30% | Configuration working, constraints validated |
| 4 | 50% | Basic CSP solver working |
| 5 | 60% | All optimization strategies, sample data working |
| 6 | 75% | All views implemented |
| 7 | 85% | Export and editing complete |
| 8 | 95% | All features complete and polished |
| 9 | 98% | All tests passing, bugs fixed |
| 10 | 100% | Documentation complete, ready for delivery |

### Definition of "Done" for Each Phase

**Phase 1 (Foundation) Done When:**
- Database schema created and tested
- Import workflow works for all 4 CSV types
- Configuration view functional
- All team members can run project locally

**Phase 2 (Algorithm) Done When:**
- Solver generates valid schedules for sample data (20 courses, 250 students)
- All constraints validated correctly
- Performance <10 seconds for sample data
- Conflict detection identifies unsolvable cases

**Phase 3 (UI) Done When:**
- All 4 views display schedule data correctly
- Export to CSV and Excel works
- Manual editing validates constraints
- UI is polished and styled

**Phase 4 (Advanced Features) Done When:**
- Schedule history works (save/load/delete)
- Conflict reports are detailed and helpful
- All features integrated smoothly
- No critical bugs remaining

**Phase 5 (Testing & Documentation) Done When:**
- Test coverage >70%
- All tests passing
- User manual complete with screenshots
- Executable JAR builds and runs correctly

---

## COMMUNICATION & COLLABORATION

### Weekly Meeting Structure

**Monday Sprint Planning (30 min):**
- Review last week's progress
- Discuss any blockers
- Assign tasks for current week
- Set goals for the week

**Wednesday Mid-Week Sync (15-30 min):**
- Quick status update from each person
- Identify any issues early
- Schedule pair programming if needed
- Adjust week plan if necessary

**Friday Demo & Retrospective (30-45 min):**
- Each person demos their work
- Celebrate wins
- Discuss what went well
- Discuss what to improve
- Plan for next week

### Communication Channels

**Synchronous:**
- Weekly meetings (Monday, Wednesday, Friday)
- Pair programming sessions (as needed)
- Emergency sync (if someone is blocked)

**Asynchronous:**
- WhatsApp/Discord (daily updates, quick questions)
- GitHub (code reviews, PR comments)
- Email (formal communication with professor)

### Code Review Process

**PR Requirements:**
- At least one approval required
- All tests must pass (CI/CD check)
- No merge conflicts
- Description follows template

**Review Checklist:**
- Code quality (naming, structure, comments)
- Tests included
- JavaDoc complete
- No obvious bugs

**Turnaround Time:**
- PRs should be reviewed within 24 hours
- Small PRs preferred (easier to review)

---

## CONTINGENCY PLANS

### If We Fall Behind Schedule

**Mild Delay (1-2 days):**
- Extend current week's work into weekend
- Reduce scope of next week slightly
- Skip optional features

**Moderate Delay (3-5 days):**
- Hold emergency team meeting
- Re-prioritize features (focus on FR1-FR4 core features)
- Reduce test coverage goal to 60%
- Simplify UI (less polish)

**Severe Delay (>1 week):**
- Meet with professor to discuss extension or scope reduction
- Cut advanced features (manual editing, history)
- Focus on core workflow: import → configure → generate → view → export
- Minimal UI (functional but not polished)

### If Team Member Is Unavailable

**Short-term (1-3 days):**
- Other team members cover their tasks
- Documentation Lead coordinates coverage

**Long-term (>1 week):**
- Redistribute responsibilities permanently
- Reduce scope if necessary
- Adjust roadmap

### If Technical Challenge Arises

**Algorithm doesn't perform well:**
- Implement greedy heuristic as fallback
- Reduce sample data size
- Extend time limit to 30 seconds

**UI framework issues:**
- Simplify UI design
- Use basic components instead of custom ones
- Accept less polished UI

**Database performance issues:**
- Add more indexes
- Use in-memory caching
- Pre-compute complex queries

---

## POST-DELIVERY (OPTIONAL)

### Potential Enhancements (Beyond Scope)

If time permits after requirements are met, consider:

1. **Multi-classroom exams:** Allow splitting large courses across multiple rooms
2. **Time slot templates:** Save common configurations (e.g., "Standard 2-week period")
3. **Conflict visualization:** Graph showing which students/courses conflict
4. **Auto-save:** Periodically save work-in-progress
5. **Undo/Redo:** For manual edits
6. **Classroom features:** Add classroom attributes (projector, capacity, building)
7. **Custom constraints:** Allow user to add soft constraints
8. **Schedule comparison:** Compare two schedules side-by-side
9. **Print preview:** Formatted print layouts for schedules
10. **Dark mode:** Alternative UI theme

**Note:** Only pursue enhancements if core requirements are 100% complete and polished.

---

## APPENDICES

### Appendix A: Key Dates

| Date | Event |
|------|-------|
| Nov 26, 2025 | Project start, Week 1 begins |
| Dec 24-26 | Christmas holiday |
| Dec 31 - Jan 1 | New Year holiday |
| Jan 28, 2026 | Week 10 begins (final week) |
| Feb 4, 2026 | Final delivery deadline |

### Appendix B: Resources

**Learning Resources:**
- JavaFX Tutorial: https://openjfx.io/openjfx-docs/
- SQLite Tutorial: https://www.sqlitetutorial.net/
- CSP Algorithms: Russell & Norvig AI Textbook, Chapter 6
- JUnit 5 Guide: https://junit.org/junit5/docs/current/user-guide/

**Tools:**
- IntelliJ IDEA: https://www.jetbrains.com/idea/
- Scene Builder: https://gluonhq.com/products/scene-builder/
- DB Browser for SQLite: https://sqlitebrowser.org/
- Git: https://git-scm.com/

### Appendix C: Contact Information

| Team Member | Email | Phone | Role |
|------------|-------|-------|------|
| Kerem Bozdag | [email] | [phone] | Database Lead |
| Milena Larissa Ünal | [email] | [phone] | Algorithm |
| Rabia Tülay Gokdag | [email] | [phone] | UI/FXML |
| Mehmet Sefa Keskin | [email] | [phone] | Controller |
| Feyza Gereme | [email] | [phone] | Testing |
| Emin Arslan | [email] | [phone] | Documentation |

**Professor:** [Name, Email, Office Hours]

---

## CHANGELOG

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-26 | Claude Code | Initial roadmap creation |

---

**END OF ROADMAP**

---

**Note:** This roadmap is a living document. Update it as the project progresses, risks materialize, or plans change. Review and adjust weekly during sprint planning meetings.
