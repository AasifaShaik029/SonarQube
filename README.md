# SonarQube
SonarQube Integration and Report Generation for Linting, Coverage, and Unit Tests
# Objective:
To integrate SonarQube with the CI/CD pipeline for monitoring code quality, including linting, coverage, unit test reports, and duplications.
### Tools and Components
- **SonarQube**: Code quality and static analysis.
- **ESLint**: Linting for JavaScript/TypeScript code.
- **Jest**: Unit tests and coverage reports.
- **CI/CD Pipeline**: E.g., GitLab CI

## Workflow Diagram

```plaintext
[ Code Repository ] 
      |
      v
[ CI/CD Pipeline ] 
      |
      v
[ Linting (ESLint) ] -- Generates ESLint reports --> Stored in 'reports/eslint'
      |
      v
[ Unit Tests + Coverage (Jest) ] -- Generates coverage + test reports --> Stored in 'reports/unit_tests' & 'coverage'
      |
      v
[ SonarQube Scanner ] -- Reads reports + analyzes code --> SonarQube Dashboard
