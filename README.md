# GitHub Runs Archiver - SonarQube Setup Guide

This guide explains how to set up and use SonarQube for code analysis on your local project using Docker Compose.

## Prerequisites

- Docker and Docker Compose installed on your machine
- Basic understanding of Docker and SonarQube
- A project that you want to analyze

## Setup Steps

### 1. Configure Environment Variables

Create a .env file in the same directory as your docker-compose.yml with the following content:

```properties
# SonarQube Connection Settings
SONAR_HOST_URL=http://sonarqube:9000
SONAR_TOKEN=your_sonarqube_token

# Database Configuration (Make sure these match!)
SONAR_JDBC_URL=jdbc:postgresql://db:5432/sonar
SONAR_JDBC_USERNAME=sonar
SONAR_JDBC_PASSWORD=sonar

# PostgreSQL Configuration
POSTGRES_USER=sonar
POSTGRES_PASSWORD=sonar
POSTGRES_DB=sonar
```

**Note:** Replace `your_sonarqube_token` with your actual token (you'll generate this later).

### 2. Start SonarQube

```bash
docker-compose up -d sonarqube db
```

Wait about 1-2 minutes for SonarQube to fully initialize.

### 3. Access SonarQube UI

1. Open your browser and navigate to [http://localhost:9000](http://localhost:9000)
2. Log in with the default credentials:
   - Username: `admin`
   - Password: `admin`
3. You'll be prompted to change the password on first login

### 4. Generate a SonarQube Token

1. In the SonarQube UI, go to your user profile (click your avatar in the top-right)
2. Select **My Account** > **Security**
3. Enter a name for your token (e.g., "local-scanner") and click **Generate**
4. Copy the generated token and update the `SONAR_TOKEN` value in your .env file

### 5. Run the Code Analysis

```bash
docker-compose up sonar-scanner
```

This will:
1. Scan your code in the project_name directory
2. Upload the results to your SonarQube instance
3. Display the analysis progress in the terminal

### 6. View Analysis Results

1. Go back to the SonarQube UI at [http://localhost:9000](http://localhost:9000)
2. Navigate to **Projects**
3. Click on your project (named "project_name" by default)
4. Explore the results, including:
   - Code quality issues
   - Code smells
   - Security vulnerabilities
   - Test coverage (if configured)

## Customizing the Analysis

To customize the analysis for your specific project, modify the `command` section in the `sonar-scanner` service in docker-compose.yml:

```yaml
command: >
  -Dsonar.projectKey=your-project-key
  -Dsonar.sources=your-source-directory
  -Dsonar.python.version=3.9.7  # Adjust to your Python version
  -Dsonar.sourceEncoding=UTF-8
  -Dsonar.login=${SONAR_TOKEN}
  # Add additional parameters as needed:
  # -Dsonar.tests=tests
  # -Dsonar.python.coverage.reportPaths=coverage.xml
  # -Dsonar.exclusions=**/tests/**,**/migrations/**
```

## Troubleshooting

- **Database Connection Issues**: Ensure the database credentials match between all services
- **Permission Errors**: Check that Docker has proper permissions to mount volumes
- **SonarQube Not Starting**: Increase memory allocated to Docker if needed

## Stopping Services

```bash
docker-compose down
```

To completely reset (including volumes):

```bash
docker-compose down -v
```

---

This setup provides a containerized SonarQube environment that can be used with any project, enabling consistent code quality analysis across your development workflow.
