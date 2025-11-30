# GETTING STARTED - Exam Scheduling System

**Welcome to the Exam Scheduling System project!**

This document will help you get started with the project quickly.

---

## Project Setup Complete! ✅

The project foundation has been fully set up with:

### Documentation Created
- ✅ **MEMORY_BANK.md** - Comprehensive project documentation (100+ pages)
- ✅ **ROADMAP.md** - Detailed 10-week development plan
- ✅ **TEAM_ASSIGNMENTS.md** - Team roles and responsibilities
- ✅ **GIT_WORKFLOW.md** - Git branching strategy and PR process
- ✅ **README.md** - Project overview and quick start guide

### Project Structure
- ✅ Maven `pom.xml` with all dependencies configured
- ✅ Directory structure created (`src/main/java`, `src/test/java`, `src/main/resources`)
- ✅ Package structure ready (`model`, `dao`, `service`, `algorithm`, `controller`, `util`)
- ✅ `.gitignore` configured for Java/Maven project
- ✅ GitHub Actions CI/CD pipeline (`.github/workflows/ci.yml`)

---

## Quick Start for Team Members

### 1. Clone the Repository

```bash
git clone https://github.com/sabazadam/se302.git
cd se302
```

### 2. Install Prerequisites

**Required:**
- Java JDK 17 or higher - [Download](https://adoptium.net/)
- Maven 3.8+ - [Download](https://maven.apache.org/download.cgi)
- Git - [Download](https://git-scm.com/)

**Recommended:**
- IntelliJ IDEA (recommended) or Eclipse - [Download IntelliJ](https://www.jetbrains.com/idea/download/)
- Scene Builder (for FXML editing) - [Download](https://gluonhq.com/products/scene-builder/)

### 3. Build the Project

```bash
mvn clean install
```

This will:
- Download all dependencies
- Compile the code
- Run tests (when we have them)
- Create a JAR file

### 4. Open in IDE

**IntelliJ IDEA:**
1. File → Open
2. Select the `pom.xml` file
3. Click "Open as Project"
4. Wait for dependencies to download

**Eclipse:**
1. File → Import
2. Maven → Existing Maven Projects
3. Select the project directory
4. Finish

### 5. Configure JDK 17

**IntelliJ:**
- File → Project Structure → Project → SDK → Select JDK 17

**Eclipse:**
- Right-click project → Properties → Java Build Path → Libraries → Add JDK 17

---

## First Steps for Each Team Member

### Database Lead (Kerem)

**Week 1 Tasks:**
1. Read [MEMORY_BANK.md](MEMORY_BANK.md) - Database Schema section
2. Create `schema.sql` in `src/main/resources/database/`
3. Implement DAO interfaces
4. Start with StudentDAO and test it

**Getting Started:**
```bash
git checkout develop
git pull origin develop
git checkout -b feature/database-schema

# Create your database schema file
# Edit src/main/resources/database/schema.sql
# Then commit and push
```

### Algorithm Specialist (Milena)

**Week 1 Tasks:**
1. Read [MEMORY_BANK.md](MEMORY_BANK.md) - Algorithm Design section
2. Create domain model classes (Student, Course, Classroom, etc.)
3. Start designing constraint validator

**Getting Started:**
```bash
git checkout develop
git pull origin develop
git checkout -b feature/domain-models

# Create model classes in src/main/java/com/se302/examscheduler/model/
# Start with Student.java, Course.java
```

### UI/FXML Specialist (Rabia)

**Week 1 Tasks:**
1. Read [MEMORY_BANK.md](MEMORY_BANK.md) - User Interface Design section
2. Install Scene Builder
3. Create wireframes for main window
4. Start FXML for MainWindow

**Getting Started:**
```bash
git checkout develop
git pull origin develop
git checkout -b feature/ui-main-window

# Create FXML files in src/main/resources/fxml/
# Design with Scene Builder
```

### Controller Lead (Sefa)

**Week 1 Tasks:**
1. Read [MEMORY_BANK.md](MEMORY_BANK.md) - Controller Layer section
2. Create Main.java application entry point
3. Design controller architecture

**Getting Started:**
```bash
git checkout develop
git pull origin develop
git checkout -b feature/main-controller

# Create Main.java in src/main/java/com/se302/examscheduler/
# Set up JavaFX application structure
```

### Testing Lead (Feyza)

**Week 1 Tasks:**
1. Read [MEMORY_BANK.md](MEMORY_BANK.md) - Testing Strategy section
2. Set up test utilities (TestDataBuilder)
3. Verify CI/CD pipeline works
4. Create test data fixtures

**Getting Started:**
```bash
git checkout develop
git pull origin develop
git checkout -b feature/test-infrastructure

# Create test utilities in src/test/java/com/se302/examscheduler/util/
# Create test CSV files in src/test/resources/
```

### Documentation Lead (Emin)

**Week 1 Tasks:**
1. Review all documentation created
2. Set up JavaDoc generation
3. Create documentation templates
4. Review first PRs from team

**Getting Started:**
```bash
# Generate JavaDoc
mvn javadoc:javadoc
open target/site/apidocs/index.html

# Set up documentation watching
# Review team members' code for documentation quality
```

---

## Important Files to Read

### MUST READ (Everyone)
1. **README.md** - Project overview and quick start
2. **MEMORY_BANK.md** - Comprehensive documentation (read your section)
3. **ROADMAP.md** - Week-by-week plan (see your tasks)
4. **TEAM_ASSIGNMENTS.md** - Your role and responsibilities
5. **GIT_WORKFLOW.md** - How to use Git, commit messages, PRs

### Your Section in MEMORY_BANK.md
- **Kerem:** Database Schema, DAO Layer
- **Milena:** Algorithm Design, Constraint Validation
- **Rabia:** User Interface Design, UI Components
- **Sefa:** API Contracts (Controller), MVC Architecture
- **Feyza:** Testing Strategy, Test Types
- **Emin:** All sections (for review)

---

## Git Workflow Reminder

### Creating a Feature Branch

```bash
# Always start from latest develop
git checkout develop
git pull origin develop

# Create your feature branch
git checkout -b feature/your-feature-name

# Make changes, then commit
git add .
git commit -m "feat(scope): brief description"

# Push to remote
git push -u origin feature/your-feature-name
```

### Commit Message Format

```
<type>(<scope>): <subject>

Examples:
feat(dao): Add StudentDAO with batch insert
fix(algorithm): Correct consecutive exam constraint
test(dao): Add unit tests for StudentDAO
docs(readme): Update installation instructions
```

### Creating a Pull Request

1. Push your feature branch to GitHub
2. Go to GitHub repository
3. Click "New Pull Request"
4. Base: `develop`, Compare: `feature/your-feature-name`
5. Fill in PR template
6. Request at least 1 reviewer
7. Wait for approval and CI to pass
8. Merge when approved

---

## Useful Commands

### Maven Commands

```bash
# Clean and build
mvn clean install

# Run tests
mvn test

# Run specific test
mvn test -Dtest=StudentDAOTest

# Generate test coverage report
mvn clean test jacoco:report
open target/site/jacoco/index.html

# Generate JavaDoc
mvn javadoc:javadoc
open target/site/apidocs/index.html

# Run application (when Main.java is ready)
mvn javafx:run

# Package into JAR
mvn package
```

### Git Commands

```bash
# Check status
git status

# View changes
git diff

# Stage files
git add file.java
git add .

# Commit
git commit -m "message"

# Push
git push origin feature-name

# Pull latest develop
git checkout develop
git pull origin develop

# Rebase your branch on develop
git checkout feature-name
git rebase develop

# View commit history
git log --oneline

# View branches
git branch -a
```

---

## Project Statistics

- **Team Size:** 6 members
- **Duration:** 10 weeks (Nov 26, 2025 - Feb 4, 2026)
- **Lines of Documentation:** ~15,000+ lines
- **Files Created:** 8 core documentation files
- **Technology Stack:** Java 17, JavaFX 21, SQLite, Maven
- **Target:** Fully functional desktop application with >70% test coverage

---

## Weekly Meeting Schedule

**Monday (30 min) - Sprint Planning:**
- Review last week's progress
- Assign this week's tasks
- Discuss blockers

**Wednesday (15-30 min) - Mid-Week Sync:**
- Quick status updates
- Identify issues early
- Schedule pair programming if needed

**Friday (30-45 min) - Demo & Retrospective:**
- Each person demos their work
- Celebrate wins
- Discuss improvements
- Brief planning for next week

---

## Communication Channels

**Synchronous:**
- Weekly meetings (Monday, Wednesday, Friday)
- Pair programming sessions (as needed)

**Asynchronous:**
- WhatsApp/Discord - Daily updates, quick questions
- GitHub - Code reviews, PR discussions
- Email - Formal communication with professor

---

## Success Metrics

### Week 1 (Current Week)
- **Expected Completion:** 10%
- **Key Milestone:** Project setup, infrastructure, initial designs
- **Deliverables:**
  - Database schema script
  - Algorithm design document
  - UI wireframes
  - CI/CD pipeline working

### By Week 3 (End of Phase 1)
- **Expected Completion:** 30%
- **Key Milestone:** Foundation complete
- **Deliverables:**
  - Database layer working
  - CSV import functional
  - Configuration screen working
  - All team members can run project locally

---

## Getting Help

### If You're Stuck

1. **Try for 2 hours** - Google, documentation, StackOverflow
2. **Ask in team chat** - Someone may have solved it
3. **Pair program** - Schedule session with teammate
4. **Ask Emin** - Integration Lead can help with architecture questions
5. **Ask Professor** - For requirements clarification

### Common Issues

**Maven dependency download fails:**
```bash
# Clear Maven cache and retry
rm -rf ~/.m2/repository
mvn clean install
```

**IDE doesn't recognize Java 17:**
- Verify JDK 17 is installed: `java -version`
- Configure project SDK in IDE settings

**Git merge conflicts:**
- See GIT_WORKFLOW.md section on "Merge Conflict Resolution"
- Ask Emin if you need help resolving

**Tests won't run:**
- Ensure Maven Surefire plugin is configured in pom.xml (already done)
- Check test file naming: must end with `Test.java`

---

## Next Steps

### For the Team

1. **Today (Nov 26):**
   - All members clone repository
   - Set up development environment
   - Read assigned sections of MEMORY_BANK.md

2. **This Week (Week 1):**
   - Each member starts assigned tasks from ROADMAP.md
   - Create first feature branches
   - Make first commits

3. **Friday Meeting (Nov 30):**
   - Each person demos progress
   - Database schema presented
   - UI wireframes presented
   - Algorithm pseudocode presented

---

## Resources

### Learning Resources

**Java & JavaFX:**
- JavaFX Official Docs: https://openjfx.io/javadoc/17/
- JavaFX Tutorial: https://openjfx.io/openjfx-docs/

**SQLite:**
- SQLite Tutorial: https://www.sqlitetutorial.net/
- SQLite Java: https://www.sqlitetutorial.net/sqlite-java/

**CSP Algorithms:**
- Russell & Norvig AI Textbook: Chapter 6
- Online: https://artint.info/2e/html/ArtInt2e.Ch4.html

**Testing:**
- JUnit 5: https://junit.org/junit5/docs/current/user-guide/
- Mockito: https://site.mockito.org/

### Tools

**Required:**
- Java JDK 17: https://adoptium.net/
- Maven: https://maven.apache.org/download.cgi
- Git: https://git-scm.com/

**Recommended:**
- IntelliJ IDEA: https://www.jetbrains.com/idea/download/
- Scene Builder: https://gluonhq.com/products/scene-builder/
- DB Browser for SQLite: https://sqlitebrowser.org/

---

## Questions?

- Check MEMORY_BANK.md for detailed answers
- Ask in team chat
- Contact Emin (Documentation Lead) for documentation questions
- Contact team members for their specialty areas
- Email professor for requirements clarification

---

**Last Updated:** November 26, 2025

**Good luck team! Let's build an amazing exam scheduler! 🚀**

---

*For comprehensive documentation, see [MEMORY_BANK.md](MEMORY_BANK.md)*
