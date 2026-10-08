# Git Workflow

## Branch Management

### Branch Naming Convention

```
feature-<hash>--<human_name>
```

Where:

- `<hash>` — unique short identifier of the work
- `<human_name>` — short human-readable task name in kebab-case

### Workflow Steps

1. Ensure `dev` branch is up-to-date and working tree is clean
2. Create feature branch from `dev`:
   `feature-<hash>--<human_name>`
3. Work exclusively in the feature branch
4. Push feature branch to remote
5. Upon completion:
    - Run all necessary tests and checks
    - Push feature branch to remote
    - Create Pull Request: `feature-<hash>--<human_name>` → `dev`
    - Switch repositories back to `dev` branch

### PR Rules

- Always target `dev` branch
- Include links to task PR in all status messages
- Do not merge PRs manually
- PR must reference related Issue if it exists

### Context Repository Specifics

- Basic context is stored in `context/` repository
- Context updates occur in feature branches
- Changes to context are reflected via Pull Request to `dev`