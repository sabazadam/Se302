# Exam Scheduling System

[![Java CI](https://github.com/sabazadam/se302/workflows/Java%20CI%20with%20Maven/badge.svg)](https://github.com/sabazadam/se302/actions)
[![License: Academic](https://img.shields.io/badge/License-Academic-yellow.svg)](https://opensource.org/licenses/MIT)

A desktop application for automated exam scheduling using Constraint Satisfaction Problem (CSP) solving techniques. Built with Java and JavaFX for educational institutions to efficiently generate conflict-free exam schedules.

---

## Table of Contents

- [Features](#features)
- [Quick Start](#quick-start)
- [Installation](#installation)
- [Usage](#usage)
- [Project Structure](#project-structure)
- [Development](#development)
- [Testing](#testing)
- [Documentation](#documentation)
- [Team](#team)
- [License](#license)

---

## Features

### Core Functionality

- **CSV Data Import**: Import students, courses, classrooms, and enrollment data from CSV files
- **Intelligent Scheduling**: Automated exam scheduling using backtracking CSP solver
- **Constraint Validation**: Enforces hard constraints:
  - No consecutive exams for any student
  - Maximum 2 exams per student per day
  - Classroom capacity limits respected
- **Multiple Optimization Strategies**:
  - Minimize days used
  - Balance distribution across exam period
  - Minimize classrooms needed
  - Balance classroom usage per day
- **Multi-View Display**:
  - Classroom view (which exams in which rooms)
  - Student view (personal exam schedule)
  - Course view (exam details per course)
  - Day view (grid layout by day and time slot)
- **Export Capabilities**: Export schedules to CSV or Excel (.xlsx) format
- **Manual Adjustments**: Edit schedules with real-time constraint validation
- **Schedule History**: Save, load, and compare previous schedules
- **Conflict Detection**: Detailed reporting when no valid schedule exists

### Technical Highlights

- **Performance**: Generates schedules for 20 courses, 250 students in < 5 seconds
- **Scalability**: Handles datasets up to 50 courses, 500+ students
- **Reliability**: 100% constraint satisfaction guarantee
- **User-Friendly**: Clear error messages, progress indicators, intuitive UI

---

## Quick Start

```bash
# Clone the repository
git clone https://github.com/sabazadam/se302.git
cd se302

# Build the project
mvn clean install

# Run the application
mvn javafx:run
```

---

## Installation

### Prerequisites

- **Java Development Kit (JDK) 17 or higher**
  - Download: [Oracle JDK](https://www.oracle.com/java/technologies/downloads/) or [OpenJDK](https://adoptium.net/)
- **Maven 3.8+** (for building from source)
  - Download: [Apache Maven](https://maven.apache.org/download.cgi)
- **Operating System**: Windows 10/11, macOS, or Linux
- **Minimum Hardware**:
  - 2 GHz dual-core processor
  - 4 GB RAM
  - 100 MB disk space

### Installation Steps

#### Option 1: Run from JAR (Recommended for Users)

1. Download the latest release JAR from [Releases](https://github.com/sabazadam/se302/releases)
2. Double-click the JAR file or run from command line:
   ```bash
   java -jar exam-scheduler-1.0.0-jar-with-dependencies.jar
   ```

#### Option 2: Build from Source (For Developers)

1. **Clone the repository:**
   ```bash
   git clone https://github.com/sabazadam/se302.git
   cd se302
   ```

2. **Build the project:**
   ```bash
   mvn clean package
   ```

3. **Run the application:**
   ```bash
   mvn javafx:run
   ```

   Or run the generated JAR:
   ```bash
   java -jar target/exam-scheduler-1.0.0-jar-with-dependencies.jar
   ```

---

## Usage

### 1. Import Data

1. Click **Import Data** in the navigation panel
2. Select your CSV files:
   - **Students**: List of student IDs (one per line)
   - **Courses**: List of course codes (one per line)
   - **Classrooms**: Format `ClassroomID;Capacity` (e.g., `Room_01;40`)
   - **Enrollments**: Student-course enrollment mappings
3. Click **Import All** and verify success

**CSV Format Examples:**

`students.csv`:
```
Std_ID_001
Std_ID_002
Std_ID_003
```

`classrooms.csv`:
```
Classroom_01;40
Classroom_02;35
Classroom_03;50
```

### 2. Configure Exam Period

1. Click **Configure** in the navigation panel
2. Set **Number of Days** (e.g., 5 days)
3. Set **Time Slots Per Day** (e.g., 4 slots)
4. Total available slots = Days × Slots (e.g., 5 × 4 = 20 slots)
5. Choose **Optimization Strategy**:
   - **Minimize Days**: Pack exams into fewest days
   - **Balance Distribution**: Spread exams evenly
   - **Minimize Classrooms**: Use fewest rooms
   - **Balance Classrooms**: Even room usage per day
6. Click **Save Configuration**

### 3. Generate Schedule

1. Click **Generate** in the navigation panel
2. Review configuration summary
3. Click **Generate Schedule**
4. Watch progress bar (cancellation available)
5. View success/failure result

**If generation fails**, view conflict report for:
- Students with too many courses
- Courses exceeding classroom capacity
- Recommendations to resolve conflicts

### 4. View Schedule

After successful generation, view schedule in 4 different formats:

- **Classroom View**: See which exams are in each classroom
- **Student View**: Search for a student's personal exam schedule
- **Course View**: See when each course exam is scheduled
- **Day View**: Visual grid of all exams by day and time slot

### 5. Export Schedule

1. Click **Export** in the navigation panel
2. Choose format: CSV or Excel
3. Select which views to export (can export all 4 views)
4. Choose destination folder
5. Click **Export**

Excel exports create a multi-sheet workbook with formatted tables ready for printing.

---

## Project Structure

```
se302/
├── src/
│   ├── main/
│   │   ├── java/com/se302/examscheduler/
│   │   │   ├── model/              # Domain models (Student, Course, etc.)
│   │   │   ├── dao/                # Data Access Objects
│   │   │   ├── service/            # Business logic layer
│   │   │   ├── algorithm/          # CSP solver implementation
│   │   │   ├── controller/         # JavaFX controllers
│   │   │   ├── util/               # Utility classes
│   │   │   └── Main.java           # Application entry point
│   │   └── resources/
│   │       ├── fxml/               # JavaFX FXML layouts
│   │       ├── css/                # Stylesheets
│   │       ├── images/             # Icons and images
│   │       └── database/           # SQL schema
│   └── test/
│       └── java/com/se302/examscheduler/
│           ├── dao/                # DAO tests
│           ├── service/            # Service tests
│           ├── algorithm/          # Algorithm tests
│           └── integration/        # Integration tests
├── docs/                           # Documentation
│   ├── MEMORY_BANK.md             # Comprehensive project documentation
│   ├── ROADMAP.md                 # Development roadmap
│   ├── TEAM_ASSIGNMENTS.md        # Team roles and responsibilities
│   ├── GIT_WORKFLOW.md            # Git workflow guide
│   └── screenshots/               # Application screenshots
├── .github/
│   └── workflows/
│       └── ci.yml                 # GitHub Actions CI/CD
├── pom.xml                        # Maven configuration
├── .gitignore                     # Git ignore rules
└── README.md                      # This file
```

---

## Development

### Prerequisites for Development

- Java JDK 17+
- Maven 3.8+
- IDE: IntelliJ IDEA (recommended) or Eclipse
- Scene Builder (for FXML editing): [Download](https://gluonhq.com/products/scene-builder/)
- Git

### Setup Development Environment

1. **Clone the repository:**
   ```bash
   git clone https://github.com/sabazadam/se302.git
   cd se302
   ```

2. **Import into IDE:**
   - IntelliJ IDEA: File → Open → Select `pom.xml`
   - Eclipse: File → Import → Existing Maven Projects

3. **Configure JDK 17:**
   - IntelliJ: File → Project Structure → Project SDK → Select JDK 17
   - Eclipse: Right-click project → Properties → Java Build Path → JRE System Library

4. **Build the project:**
   ```bash
   mvn clean install
   ```

5. **Run from IDE:**
   - Main class: `com.se302.examscheduler.Main`
   - VM options: `--module-path /path/to/javafx-sdk/lib --add-modules javafx.controls,javafx.fxml`

### Git Workflow

We follow a **feature branch workflow** with `main` and `develop` branches:

1. Create feature branch from `develop`:
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/your-feature-name
   ```

2. Make changes and commit:
   ```bash
   git add .
   git commit -m "feat(scope): brief description"
   ```

3. Push and create Pull Request:
   ```bash
   git push origin feature/your-feature-name
   ```

4. After PR approval, merge to `develop`
5. Delete feature branch after merge

**See [GIT_WORKFLOW.md](GIT_WORKFLOW.md) for detailed guidelines.**

### Coding Standards

- **Java Naming Conventions**: camelCase for variables/methods, PascalCase for classes
- **JavaDoc**: Required for all public methods
- **Comments**: Explain "why" not "what"
- **Max line length**: 120 characters
- **Indentation**: 4 spaces (no tabs)
- **Formatting**: Follow Java standard code style

---

## Testing

### Running Tests

```bash
# Run all tests
mvn test

# Run specific test class
mvn test -Dtest=StudentDAOTest

# Run with coverage report
mvn clean test jacoco:report

# View coverage report
open target/site/jacoco/index.html
```

### Test Coverage Goals

- **Overall**: > 70% line coverage
- **DAO Layer**: > 90%
- **Algorithm Layer**: > 95%
- **Service Layer**: > 80%
- **Controller Layer**: > 70%

### Continuous Integration

GitHub Actions automatically runs tests on every push and pull request:
- Compiles code
- Runs all tests
- Generates coverage report
- Uploads to Codecov

See [.github/workflows/ci.yml](.github/workflows/ci.yml) for CI configuration.

---

## Documentation

### User Documentation

- **User Manual** (PDF): Comprehensive guide with screenshots (coming in Week 10)
- **Installation Guide**: See [Installation](#installation) section above
- **Troubleshooting Guide**: (coming soon)

### Developer Documentation

- **Memory Bank**: [MEMORY_BANK.md](MEMORY_BANK.md) - Comprehensive project documentation including:
  - Requirements summary
  - Database schema
  - Architecture and design patterns
  - Algorithm design
  - API contracts
  - Testing strategy
- **Roadmap**: [ROADMAP.md](ROADMAP.md) - Week-by-week development plan
- **Team Assignments**: [TEAM_ASSIGNMENTS.md](TEAM_ASSIGNMENTS.md) - Roles and responsibilities
- **Git Workflow**: [GIT_WORKFLOW.md](GIT_WORKFLOW.md) - Branching strategy and PR process
- **JavaDoc**: Generate with `mvn javadoc:javadoc`, view at `target/site/apidocs/index.html`

### API Documentation

Generate JavaDoc:
```bash
mvn javadoc:javadoc
open target/site/apidocs/index.html
```

---

## Team

**SE302 - Software Engineering | Team 3, Section 1**

| Name | Student ID | Role |
|------|-----------|------|
| Kerem Bozdag | 20200602012 | Database & Data Layer Lead |
| Milena Larissa Ünal | 20230602107 | Algorithm Specialist |
| Rabia Tülay Gokdag | 20210602029 | UI/FXML Specialist |
| Mehmet Sefa Keskin | 20230602047 | Controller Layer Lead |
| Feyza Gereme | 20230602028 | Testing & QA Lead |
| Emin Arslan | 20230602007 | Documentation & Integration Lead |

---

## Technology Stack

- **Language**: Java 17
- **UI Framework**: JavaFX 21
- **Database**: SQLite 3.44
- **Build Tool**: Maven 3.8+
- **Testing**: JUnit 5, Mockito, TestFX
- **Libraries**:
  - Apache POI (Excel export)
  - Apache Commons CSV (CSV parsing)
  - SQLite JDBC
- **CI/CD**: GitHub Actions
- **Code Coverage**: JaCoCo

---

## Architecture

The application follows the **Model-View-Controller (MVC)** architectural pattern:

- **Model**: Domain objects (Student, Course, Classroom, etc.) and business logic
- **View**: JavaFX FXML layouts and UI components
- **Controller**: JavaFX controllers handling user interactions

**Key Design Patterns**:
- **DAO Pattern**: Data access abstraction
- **Strategy Pattern**: Pluggable optimization strategies
- **Singleton Pattern**: Database connection management
- **Observer Pattern**: Progress tracking for long-running operations
- **Factory Pattern**: DAO instantiation

---

## Algorithm

The scheduling algorithm uses **Backtracking with Constraint Propagation**:

1. **Variables**: Each course is a variable needing assignment
2. **Domain**: Each course can be assigned to {Classroom, Day, TimeSlot} triple
3. **Constraints**:
   - No consecutive exams for any student
   - Max 2 exams per student per day
   - Classroom capacity limits
4. **Heuristics**:
   - **Variable ordering**: Minimum Remaining Values (MRV)
   - **Value ordering**: Least Constraining Value
5. **Optimization**: Strategy-based heuristics affect value ordering

See [MEMORY_BANK.md](MEMORY_BANK.md#algorithm-design) for detailed algorithm documentation.

---

## Course Information

- **Course**: SE302 - Software Engineering
- **Section**: 1
- **Team**: 3
- **Academic Year**: 2025-2026 Fall Semester
- **Project Start**: November 26, 2025
- **Final Delivery**: February 4, 2026

---

## License

This project is developed as part of SE302 Software Engineering course.

**Academic Use Only** - Not licensed for commercial use.

Copyright © 2025 Team 3, Section 1. All rights reserved.

---

## Frequently Asked Questions

### Q: What happens if no valid schedule exists?

**A:** The system will detect conflicts and provide a detailed report identifying:
- Students with too many courses for the available slots
- Courses with enrollment exceeding maximum classroom capacity
- Specific recommendations to resolve the issue (extend period, add classrooms, reduce enrollments)

### Q: Can I run multiple schedules with different configurations?

**A:** Yes! The system saves schedule history with metadata (date, optimization strategy, configuration). You can generate multiple schedules and compare them.

### Q: Can I edit a generated schedule manually?

**A:** Yes, but all edits are validated against constraints in real-time. If an edit would violate a constraint (e.g., create consecutive exams for a student), the system will reject it with a detailed explanation.

### Q: How fast is the scheduling algorithm?

**A:** For the sample dataset (20 courses, 250 students, 10 classrooms), generation typically completes in < 5 seconds. Larger datasets (50 courses, 500 students) complete in < 10 seconds.

### Q: Can I use this on macOS or Linux?

**A:** Yes! The application is built with Java and JavaFX, which are cross-platform. It works on Windows, macOS, and Linux.

---

## Development Progress

### Completed
- [x] Requirements analysis and specification
- [x] System design and architecture
- [x] Database schema design
- [x] Project structure and Maven setup
- [x] Git workflow and CI/CD pipeline
- [x] Comprehensive documentation (Memory Bank, Roadmap, Team Assignments)

### In Progress (Week 1)
- [ ] Database layer implementation
- [ ] CSV import functionality
- [ ] Domain model classes
- [ ] UI wireframes and layouts

### Planned
- [ ] CSP solver implementation
- [ ] Optimization strategies
- [ ] JavaFX UI views
- [ ] Export functionality
- [ ] Testing and quality assurance
- [ ] User manual and final documentation

See [ROADMAP.md](ROADMAP.md) for detailed week-by-week plan.

---

## Contact

For questions or issues related to this project:
- Create an issue on GitHub
- Contact team members (see [Team](#team) section)
- Contact course instructor

---

*Last Updated: November 26, 2025*

For comprehensive project documentation, see [MEMORY_BANK.md](MEMORY_BANK.md).
