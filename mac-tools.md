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
