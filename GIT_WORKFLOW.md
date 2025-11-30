# GIT WORKFLOW - EXAM SCHEDULING SYSTEM

**Project:** Exam Planner Desktop Application
**Team:** Team 3, Section 1
**Version Control:** Git + GitHub

---

## BRANCHING STRATEGY

### Branch Structure

```
main (production-ready code)
  └── develop (integration branch)
       ├── feature/database-schema (Kerem's work)
       ├── feature/csv-import (Kerem's work)
       ├── feature/csp-solver (Milena's work)
       ├── feature/constraint-validation (Milena's work)
       ├── feature/ui-main-window (Rabia's work)
       ├── feature/import-view (Rabia's work)
       ├── feature/import-controller (Sefa's work)
       ├── feature/generation-controller (Sefa's work)
       ├── feature/dao-tests (Feyza's work)
       ├── feature/algorithm-tests (Feyza's work)
       └── bugfix/constraint-validation-issue (Any team member)
```

### Branch Types

#### 1. `main` Branch
- **Purpose:** Production-ready, stable code only
- **Protection:** Requires PR approval from Emin (Documentation Lead)
- **Merging:** Only from `develop` branch
- **Tagging:** Every merge to main gets a version tag (v1.0.0, v1.1.0, etc.)
- **Never:** Commit directly to `main`

#### 2. `develop` Branch
- **Purpose:** Integration branch for all features
- **Protection:** Requires at least 1 PR approval
- **Testing:** All tests must pass before merge
- **Never:** Commit directly to `develop` (always use feature branches)

#### 3. `feature/*` Branches
- **Purpose:** New features or enhancements
- **Naming:** `feature/short-description` (e.g., `feature/csv-import`)
- **Base:** Created from `develop`
- **Merge to:** `develop` via Pull Request
- **Lifetime:** Delete after successful merge
- **Examples:**
  - `feature/database-schema`
  - `feature/csp-solver`
  - `feature/classroom-view`
  - `feature/export-excel`

#### 4. `bugfix/*` Branches
- **Purpose:** Fix bugs found during development
- **Naming:** `bugfix/issue-description` (e.g., `bugfix/constraint-validation`)
- **Base:** Created from `develop`
- **Merge to:** `develop` via Pull Request
- **Examples:**
  - `bugfix/duplicate-student-detection`
  - `bugfix/constraint-validation-boundary`
  - `bugfix/ui-table-alignment`

#### 5. `hotfix/*` Branches (Emergency Only)
- **Purpose:** Critical fixes for production code
- **Naming:** `hotfix/critical-issue`
- **Base:** Created from `main`
- **Merge to:** Both `main` AND `develop`
- **Use:** Only for critical bugs in production
- **Examples:**
  - `hotfix/database-corruption`
  - `hotfix/solver-infinite-loop`

#### 6. `refactor/*` Branches
- **Purpose:** Code refactoring (no functional changes)
- **Naming:** `refactor/component-name`
- **Base:** Created from `develop`
- **Merge to:** `develop` via Pull Request
- **Examples:**
  - `refactor/dao-layer-optimization`
  - `refactor/algorithm-performance`

#### 7. `test/*` Branches
- **Purpose:** Adding or improving tests
- **Naming:** `test/component-name`
- **Base:** Created from `develop`
- **Merge to:** `develop` via Pull Request
- **Examples:**
  - `test/dao-integration-tests`
  - `test/constraint-validator-edge-cases`

#### 8. `docs/*` Branches
- **Purpose:** Documentation updates
- **Naming:** `docs/document-name`
- **Base:** Created from `develop`
- **Merge to:** `develop` via Pull Request
- **Examples:**
  - `docs/algorithm-design`
  - `docs/user-manual`

---

## GIT WORKFLOW COMMANDS

### 1. Starting a New Feature

```bash
# Make sure you have latest develop
git checkout develop
git pull origin develop

# Create new feature branch
git checkout -b feature/csv-import

# Work on your feature (make changes, add files)
# ...

# Stage and commit your changes
git add src/main/java/com/se302/examscheduler/service/ImportService.java
git commit -m "feat(import): Add CSV parser for student data"

# Push to remote
git push origin feature/csv-import

# If branch doesn't exist on remote yet
git push -u origin feature/csv-import
```

### 2. Making Commits

