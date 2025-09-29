# 📋 Prerequisites and Setup Guide

This guide covers all the prerequisites and setup instructions needed to run the Go Microservices with API Gateway and Service Discovery project.

## 🔧 Required Software

### 1. Go Programming Language (Version 1.18+)

#### Windows:
1. Download Go from the official website: https://golang.org/dl/
2. Run the installer (.msi file) and follow the installation wizard
3. Add Go to your PATH (the installer usually does this automatically)
4. Open Command Prompt or PowerShell and verify:
   ```powershell
   go version
   ```

#### macOS:
```bash
# Using Homebrew (recommended)
brew install go

# Or download from https://golang.org/dl/ and run the .pkg installer
```

#### Linux (Ubuntu/Debian):
```bash
# Remove any existing Go installation
sudo rm -rf /usr/local/go

# Download and install Go (replace with latest version)
wget https://golang.org/dl/go1.21.0.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.0.linux-amd64.tar.gz

# Add to PATH in ~/.bashrc or ~/.zshrc
echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
source ~/.bashrc
```

#### Verify Go Installation:
```bash
go version
# Should output: go version go1.x.x [os/arch]

# Check Go environment
go env GOPATH
go env GOROOT
```

### 2. Docker and Docker Compose

#### Windows:
1. Download Docker Desktop from: https://www.docker.com/products/docker-desktop
2. Run the installer and follow the setup wizard
3. Start Docker Desktop after installation
4. Enable WSL 2 integration if prompted

#### macOS:
```bash
# Using Homebrew (recommended)
brew install --cask docker

# Or download Docker Desktop from https://www.docker.com/products/docker-desktop
```

#### Linux (Ubuntu/Debian):
```bash
# Update package index
sudo apt-get update

# Install required packages
sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release

# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

# Add Docker repository
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker Engine
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

# Add your user to docker group
sudo usermod -aG docker $USER
newgrp docker
```

#### Verify Docker Installation:
```bash
docker --version
# Should output: Docker version x.x.x

docker run hello-world
# Should download and run a test container successfully
```

### 3. Development Tools (Optional but Recommended)

#### cURL (for testing APIs):
```bash
# Windows (using chocolatey)
choco install curl

# macOS (usually pre-installed, or via Homebrew)
brew install curl

# Linux (usually pre-installed, or)
sudo apt-get install curl
```

#### Postman (Alternative to cURL):
- Download from: https://www.postman.com/downloads/
- Useful for testing and debugging APIs with a graphical interface

#### Go Language Extensions (for your IDE):
- **VS Code**: Install the "Go" extension by Google
- **JetBrains GoLand**: Full-featured Go IDE
- **Vim/Neovim**: Install vim-go plugin

## 📦 Required Go Libraries

The following Go libraries will be automatically downloaded when you run `go mod tidy` in each service directory:

### Chi Router (HTTP Router)
- **Package**: `github.com/go-chi/chi/v5`
- **Purpose**: Lightweight HTTP router for building REST APIs
- **Features**: Middleware support, URL parameters, route groups

### Consul API Client
- **Package**: `github.com/hashicorp/consul/api`
- **Purpose**: Go client library for HashiCorp Consul
- **Features**: Service registration, health checks, service discovery

## 🗂️ Directory Structure Setup

Create your workspace directory:

```bash
# Create main project directory
mkdir go-microservices
cd go-microservices

# Create directory structure
mkdir api-gateway
mkdir services
mkdir services/users-service
mkdir services/products-service

# Verify directory structure
ls -la
```

Your final project structure should look like this:
```
go-microservices/
├── README.md
├── PREREQUISITES.md
├── api-gateway/
│   ├── go.mod
│   ├── go.sum
│   └── main.go
└── services/
    ├── products-service/
    │   ├── go.mod
    │   ├── go.sum
    │   └── main.go
    └── users-service/
        ├── go.mod
        ├── go.sum
        └── main.go
```

## ✅ Pre-flight Checks

Before proceeding with the project setup, verify all tools are properly installed:

```bash
# Check Go
go version

# Check Docker
docker --version
docker ps

# Check if Docker daemon is running
docker info

# Test network connectivity (for downloading dependencies)
curl -I https://proxy.golang.org
```

## 🔧 Port Requirements

