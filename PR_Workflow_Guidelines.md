# PR Workflow Guidelines

This document outlines our team's approach to managing Pull Requests (PRs) and branching strategies for efficient development workflow.

## Core Principles

- **Small, focused PRs**: Easier to review, lower risk of conflicts
- **Continuous progress**: Minimize blocking time waiting for reviews
- **Clear communication**: Document dependencies between branches and PRs
- **Quality first**: Never sacrifice code quality for speed

## Branching Strategies

We employ two main workflow patterns depending on the task complexity and timeline:

### 1. Sequential Workflow (Standard Approach)

Best for simple features or when working with stable, well-established parts of the codebase.

```
main
  └── feature/X
      ├── PR: Feature X (wait for review & merge)
      └── feature/Y (create after X is merged)
          └── PR: Feature Y
```

**Process:**

1. Branch off `main` to create `feature/X`
2. Complete work on Feature X
3. Submit PR for Feature X
4. Wait for review and merge
5. Branch off `main` to create `feature/Y`
6. Repeat

**Pros:** Clean history, minimal conflicts, straightforward process
**Cons:** Waiting for reviews blocks progress

### 2. Parallel Workflow (Stacked PRs)

Best for complex features that can be broken down into related components.

```
main
  └── feature/base
      ├── feature/component-A
      │   └── PR: Component A
      └── feature/component-B (branched from component-A)
          └── PR: Component B (depends on A)
```

**Process:**

1. Branch off `main` to create a base feature branch (optional)
2. Create component branches from this base (or from `main`)
3. Complete work on Component A
4. Submit PR for Component A
5. While waiting for review, branch from Component A to create Component B
6. Work on Component B while Component A is being reviewed
7. When Component A is merged, rebase Component B onto the updated `main`
8. Continue development and submit PR for Component B

**Pros:** Continuous progress, minimizes blocking time
**Cons:** Requires more git management, potential rebase complexities

## PR Guidelines

### PR Structure

Each PR should:

- Implement a single logical change or feature
- Include tests for the new functionality
- Contain a descriptive PR template with:
  - Purpose of the changes
  - Links to related issues/tickets
  - Testing instructions
  - **Dependencies on other PRs** (critical for parallel workflow)

### Status Indicators

Use GitHub's PR features to indicate status:

- **Draft PRs**: Early work seeking initial feedback
- **Ready for Review**: Complete work awaiting formal review
- **Labels**: Use custom labels like `blocked`, `depends-on-#123`, etc.

### Managing Dependent PRs

When using the parallel workflow:

1. Clearly mark PR dependencies in the description
2. Set the base branch of dependent PRs appropriately
3. Use comments to update reviewers on status changes in dependencies
4. Consider using tools like [GitHub's autolinked references](https://docs.github.com/en/github/writing-on-github/autolinked-references-and-urls)

## Example: CRUD Feature Implementation

### Sequential Approach:

```
main
  └── feature/crud-list
      ├── PR: CRUD List View
      │   └── (wait for review & merge)
      └── feature/crud-create
          ├── PR: CRUD Create Form
          │   └── (wait for review & merge)
          └── feature/crud-edit
              └── PR: CRUD Edit Form
```

### Parallel Approach:

```
main
  └── feature/crud-base
      ├── feature/crud-list
      │   └── PR: CRUD List View
      ├── feature/crud-create (branched from crud-list)
      │   └── PR: CRUD Create Form (depends on List View)
      └── feature/crud-edit (branched from crud-create)
          └── PR: CRUD Edit Form (depends on Create Form)
```

## Git Commands Reference

### Setting Up Parallel Work

```bash
# Create base feature branch
git checkout main
git pull
git checkout -b feature/crud-base

# Create first component branch
git checkout -b feature/crud-list

# Work on and commit changes...
git add .
git commit -m "Implement CRUD list view"
git push -u origin feature/crud-list

# Create PR for list view component

# Create second component branch while waiting
git checkout feature/crud-list
git checkout -b feature/crud-create

# Work on create component...
```

### Handling Updates to Dependencies

```bash
# When crud-list is approved and merged to main
git checkout main
git pull

# Update your create branch with changes from main
git checkout feature/crud-create
git rebase main

# Fix any conflicts and continue
git add .
git rebase --continue

# Force push the rebased branch
git push --force-with-lease
```

## Conclusion

This workflow documentation provides guidelines, not strict rules. The specific approach should be selected based on:

1. Project complexity
2. Team size and availability for reviews
3. Deadline constraints
4. Risk tolerance

Remember that communication is key, especially when using the parallel approach with dependent PRs.