```bash
# Check what files have changed
git status

# View changes before committing
git diff

# Stage specific files
git add file1.java file2.java

# Or stage all changes
git add .

# Commit with descriptive message
git commit -m "feat(dao): Implement StudentDAO with batch insert"

# Push to remote branch
git push origin feature/csv-import
```

### 3. Keeping Your Branch Up-to-Date

```bash
# While working on feature branch, periodically sync with develop

# Fetch latest changes from remote
git fetch origin

# Rebase your branch on latest develop (preferred for clean history)
git rebase origin/develop

# Or merge develop into your branch (creates merge commit)
git merge origin/develop

# If conflicts occur during rebase
# 1. Resolve conflicts in files
# 2. Stage resolved files
git add resolved-file.java
# 3. Continue rebase
git rebase --continue

# Force push after rebase (only if you haven't pushed yet or working alone on branch)
git push origin feature/csv-import --force-with-lease
```

### 4. Creating a Pull Request

**After pushing your feature branch:**

1. Go to GitHub repository
2. Click "Pull requests" tab
3. Click "New pull request"
4. Set base branch: `develop`
5. Set compare branch: `feature/csv-import`
6. Fill in PR template:
   - Title: Brief description (e.g., "Add CSV import functionality")
   - Description: Detailed changes, testing done, screenshots if UI
   - Link related issues: "Closes #15"
7. Request reviewers (at least one team member)
8. Assign yourself
9. Add labels (feature, bug, documentation, etc.)
10. Create pull request

### 5. Reviewing a Pull Request

**As a reviewer:**

```bash
# Fetch the PR branch
git fetch origin
git checkout feature/csv-import

# Test the changes locally
mvn clean test
mvn javafx:run

# Review code in IDE
# Leave comments on GitHub PR page

# If changes requested
# - Author makes changes
# - Author pushes to same branch (PR updates automatically)

# If approved
# - Click "Approve" on GitHub
# - Merge when all checks pass
```

### 6. Merging a Pull Request

**After PR is approved:**

**Option A: Squash and Merge (Recommended)**
- Combines all commits into one clean commit
- Use for most feature branches
- Keeps develop history clean
- Click "Squash and merge" on GitHub

**Option B: Rebase and Merge**
- Replays all commits on top of develop
- Use for well-organized commit history
- Click "Rebase and merge" on GitHub

**Option C: Merge Commit**
- Creates a merge commit preserving all commits
- Use for large features with meaningful commit history
- Click "Merge pull request" on GitHub

**After merging:**

```bash
# Delete the remote feature branch (GitHub does this automatically)

# Delete your local feature branch
git checkout develop
git pull origin develop
git branch -d feature/csv-import

# If git complains, force delete
git branch -D feature/csv-import
```

### 7. Releasing to Main

**When develop is ready for release:**

```bash
# Ensure develop is stable
git checkout develop
git pull origin develop
mvn clean test  # All tests must pass

# Create PR from develop to main
# On GitHub:
# 1. New Pull Request: develop -> main
# 2. Title: "Release v1.0.0"
# 3. Description: List of features included
# 4. Request review from Emin (Documentation Lead)

# After approval and merge
git checkout main
git pull origin main

# Tag the release
git tag -a v1.0.0 -m "Release version 1.0.0 - Initial delivery"
git push origin v1.0.0
```

---

## COMMIT MESSAGE GUIDELINES

### Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Types

| Type | Description | Example |
|------|-------------|---------|
| `feat` | New feature | `feat(import): Add CSV import for students` |
| `fix` | Bug fix | `fix(csp-solver): Correct consecutive exam check` |
| `refactor` | Code refactoring (no functional change) | `refactor(dao): Optimize batch insert queries` |
| `test` | Adding or updating tests | `test(algorithm): Add constraint validation tests` |
| `docs` | Documentation changes | `docs(readme): Update installation instructions` |
| `style` | Code style changes (formatting, no logic change) | `style(dao): Fix indentation and spacing` |
| `chore` | Build process, dependency updates | `chore(pom): Update JavaFX version to 21.0.1` |
| `perf` | Performance improvements | `perf(solver): Add memoization for constraint checks` |

### Scope

Common scopes:
- `dao` - Data Access layer
- `algorithm` - CSP solver and algorithm layer
- `ui` - User interface (FXML, views)
- `controller` - Controller layer
- `import` - Import functionality
- `export` - Export functionality
- `config` - Configuration
- `test` - Testing
- `docs` - Documentation

### Subject

- Use imperative mood ("Add" not "Added" or "Adds")
- Don't capitalize first letter
- No period at the end
- Max 50 characters

