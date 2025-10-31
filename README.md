# SDMO Project 1 – Identifying unique developers in OSS

This project aims to identify duplicate developer identities across commits in open-source repositories,
based on the approach of Bird et al. (MSR 2006).

## Structure

- `src/` — all source code (collection, preprocessing, baseline, improved methods, evaluation)
- `data/` — input and output datasets
- `tests/` — unit tests for each module
- `reports/` — documentation and report drafts

## Setup

### Option 1: Local Development

1. Create a Python virtual environment (Python ≥ 3.10).
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. [See section Usage for further instructions](#usage).

### Option 2: Docker Setup

1. **Build and run the application:**
   ```bash
   # Build the Docker image
   docker build -t unique-developers-app .
   
   # Run the application
   docker run -v $(pwd)/output:/app/output unique-developers-app --repo https://github.com/numpy/numpy --threshold 0.85
   ```

2. **Using Docker Compose (includes SonarQube):**
   ```bash
   # Start all services (app, SonarQube, PostgreSQL)
   docker-compose up -d
   
   # Run analysis with specific repository
   docker-compose exec app python src/main.py --repo https://github.com/numpy/numpy --threshold 0.85
   
   # Stop all services
   docker-compose down
   ```

## Usage

### Basic Usage

```bash
# Analyze a repository
python src/main.py --repo https://github.com/numpy/numpy --threshold 0.85

# With Docker
docker run -v $(pwd)/output:/app/output unique-developers-app --repo https://github.com/numpy/numpy --threshold 0.85
```

### Advanced Options

```bash
# Limit commits and pairs for faster analysis
python src/main.py --repo https://github.com/torvalds/linux --threshold 0.8 --max-commits 5000 --max-pairs 2000

# Skip extraction and use existing data
python src/main.py --repo https://github.com/microsoft/vscode --skip-extraction --threshold 0.9

# Clean repository after analysis
python src/main.py --repo https://github.com/numpy/numpy --clean-repo
```

## Output

The analysis generates several output files in the `output/` directory:
- `{repo_name}_bird_duplicates.csv` - Duplicates found by Bird heuristic
- `{repo_name}_improved_duplicates.csv` - Duplicates found by improved heuristic
- `{repo_name}_summary.json` - Complete analysis results
- `{repo_name}_report.md` - Human-readable report

## SonarQube Code Analysis

This project includes SonarQube integration for comprehensive code quality analysis.

### Accessing SonarQube

1. **Start SonarQube services:**
   ```bash
   docker-compose up -d sonarqube postgres
   ```

2. **Access SonarQube Web Interface:**
   - Open your browser and go to: http://localhost:9000
   - Default credentials: admin/admin (you'll be prompted to change password on first login)

3. **Run Code Analysis:**
   ```bash
   # Run SonarQube scanner
   docker-compose up sonar-scanner
   
   # Or run manually
   docker-compose exec sonar-scanner sonar-scanner
   ```

### SonarQube Configuration

The project includes a `sonar-project.properties` file with the following configuration:
- Project key: `unique-developers-oss`
- Source code: `src/` directory
- Tests: `tests/` directory
- Python version: 3.10
- Exclusions: `__pycache__`, `venv`, `output`, `data`, `reports` directories

### Code Quality Reports

After running SonarQube analysis, you can:
1. View detailed code quality metrics in the SonarQube web interface
2. Download reports in various formats (PDF, CSV, etc.)
3. Set up quality gates for continuous integration
4. Track technical debt and code smells