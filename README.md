
# SonarQube Integration and Report Generation for Linting, Coverage, and Unit Tests

## Introduction

### Objective
To integrate SonarQube with the CI/CD pipeline for monitoring code quality, including linting, coverage, unit test reports, and duplications.

### Tools and Components
- **SonarQube**: Code quality and static analysis.
- **ESLint**: Linting for JavaScript/TypeScript code.
- **Jest**: Unit tests and coverage reports.
- **CI/CD Pipeline**: E.g., GitLab CI, Jenkins.

---

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
```

---

## Step-by-Step Process

### 1. Set Up SonarQube
1. Install and configure SonarQube (Community or Enterprise) on a server.
2. Create a new project in SonarQube and generate an authentication token.

---

### 2. Update CI/CD Pipeline

1. Add the **SonarScanner** or **SonarQube plugin** to your CI/CD pipeline.
2. Configure the `sonar-project.properties` file in your repository:

```properties
sonar.projectKey=my-project-key
sonar.host.url=http://<your-sonarqube-server>
sonar.login=<your-sonar-token>
sonar.sources=src
sonar.tests=tests
sonar.javascript.lcov.reportPaths=coverage/lcov.info
sonar.testExecutionReportPaths=reports/unit_tests/junit.xml
sonar.eslint.reportPaths=reports/eslint/eslint-report.json
```

---

### 3. Generate Reports in the Pipeline

1. Add scripts to your CI/CD pipeline for generating reports:

   - **ESLint Report Generation**:
     ```bash
     mkdir -p reports/eslint
     npm run lint -- --format json --output-file=reports/eslint/eslint-report.json
     npm run lint -- --format html --output-file=reports/eslint/eslint-report.html
     ```

   - **Coverage Report Generation (Jest)**:
     ```bash
     mkdir -p coverage
     npm run test:cov
     ```

   - **Unit Test Report Generation (JUnit)**:
     ```bash
     npx jest --reporters=default --reporters=jest-junit --outputFile="reports/unit_tests/junit.xml"
     ```

2. Ensure these reports are generated in the correct paths defined in `sonar-project.properties`.

---

### 4. Run SonarScanner

Add a stage in your CI/CD pipeline to trigger the **SonarScanner** after report generation:

```yaml
sonar-analysis:
  stage: code-quality
  image: sonarsource/sonar-scanner-cli
  script:
    - sonar-scanner
  only:
    - merge_requests
    - main
```

---

### 5. Review Results in SonarQube

- Navigate to the **SonarQube dashboard** for your project.
- Review the following:
  - **Issues**: Displays linting and other static analysis issues.
  - **Coverage**: Shows the percentage of code coverage from your Jest report.
  - **Tests**: Reflects the test execution details from the JUnit XML report.
  - **Duplications**: Highlights duplicated code blocks in your repository.

---

## Troubleshooting

### Issue 1: Unit Test Reports Not Showing
- **Cause**: Incorrect JUnit XML path in `sonar-project.properties`.
- **Solution**: Verify the path and ensure the XML file exists.

```properties
sonar.testExecutionReportPaths=reports/unit_tests/junit.xml
```

### Issue 2: Coverage Not Showing
- **Cause**: Missing or incorrect LCOV file path.
- **Solution**: Ensure Jest generates the LCOV file and the path is correctly defined.

```properties
sonar.javascript.lcov.reportPaths=coverage/lcov.info
```

### Issue 3: Duplications Missing
- **Cause**: Code duplications might not meet the detection threshold.
- **Solution**: Lower the `sonar.cpd.minimumTokens` setting in `sonar-project.properties`.

```properties
sonar.cpd.minimumTokens=50
```

---

## Summary of Key Configuration Files

### CI/CD Pipeline Script (Snippet)

```yaml
lint-check:
  stage: pre
  script:
    - mkdir -p reports/eslint coverage reports/unit_tests
    - npm run lint -- --format json --output-file=reports/eslint/eslint-report.json
    - npm run lint -- --format html --output-file=reports/eslint/eslint-report.html
    - npm run test:cov
    - npx jest --reporters=default --reporters=jest-junit --outputFile="reports/unit_tests/junit.xml"
  artifacts:
    paths:
      - reports/
      - coverage/
```

---

### sonar-project.properties

```properties
sonar.projectKey=my-project-key
sonar.host.url=http://<your-sonarqube-server>
sonar.login=<your-sonar-token>
sonar.sources=src
sonar.tests=tests
sonar.javascript.lcov.reportPaths=coverage/lcov.info
sonar.testExecutionReportPaths=reports/unit_tests/junit.xml
sonar.eslint.reportPaths=reports/eslint/eslint-report.json
```

---

## Conclusion

This integration ensures that **linting**, **unit testing**, **coverage**, and **duplications** are seamlessly analyzed in SonarQube. The CI/CD pipeline automates report generation and analysis, ensuring continuous monitoring and improvement of code quality.
