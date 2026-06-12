# Contributing to CivicMesh

## GitHub Workflow
1. Check open issues: https://github.com/orgs/TrafficMesh/issues
2. Claim issue: "Assign to me"
3. Create feature branch: `git checkout -b feature/description`
4. Make commits with clear messages
5. Push and open PR: `git push origin feature/description`
6. Wait for code review and CI/CD

## Code Standards

### Python (Backend/Firmware)
- Formatter: Black
- Linter: flake8
- Type checker: mypy
- Tests: pytest

### React/TypeScript (Frontend)
- Formatter: Prettier
- Linter: ESLint
- Tests: Jest

### Hardware
- Clear assembly procedures
- Calibration reports
- QA checklists

## Testing
All PRs must pass:
- Unit tests (pytest / jest)
- Linting (flake8 / eslint)
- Type checking (mypy / tsc)
- Security scanning (Trivy)

Run locally before pushing:
```bash
pytest tests/unit/
black --check backend/
flake8 backend/
mypy backend/
```