Ensure the following ports are available on your system:

- **Port 8080**: API Gateway
- **Port 8081**: Users Service
- **Port 8082**: Products Service
- **Port 8500**: Consul Web UI and API

### Check Port Availability:

#### Windows (PowerShell):
```powershell
netstat -an | findstr :8080
netstat -an | findstr :8081
netstat -an | findstr :8082
netstat -an | findstr :8500
```

#### macOS/Linux:
```bash
lsof -i :8080
lsof -i :8081
lsof -i :8082
lsof -i :8500
```

## 🐛 Troubleshooting Common Setup Issues

### Go Command Not Found
- **Issue**: `go: command not found`
- **Solution**: Ensure Go is in your PATH environment variable
- **Windows**: Add `C:\Go\bin` to your PATH
- **macOS/Linux**: Add `/usr/local/go/bin` to your PATH

### Docker Daemon Not Running
- **Issue**: `Cannot connect to the Docker daemon`
- **Solutions**:
  - **Windows/macOS**: Start Docker Desktop
  - **Linux**: `sudo systemctl start docker`

### Permission Denied for Docker
- **Issue**: `permission denied while trying to connect to the Docker daemon`
- **Solution**: Add your user to the docker group:
  ```bash
  sudo usermod -aG docker $USER
  newgrp docker
  ```

### Network Issues
- **Issue**: Cannot download Go modules or Docker images
- **Solutions**:
  1. Check firewall settings
  2. Configure proxy settings if behind corporate firewall
  3. Use Go module proxy: `go env -w GOPROXY=https://proxy.golang.org,direct`

### Port Conflicts
- **Issue**: `address already in use`
- **Solution**: 
  ```bash
  # Find and kill process using the port
  # Windows
  netstat -ano | findstr :8080
  taskkill /PID <PID> /F
  
  # macOS/Linux
  lsof -ti:8080 | xargs kill -9
  ```

## 🌐 Environment Variables (Optional)

Set these environment variables for easier development:

```bash
# Go configuration
export GOPATH=$HOME/go
export PATH=$PATH:$GOPATH/bin

# Consul configuration
export CONSUL_HTTP_ADDR="http://localhost:8500"

# Service configuration
export API_GATEWAY_PORT="8080"
export USERS_SERVICE_PORT="8081"
export PRODUCTS_SERVICE_PORT="8082"
```
## 🚀 Production Deployment

### Docker Deployment
Create Dockerfiles for each service:

```dockerfile
# Example Dockerfile for users-service
FROM golang:1.20-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN go build -o users-service .

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/users-service .
CMD ["./users-service"]
```

### Docker Compose Setup
```yaml
version: '3.8'
services:
  consul:
    image: hashicorp/consul
    command: agent -dev -ui -client=0.0.0.0
    ports:
      - "8500:8500"
  
  users-service:
    build: ./services/users-service
    depends_on:
      - consul
    environment:
      - CONSUL_HTTP_ADDR=http://consul:8500
  
  products-service:
    build: ./services/products-service
    depends_on:
      - consul
    environment:
      - CONSUL_HTTP_ADDR=http://consul:8500
      
  api-gateway:
    build: ./api-gateway
    ports:
      - "8080:8080"
    depends_on:
      - consul
    environment:
      - CONSUL_HTTP_ADDR=http://consul:8500
```

### Windows (PowerShell):
```powershell
$env:CONSUL_HTTP_ADDR = "http://localhost:8500"
$env:API_GATEWAY_PORT = "8080"
$env:USERS_SERVICE_PORT = "8081"
$env:PRODUCTS_SERVICE_PORT = "8082"
```

## 🚀 Next Steps

Once all prerequisites are installed and verified:

1. Follow the **Quick Start** guide in the main README.md
2. Start with setting up Consul service registry
3. Run each microservice individually
4. Start the API Gateway
5. Test the complete system


## 📚 Additional Resources

- [Go Installation Guide](https://golang.org/doc/install)
- [Docker Installation Guide](https://docs.docker.com/get-docker/)
- [Consul Installation Guide](https://developer.hashicorp.com/consul/install)
- [VS Code Go Extension](https://marketplace.visualstudio.com/items?itemName=golang.Go)

---

**Ready to build microservices? Let's go! 🚀**