### Body (Optional)

- Explain **why** the change was made, not **what**
- Wrap at 72 characters
- Separate from subject with blank line

### Footer (Optional)

- Reference issues: `Closes #15`, `Fixes #42`, `Relates to #23`
- Breaking changes: `BREAKING CHANGE: API endpoint changed`

### Examples

**Good Examples:**

```
feat(import): Add CSV parser for student data

Implemented CSVParser utility class to read student IDs from CSV file.
Includes validation for duplicates and empty lines.
Uses Apache Commons CSV library for robust parsing.

Closes #15
```

```
fix(csp-solver): Correct consecutive exam constraint at day boundaries

Previous implementation didn't handle case where last slot of day N
and first slot of day N+1 are consecutive. Now uses absolute slot
numbering to correctly identify consecutive slots across days.

Fixes #42
```

```
test(dao): Add integration tests for StudentDAO

Added tests for:
- Batch insert with 100 students
- Duplicate detection
- Transaction rollback on error

Coverage for StudentDAO increased from 65% to 92%.
```

```
refactor(algorithm): Extract domain filtering into separate method

Extracted domain pre-filtering logic from solve() into
filterInvalidAssignments() for better readability and testability.
No functional changes.
```

**Bad Examples:**

❌ `Update files` - Too vague
❌ `Fixed bug` - What bug? Where?
❌ `WIP` - Work in progress commits should be rebased before PR
❌ `asdfgh` - Meaningless message
❌ `Updated StudentDAO.java and CourseDAO.java and ImportService.java` - Too long, no context

---

## PULL REQUEST TEMPLATE

Create `.github/PULL_REQUEST_TEMPLATE.md`:

```markdown
## Description
<!-- Brief description of what this PR does -->

## Type of Change
- [ ] New feature (feat)
- [ ] Bug fix (fix)
- [ ] Refactoring (refactor)
- [ ] Documentation (docs)
- [ ] Testing (test)
- [ ] Build/dependency update (chore)

## Changes Made
<!-- List specific changes -->
-
-
-

## Testing Done
<!-- How did you test these changes? -->
- [ ] Unit tests added/updated
- [ ] Integration tests added/updated
- [ ] Manual testing performed
- [ ] All existing tests pass

**Test coverage:** [X]% (before) → [Y]% (after)

## Screenshots (if applicable)
<!-- For UI changes, add before/after screenshots -->

## Checklist
- [ ] Code follows Java naming conventions
- [ ] JavaDoc added for public methods
- [ ] No compiler warnings
- [ ] All tests pass locally (`mvn clean test`)
- [ ] Updated MEMORY_BANK.md if design decisions changed
- [ ] Updated user documentation if user-facing changes

## Related Issues
<!-- Link to issues: Closes #123, Fixes #456, Relates to #789 -->

## Reviewer Notes
<!-- Anything specific you want reviewers to focus on? -->
```

---

## CODE REVIEW CHECKLIST

### For Reviewers

**Before approving, verify:**

#### Code Quality
- [ ] Follows Java naming conventions (camelCase variables, PascalCase classes)
- [ ] No magic numbers (use named constants: `private static final int MAX_CAPACITY = 40;`)
- [ ] Meaningful variable and method names
- [ ] Appropriate access modifiers (private, public, protected)
- [ ] No code duplication (DRY principle)
- [ ] Proper exception handling (try-catch where appropriate)
- [ ] No commented-out code
- [ ] No TODO comments (or documented in issues)

