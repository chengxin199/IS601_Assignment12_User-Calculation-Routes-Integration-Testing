# FastAPI Calculator with User Authentication

A production-ready RESTful API built with FastAPI that provides calculation services (addition, subtraction, multiplication, division) with user authentication and authorization using JWT tokens.

## 🚀 Features

- **User Management**: Register, login with JWT-based authentication
- **BREAD Operations**: Browse, Read, Edit, Add, Delete calculations
- **Secure Authentication**: Password hashing with bcrypt, JWT access/refresh tokens
- **Database**: PostgreSQL with SQLAlchemy ORM
- **Testing**: Comprehensive unit, integration, and E2E tests
- **CI/CD**: GitHub Actions with automated testing and Docker Hub deployment
- **Containerization**: Docker and Docker Compose support

## 🐳 Docker Hub

Pre-built Docker images are available on Docker Hub:
- **Repository**: [chengxin199/is601_module12](https://hub.docker.com/r/chengxin199/is601_module12)
- **Tags**: `latest`, `v1.0`

Pull the image:
```bash
docker pull chengxin199/is601_module12:latest
```

Run the container:
```bash
docker run -p 8000:8000 -e DATABASE_URL=postgresql://user:password@host:5432/db chengxin199/is601_module12:latest
```

---

## 📋 Table of Contents

- [Installation](#installation)
- [Running Tests Locally](#running-tests-locally)
- [API Documentation](#api-documentation)
- [Environment Variables](#environment-variables)
- [Docker Setup](#docker-setup)
- [CI/CD Pipeline](#cicd-pipeline)

---

## 🛠️ Installation

### Prerequisites

- Python 3.10+
- PostgreSQL 12+
- Docker (optional)
- Git

### Local Setup

1. **Clone the repository**
```bash
git clone git@github.com:chengxin199/IS601_Assignment12_User-Calculation-Routes-Integration-Testing.git
cd IS601_Assignment12_User-Calculation-Routes-Integration-Testing
```

2. **Create and activate virtual environment**
```bash
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. **Install dependencies**
```bash
pip install -r requirements.txt
```

4. **Set up environment variables**
```bash
cp .env.example .env  # Create .env file and update values
```

5. **Start PostgreSQL** (or use Docker Compose)
```bash
docker-compose up -d postgres
```

6. **Run the application**
```bash
uvicorn app.main:app --reload --host 0.0.0.0 --port 8000
```

Visit: http://localhost:8000/docs for interactive API documentation

---

## 🧪 Running Tests Locally

### Run All Tests
```bash
pytest
```

### Run Specific Test Suites

**Unit Tests:**
```bash
pytest tests/unit/ -v
```

**Integration Tests:**
```bash
pytest tests/integration/ -v
```

**E2E Tests:**
```bash
pytest tests/e2e/ -v
```

### Run Tests with Coverage
```bash
pytest --cov=app --cov-report=html --cov-report=term
```

View coverage report:
```bash
open htmlcov/index.html  # Mac
start htmlcov/index.html # Windows
```

### Run Specific Test File
```bash
pytest tests/integration/test_user_auth.py -v
```

### Run Tests with Database
Ensure PostgreSQL is running:
```bash
docker-compose up -d postgres
export DATABASE_URL=postgresql://user:password@localhost:5432/mytestdb
pytest
```

---

## 📚 API Documentation

### User Endpoints

#### Register User
```http
POST /auth/register
Content-Type: application/json

{
  "first_name": "John",
  "last_name": "Doe",
  "email": "john.doe@example.com",
  "username": "johndoe",
  "password": "SecurePass123!",
  "confirm_password": "SecurePass123!"
}
```

#### Login
```http
POST /auth/login
Content-Type: application/json

{
  "username": "johndoe",
  "password": "SecurePass123!"
}
```

Returns:
```json
{
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "refresh_token": "eyJ0eXAiOiJKV1QiLCJhbGc...",
  "token_type": "bearer",
  "expires_at": "2025-12-01T12:00:00Z",
  "user_id": "uuid",
  "username": "johndoe"
}
```

### Calculation Endpoints (Requires Authentication)

#### Create Calculation
```http
POST /calculations
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "type": "addition",
  "inputs": [10, 20, 30]
}
```

#### List Calculations
```http
GET /calculations
Authorization: Bearer <access_token>
```

#### Get Calculation by ID
```http
GET /calculations/{id}
Authorization: Bearer <access_token>
```

#### Update Calculation
```http
PUT /calculations/{id}
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "inputs": [5, 10, 15]
}
```

#### Delete Calculation
```http
DELETE /calculations/{id}
Authorization: Bearer <access_token>
```

**Supported Calculation Types:**
- `addition`: Sum of all inputs
- `subtraction`: Sequential subtraction
- `multiplication`: Product of all inputs
- `division`: Sequential division

---

## 🔧 Environment Variables

Create a `.env` file in the root directory:

```env
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/fastapi_db

# JWT Settings
JWT_SECRET_KEY=your-super-secret-key-change-in-production
JWT_REFRESH_SECRET_KEY=your-refresh-secret-key
ALGORITHM=HS256
ACCESS_TOKEN_EXPIRE_MINUTES=30
REFRESH_TOKEN_EXPIRE_DAYS=7

# Security
BCRYPT_ROUNDS=12

# Redis (Optional)
REDIS_URL=redis://localhost:6379/0
```

---

## 🐳 Docker Setup

### Using Docker Compose (Recommended)

**Start all services:**
```bash
docker-compose up -d
```

**View logs:**
```bash
docker-compose logs -f
```

**Stop services:**
```bash
docker-compose down
```

**Rebuild and start:**
```bash
docker-compose up --build -d
```

### Using Docker Only

**Build image:**
```bash
docker build -t is601-calculator .
```

**Run container:**
```bash
docker run -d \
  -p 8000:8000 \
  -e DATABASE_URL=postgresql://user:password@host:5432/db \
  --name calculator-api \
  is601-calculator
```

---

## 🔄 CI/CD Pipeline

The project uses GitHub Actions for continuous integration and deployment:

### Workflow Steps:
1. **Test**: Runs unit, integration, and E2E tests with PostgreSQL
2. **Security**: Scans Docker image for vulnerabilities using Trivy
3. **Deploy**: Builds and pushes Docker image to Docker Hub

### GitHub Actions Configuration

The pipeline automatically runs on:
- Push to `main` branch
- Pull requests to `main` branch

**Required Secrets:**
- `DOCKERHUB_USERNAME`: Your Docker Hub username
- `DOCKERHUB_TOKEN`: Your Docker Hub access token

**View workflow:** [GitHub Actions](.github/workflows/test.yml)

---

## 📁 Project Structure

```
.
├── app/
│   ├── auth/              # Authentication & JWT logic
│   ├── core/              # Configuration
│   ├── models/            # SQLAlchemy models
│   ├── schemas/           # Pydantic schemas
│   ├── operations/        # Business logic
│   ├── database.py        # Database connection
│   └── main.py            # FastAPI application
├── tests/
│   ├── unit/              # Unit tests
│   ├── integration/       # Integration tests
│   └── e2e/               # End-to-end tests
├── .github/
│   └── workflows/         # CI/CD workflows
├── docker-compose.yml     # Docker Compose configuration
├── Dockerfile             # Docker image definition
├── requirements.txt       # Python dependencies
├── pytest.ini             # Pytest configuration
└── README.md             # This file
```

---

## 🧪 Testing Architecture

- **Unit Tests**: Test individual functions and methods
- **Integration Tests**: Test database operations and business logic
- **E2E Tests**: Test complete API workflows with live server

All tests use pytest fixtures for database setup and teardown.

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 📞 Contact

- **GitHub**: [@chengxin199](https://github.com/chengxin199)
- **Docker Hub**: [chengxin199/is601_module12](https://hub.docker.com/r/chengxin199/is601_module12)

---

## 📚 Additional Resources

# 🧩 1. Install Homebrew (Mac Only)

> Skip this step if you're on Windows.

Homebrew is a package manager for macOS.  
You’ll use it to easily install Git, Python, Docker, etc.

**Install Homebrew:**

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

**Verify Homebrew:**

```bash
brew --version
```

If you see a version number, you're good to go.

---

# 🧩 2. Install and Configure Git

## Install Git

- **MacOS (using Homebrew)**

```bash
brew install git
```

- **Windows**

Download and install [Git for Windows](https://git-scm.com/download/win).  
Accept the default options during installation.

**Verify Git:**

```bash
git --version
```

---

## Configure Git Globals

Set your name and email so Git tracks your commits properly:

```bash
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```

Confirm the settings:

```bash
git config --list
```

---

## Generate SSH Keys and Connect to GitHub

> Only do this once per machine.

1. Generate a new SSH key:

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

(Press Enter at all prompts.)

2. Start the SSH agent:

```bash
eval "$(ssh-agent -s)"
```

3. Add the SSH private key to the agent:

```bash
ssh-add ~/.ssh/id_ed25519
```

4. Copy your SSH public key:

- **Mac/Linux:**

```bash
cat ~/.ssh/id_ed25519.pub | pbcopy
```

- **Windows (Git Bash):**

```bash
cat ~/.ssh/id_ed25519.pub | clip
```

5. Add the key to your GitHub account:
   - Go to [GitHub SSH Settings](https://github.com/settings/keys)
   - Click **New SSH Key**, paste the key, save.

6. Test the connection:

```bash
ssh -T git@github.com
```

You should see a success message.

---

# 🧩 3. Clone the Repository

Now you can safely clone the course project:

```bash
git clone <repository-url>
cd <repository-directory>
```

---

# 🛠️ 4. Install Python 3.10+

## Install Python

- **MacOS (Homebrew)**

```bash
brew install python
```

- **Windows**

Download and install [Python for Windows](https://www.python.org/downloads/).  
✅ Make sure you **check the box** `Add Python to PATH` during setup.

**Verify Python:**

```bash
python3 --version
```
or
```bash
python --version
```

---

## Create and Activate a Virtual Environment

(Optional but recommended)

```bash
python3 -m venv venv
source venv/bin/activate   # Mac/Linux
venv\Scripts\activate.bat  # Windows
```

### Install Required Packages

```bash
pip install -r requirements.txt
```

---

# 🐳 5. (Optional) Docker Setup

> Skip if Docker isn't used in this module.

## Install Docker

- [Install Docker Desktop for Mac](https://www.docker.com/products/docker-desktop/)
- [Install Docker Desktop for Windows](https://www.docker.com/products/docker-desktop/)

## Build Docker Image

```bash
docker build -t <image-name> .
```

## Run Docker Container

```bash
docker run -it --rm <image-name>
```

---

# 🚀 6. Running the Project

- **Without Docker**:

```bash
python main.py
```

(or update this if the main script is different.)

- **With Docker**:

```bash
docker run -it --rm <image-name>
```

---

# 📝 7. Submission Instructions

After finishing your work:

```bash
git add .
git commit -m "Complete Module X"
git push origin main
```

Then submit the GitHub repository link as instructed.

---

# 🔥 Useful Commands Cheat Sheet

| Action                         | Command                                          |
| ------------------------------- | ------------------------------------------------ |
| Install Homebrew (Mac)          | `/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"` |
| Install Git                     | `brew install git` or Git for Windows installer |
| Configure Git Global Username  | `git config --global user.name "Your Name"`      |
| Configure Git Global Email     | `git config --global user.email "you@example.com"` |
| Clone Repository                | `git clone <repo-url>`                          |
| Create Virtual Environment     | `python3 -m venv venv`                           |
| Activate Virtual Environment   | `source venv/bin/activate` / `venv\Scripts\activate.bat` |
| Install Python Packages        | `pip install -r requirements.txt`               |
| Build Docker Image              | `docker build -t <image-name> .`                |
| Run Docker Container            | `docker run -it --rm <image-name>`               |
| Push Code to GitHub             | `git add . && git commit -m "message" && git push` |

---

# 📋 Notes

- Install **Homebrew** first on Mac.
- Install and configure **Git** and **SSH** before cloning.
- Use **Python 3.10+** and **virtual environments** for Python projects.
- **Docker** is optional depending on the project.

---

# 📎 Quick Links

- [Homebrew](https://brew.sh/)
- [Git Downloads](https://git-scm.com/downloads)
- [Python Downloads](https://www.python.org/downloads/)
- [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- [GitHub SSH Setup Guide](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
