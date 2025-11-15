# Mac Tools Setup and Test
This is written with the assumption you are starting on a fresh factory Mac OS. Most of these tools you may already have on your mac. In that case, you can skip to running the verification script

## Essential Tools

### 1. Homebrew (Package Manager)
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
brew --version
```

### 2. Python 3.11+
```bash
# Install Python 3.11
brew install python@3.11

# After installation, you need to update your PATH
echo 'export PATH="/opt/homebrew/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# Check if python3.11 is available
which python3.11

# Should show: /opt/homebrew/bin/python3.11

python3.11 --version # verify installation
# Should show: Python 3.11.x
```

### 3. Docker Desktop
```bash
brew install --cask docker
```
Then open Docker Desktop from Applications to complete setup.

**Alternative**: Download directly from [docker.com](https://www.docker.com/products/docker-desktop)

### 4. Git
```bash
brew install git
git --version

# Configure git
git config --global user.name "FirstName LastName"
git config --global user.email "email@emaildomain.com"
```

### 5. Code Editor
```bash
# Visual Studio Code (recommended)
brew install --cask visual-studio-code

# OR PyCharm Community Edition
brew install --cask pycharm-ce
```

## Optional but Recommended

### 6. Kubernetes (for local testing)
```bash
# Minikube - lightweight Kubernetes
brew install minikube

# OR kind (Kubernetes in Docker)
brew install kind
```

### 7. kubectl (Kubernetes CLI)
```bash
brew install kubectl
```

### 8. OpenTofu (open-source Terraform alternative)
```bash
# Install OpenTofu 
brew install opentofu

# Verify installation
tofu --version
```
### 9. AWS CLI (if deploying to AWS)
```bash
brew install awscli
aws configure  # enter your AWS credentials
```

### 10. Useful Development Tools
```bash
# Postman (API testing)
brew install --cask postman

# HTTPie (command-line API testing)
brew install httpie

# jq (JSON processor)
brew install jq
```

## Python Development Setup

### 11. Virtual Environment Tools
```bash
# pip should come with Python, but upgrade it
brew install pipx
#Confirm pip version
pip3 --version 

# Install virtualenv
brew install virtualenv
```

### 12. Create Project Virtual Environment
```bash
# Navigate to your project directory
mkdir {custom}-service
cd {custom}-service

# Create virtual environment
python3 -m venv venv

# Activate it
source venv/bin/activate

# Your prompt should now show (venv)
```

## Verification Script

Create this file to verify everything is installed:

```bash
#!/bin/bash
# save as check_setup.sh and run: bash check_setup.sh

echo "Checking prerequisites..."
echo ""

command -v python3 >/dev/null 2>&1 && echo "✅ Python: $(python3 --version)" || echo "❌ Python not found"
command -v docker >/dev/null 2>&1 && echo "✅ Docker: $(docker --version)" || echo "❌ Docker not found"
command -v git >/dev/null 2>&1 && echo "✅ Git: $(git --version)" || echo "❌ Git not found"
command -v kubectl >/dev/null 2>&1 && echo "✅ kubectl: $(kubectl version --client --short 2>/dev/null)" || echo "⚠️  kubectl not found (optional)"
command -v tofu >/dev/null 2>&1 && echo "✅ OpenTofu: $(tofu --version | head -n 1)" || echo "⚠️  OpenTofu not found (optional)"
command -v aws >/dev/null 2>&1 && echo "✅ AWS CLI: $(aws --version)" || echo "⚠️  AWS CLI not found (optional)"

echo ""
echo "Docker daemon check..."
docker ps >/dev/null 2>&1 && echo "✅ Docker daemon is running" || echo "❌ Docker daemon not running - start Docker Desktop"
```

## Quick Start Commands After Setup

```bash
# 1. Create project directory
mkdir -p ~/projects/{custom}-service
cd ~/projects/{custom}-service

# 2. Create virtual environment
python3 -m venv venv
source venv/bin/activate

# 3. Install initial dependencies
pip install fastapi uvicorn sqlalchemy psycopg2-binary requests pytest

# 4. Initialize git repository
git init
echo "venv/" > .gitignore
echo "__pycache__/" >> .gitignore
echo "*.pyc" >> .gitignore
echo ".env" >> .gitignore
echo ".DS_Store" >> .gitignore

# 5. Create initial commit
git add .gitignore
git commit -m "Initial commit"
```

## VS Code Extensions (if using VS Code)

Open VS Code and install these extensions:
- Python (Microsoft)
- Docker (Microsoft)
- YAML (Red Hat)
- GitLens
- REST Client (for API testing)

## Time Estimate

- Installing everything: **30-45 minutes**
- Configuring and testing: **15-30 minutes**
- **Total: ~1 hour**

## Troubleshooting Tips

**Docker Desktop won't start:**
- Make sure you have at least 4GB RAM available
- Check System Preferences → Security & Privacy for permissions

**Python version issues:**
```bash
# Check which python versions are installed
brew list | grep python

# Use specific version
python3.11 -m venv venv
```

**Permission errors with pip:**
```bash
# Always use virtual environment, never sudo pip
source venv/bin/activate
pip install <package>
```

## Create Alias (Optional but Recommended)

```bash
# Add to your ~/.zshrc file
echo 'alias python3="python3.11"' >> ~/.zshrc
source ~/.zshrc

# Now verify
python3 --version
# Should now show Python 3.11.x
```

## Alternative: Use python3.11 Directly

If you don't want to change the default, just use `python3.11` explicitly:

```bash
# Create virtual environment with Python 3.11
python3.11 -m venv venv

# Activate it
source venv/bin/activate

# Verify the virtual environment is using 3.11
python --version
# Should show Python 3.11.x (inside the venv)
```

## If Homebrew Install Location Issues

If you're on Apple Silicon (M1/M2/M3), Homebrew installs to `/opt/homebrew`. If you're on Intel Mac, it's `/usr/local`.

**Check your Homebrew location:**
```bash
brew --prefix
```

**Then update PATH accordingly:**
```bash
# For Apple Silicon
echo 'export PATH="/opt/homebrew/bin:$PATH"' >> ~/.zshrc

# For Intel Mac
echo 'export PATH="/usr/local/bin:$PATH"' >> ~/.zshrc

source ~/.zshrc
```

## Complete Setup Commands

```bash
# 1. Install Python 3.11
brew install python@3.11

# 2. Update PATH
echo 'export PATH="/opt/homebrew/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc

# 3. Verify
python3.11 --version

# 4. Create project and venv
cd ~/projects/animal-picture-service
python3.11 -m venv venv
source venv/bin/activate

# 5. Verify Python version inside venv
python --version
# Should show Python 3.11.x
```

## Quick Test

Once activated in your venv:
```bash
(venv) username@Mac d8v % python --version
Python 3.11.x
```
## To exit a Python virtual environment (venv)
Open your terminal or command prompt.
Ensure you are in the shell where the virtual environment is currently activated. (e.g., (venv) user@host:~/my_project$).
```bash
deactivate
```
 Enter.
