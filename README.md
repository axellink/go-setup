# Go Setup Verification

This repository helps you verify that your Go and Docker environment is correctly configured for the Go training.

## What This Repository Contains

- `main.go` - A simple "Hello World" program to verify Go is installed
- `setup_test.go` - A test that uses testcontainers to verify Docker is working
- `go.mod` / `go.sum` - Go module definition and dependencies

## Prerequisites

Before starting the training, ensure you have:

1. **Go 1.25 or later** installed
2. **Docker** installed and the daemon running

## Go Installation (Linux)

If you don't have Go installed, follow these steps:

### 1. Download Go

Visit [go.dev/dl](https://go.dev/dl/) and download the latest Go version for Linux, or use:

```bash
curl -LO https://go.dev/dl/go1.25.6.linux-amd64.tar.gz
```

### 2. Extract to Installation Directory

**System-wide installation** (requires root):

```bash
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf go1.25.6.linux-amd64.tar.gz
```

**Userland installation** (no root required):

```bash
mkdir -p ~/go-sdk
tar -C ~/go-sdk -xzf go1.25.6.linux-amd64.tar.gz
```

### 3. Add to PATH

Add Go to your PATH by editing `~/.profile` or `~/.bashrc`:

**For system-wide installation**:

```bash
export PATH=$PATH:/usr/local/go/bin
```

**For userland installation**:

```bash
export PATH=$PATH:~/go-sdk/go/bin
```

Then reload your shell:

```bash
source ~/.profile  # or source ~/.bashrc
```

### 4. Verify Installation

```bash
go version
```

You should see something like: `go version go1.25.6 linux/amd64`

## Docker Installation

Docker is required for the database exercises in the training.

### Installation Options

- **Ubuntu/Debian**: Follow the [official Docker installation guide](https://docs.docker.com/engine/install/ubuntu/)
- **Fedora**: Follow the [official Docker installation guide](https://docs.docker.com/engine/install/fedora/)
- **Other distributions**: See [Docker Engine installation](https://docs.docker.com/engine/install/)

## VS Code SSH-Remote

If you're working on a remote development machine via SSH, VS Code's Remote-SSH extension provides a seamless development experience.

### Setup

1. **Install the extension**: Search for "Remote - SSH" (ID: `ms-vscode-remote.remote-ssh`) in VS Code extensions
2. **Connect to your remote machine**:
   - Press `Ctrl+Shift+P` → "Remote-SSH: Connect to Host..."
   - Enter your SSH connection string (e.g., `user@hostname`)
3. **Install Go extension on remote**: Once connected, install the Go extension (`golang.go`) on the remote machine

### Tips

- Your SSH config (`~/.ssh/config`) can simplify connections
- VS Code will automatically forward ports, making it easy to test web applications
- Terminal in VS Code runs on the remote machine

For more details, see the [official documentation](https://code.visualstudio.com/docs/remote/ssh).

## Verification Steps

### Step 1: Verify Go Installation

Run the Hello World program:

```bash
go run main.go
```

**Expected output**:

```
Hello World!
```

This confirms that:
- Go is installed and in your PATH
- Go can compile and run programs
- Your Go version meets the minimum requirement (1.25)

### Step 2: Verify Docker and Testcontainers

Run the test suite:

```bash
go test ./...
```

**Expected output** (first run may be slower due to image download):

```
=== RUN   TestDockerPostgreSQLSetup
    setup_test.go:XX: PostgreSQL container started successfully and query executed!
--- PASS: TestDockerPostgreSQLSetup (XXs)
PASS
ok      github.com/nathancastelein/go-setup     XXs
```

This confirms that:
- Docker daemon is running
- Testcontainers can create containers
- PostgreSQL container starts and accepts connections
- Your environment is ready for database exercises

## Troubleshooting

### "go: command not found"

Go is not installed or not in your PATH.

**Solution**:
1. Verify Go is installed: `ls /usr/local/go/bin/go` or `ls ~/go-sdk/go/bin/go`
2. Add Go to your PATH (see Go Installation section)
3. Reload your shell: `source ~/.profile` or start a new terminal

### "Cannot connect to the Docker daemon"

Docker is not running.

Ensure Docker is running on your machine.

### Go Version Mismatch Error

Your Go version is older than required.

**Solution**:
1. Check your version: `go version`
2. If older than 1.25, download and install the latest version from [go.dev/dl](https://go.dev/dl/)
3. Remove the old Go installation before installing the new one

### Image Pull Failures

Testcontainers cannot download the PostgreSQL image.

**Possible causes**:
- **Network issues**: Check your internet connection
- **Disk space**: Ensure you have at least 500MB free for the PostgreSQL image
- **Docker Hub rate limits**: If you see rate limit errors, try again later or log in to Docker Hub

**Solution**:

```bash
# Check available disk space
df -h

# Manually pull the image to see detailed errors
docker pull postgres:16-alpine
```

### Test Times Out

The test takes too long and times out.

**Possible causes**:
- Slow network connection (first-time image download)
- Insufficient system resources

**Solution**:
- First run may take 1-2 minutes to download images
- Ensure you have at least 2GB RAM available
- Run with verbose output to see progress: `go test -v ./...`