#### Architecture
- [ ] Code in correct layer (DAO/Service/Controller/View)
- [ ] No tight coupling (DAO doesn't call UI, etc.)
- [ ] Uses appropriate design patterns
- [ ] Follows MVC separation

#### Testing
- [ ] Unit tests added for new methods
- [ ] Tests cover edge cases and error scenarios
- [ ] All existing tests still pass
- [ ] Test names are descriptive (`testImportStudents_WithDuplicates_ShouldRejectDuplicates`)
- [ ] No tests commented out

#### Documentation
- [ ] JavaDoc added for all public methods
- [ ] JavaDoc includes @param, @return, @throws tags
- [ ] Complex algorithms have explanatory comments
- [ ] MEMORY_BANK.md updated if design changed

#### Functionality
- [ ] Code does what PR description says
- [ ] No obvious bugs
- [ ] Error messages are user-friendly
- [ ] Edge cases handled

#### Performance
- [ ] No obvious performance issues (e.g., O(n²) loops that could be O(n))
- [ ] Database queries use indexes efficiently
- [ ] No memory leaks (resources closed properly)

### Review Comments

**Be constructive:**
- ✅ "Consider using a HashMap here instead of iterating through the list for O(1) lookup instead of O(n)"
- ❌ "This code is terrible"

**Ask questions:**
- ✅ "Why did you choose to use a synchronized block here? Are we expecting concurrent access?"
- ❌ "This is wrong"

**Suggest alternatives:**
- ✅ "What about extracting this into a separate method for better testability?"
- ❌ "Refactor this"

**Praise good work:**
- ✅ "Nice use of the Strategy pattern here! Makes it easy to add new optimization strategies."
- ✅ "Great test coverage on this feature!"

---

## MERGE CONFLICT RESOLUTION

### When Conflicts Occur

**Scenario:** You're working on `feature/csv-import` and someone else merged changes to `develop` that conflict with your code.

```bash
# Update your local develop
git checkout develop
git pull origin develop

# Try to rebase your feature branch
git checkout feature/csv-import
git rebase develop

# If conflicts occur, Git will pause and show:
# CONFLICT (content): Merge conflict in src/main/java/.../ImportService.java
```

### Resolving Conflicts

1. **Open conflicted file in IDE** (e.g., `ImportService.java`)

2. **Look for conflict markers:**
```java
<<<<<<< HEAD (your changes)
public void importStudents(String filePath) {
    // Your implementation
}
=======
public ImportResult importStudents(String filePath) throws IOException {
    // Their implementation
}
>>>>>>> develop (their changes)
```

3. **Decide how to resolve:**
   - Keep your changes
   - Keep their changes
   - Combine both changes
   - Rewrite entirely

4. **Edit the file to final version:**
```java
public ImportResult importStudents(String filePath) throws IOException {
    // Combined implementation
}
```

5. **Mark as resolved:**
```bash
git add src/main/java/.../ImportService.java
```

6. **Continue rebase:**
```bash
git rebase --continue
```

7. **If you made a mistake, abort:**
```bash
git rebase --abort  # Starts over
```

8. **Force push after successful rebase:**
```bash
git push origin feature/csv-import --force-with-lease
```

### Preventing Conflicts

- **Communicate:** Tell team when working on same files
- **Small PRs:** Smaller changes merge faster, less chance of conflicts
- **Sync often:** Rebase on develop daily
- **Modular code:** Work on separate files/classes when possible

---

## COMMON GIT COMMANDS REFERENCE

### Basic Operations

```bash
# Check current branch and status
git status

# View commit history
git log
git log --oneline
git log --graph --oneline --all

# View changes
git diff                    # Unstaged changes
git diff --staged           # Staged changes
git diff develop            # Difference with develop branch

# Stash changes temporarily
git stash                   # Save changes
git stash list              # List stashes
git stash pop               # Apply and remove stash
git stash apply             # Apply but keep stash

# Undo changes
git checkout -- file.java   # Discard changes in file
git reset HEAD file.java    # Unstage file
git reset --hard HEAD       # Discard all changes (DANGEROUS)
git reset --hard origin/develop  # Reset to remote develop (DANGEROUS)

# Amend last commit (if not pushed yet)
git commit --amend -m "New message"
git add forgotten-file.java
git commit --amend --no-edit  # Add file to last commit
```

### Branch Operations

```bash
# List branches
git branch                  # Local branches
git branch -r               # Remote branches
git branch -a               # All branches

# Create and switch
git checkout -b feature/new-feature
git switch -c feature/new-feature  # Newer syntax

# Switch branches
git checkout develop
git switch develop          # Newer syntax

# Delete branches
git branch -d feature/old   # Delete if merged
git branch -D feature/old   # Force delete
git push origin --delete feature/old  # Delete remote branch

# Rename branch
git branch -m old-name new-name
```

### Remote Operations

```bash
# View remotes
git remote -v

# Fetch updates from remote
git fetch origin
git fetch --all

# Pull changes (fetch + merge)
git pull origin develop

# Push changes
git push origin feature/csv-import
git push -u origin feature/csv-import  # Set upstream

# Push tags
git push origin v1.0.0
git push origin --tags      # Push all tags
```

### Viewing History

```bash
# View commits
git log
git log --oneline
git log --graph --oneline --all --decorate

# View specific file history
git log -- path/to/file.java

# View changes in a commit
git show <commit-hash>

# View who changed each line (blame)
git blame file.java
```

---

## TROUBLESHOOTING

### Problem: Accidentally committed to `develop` directly

```bash
# DON'T PUSH! If you haven't pushed:

# 1. Create a new branch with your changes
git branch feature/accidental-work

# 2. Reset develop to remote
git reset --hard origin/develop

# 3. Switch to feature branch and continue
git checkout feature/accidental-work
```

### Problem: Need to undo last commit (not pushed)

```bash
# Keep changes, undo commit
git reset --soft HEAD~1

# Discard changes and commit
git reset --hard HEAD~1  # CAREFUL - loses changes!
```

### Problem: Pushed sensitive data (password, API key)

```bash
# Immediately:
# 1. Change the password/API key
# 2. Contact Emin to help remove from history
# 3. Never commit credentials - use environment variables or config files (.gitignore them)
```

### Problem: Pull request has merge conflicts

```bash
# Fetch latest develop
git checkout develop
git pull origin develop

# Rebase your feature branch
git checkout feature/your-feature
git rebase develop

# Resolve conflicts (see Merge Conflict Resolution section)
# After resolving:
git rebase --continue

# Force push to update PR
git push origin feature/your-feature --force-with-lease
```

### Problem: Accidentally deleted local branch

```bash
# If you know the commit hash:
git checkout -b recovered-branch <commit-hash>

# If you don't know commit hash:
git reflog  # Shows recent actions
# Find the commit you want to recover
git checkout -b recovered-branch HEAD@{N}
```

---

## BEST PRACTICES

### DO:

✅ **Commit often** - Small, logical commits
✅ **Write descriptive commit messages** - Follow format guidelines
✅ **Pull/rebase before starting new work** - Stay in sync
✅ **Use feature branches** - Never commit directly to main/develop
✅ **Review your own changes before PR** - Use `git diff`
✅ **Test before pushing** - Run `mvn clean test`
✅ **Keep commits focused** - One feature/fix per commit
✅ **Delete merged branches** - Keep repository clean
✅ **Tag releases** - Version your milestones

### DON'T:

❌ **Don't commit directly to main or develop**
❌ **Don't commit large binary files** - Use .gitignore
❌ **Don't commit credentials or secrets** - Use environment variables
❌ **Don't force push to shared branches** - Only your own feature branches
❌ **Don't leave WIP commits in PR** - Squash or rebase first
❌ **Don't ignore merge conflicts** - Resolve properly
❌ **Don't commit commented-out code** - Delete it
❌ **Don't commit TODO comments** - Create issues instead
❌ **Don't commit debugging print statements** - Remove them

---

## TEAM SPECIFIC RULES

### 1. Protected Branches

- **`main`**: Requires PR approval from Emin + all tests passing
- **`develop`**: Requires 1 approval + all tests passing

### 2. Commit Frequency

- Commit at least once per day when actively working
- Push to remote at least every 2 days (for backup)
- Don't let feature branches live longer than 1 week

### 3. PR Size

- Keep PRs small (< 500 lines changed)
- If a feature is large, break into multiple PRs
- Easier to review = faster merge

### 4. Review Turnaround

- Reviewers must respond within 24 hours
- If blocked, ping on WhatsApp/Discord
- If urgent, ask for immediate review

### 5. Merge Strategy

- **Default:** Squash and merge (clean history)
- **Exception:** Large features with meaningful commits can use regular merge

### 6. Branch Naming

- Always use prefix: `feature/`, `bugfix/`, `test/`, `docs/`
- Use lowercase with hyphens: `feature/csv-import` not `feature/CSV_Import`
- Be descriptive: `feature/add-classroom-view` not `feature/view`

---

## WEEKLY WORKFLOW

### Monday (Sprint Start)

```bash
# Sync with develop
git checkout develop
git pull origin develop

# Create feature branch for week's work
git checkout -b feature/this-weeks-work

# Start coding...
```

### Wednesday (Mid-Week)

```bash
# Sync with develop (in case others merged)
git fetch origin
git rebase origin/develop

# Continue work...
```

### Friday (Sprint End)

```bash
# Ensure code is clean
mvn clean test

# Push feature branch
git push origin feature/this-weeks-work

# Create Pull Request on GitHub
# Demo in team meeting
```

---

## CHANGELOG

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | 2025-11-26 | Claude Code | Initial Git workflow documentation |

---

**END OF GIT WORKFLOW GUIDE**
