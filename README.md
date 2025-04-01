# Docker with GitHub Actions Example

This is a simple example project demonstrating how to use GitHub Actions to build and test a Docker container.

## Updates

**April 1, 2025**: Updated project documentation and configurations.

## Project Structure

- `entrypoint.sh` - A simple shell script that prints a message
- `Dockerfile` - Instructions for building the Docker image
- `.github/workflows/docker-build.yml` - GitHub Actions workflow configuration

## GitHub Actions Workflows

There are two approaches you can use with GitHub Actions and Docker:

### 1. Building and Running Custom Images

This approach builds your custom Docker image and runs it:

```yaml
name: Build and Test Docker
on:
  push:
    branches:
      - main
  pull_request:

env:
  IMAGE_NAME: test:latest

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4
      
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3
      
      - name: Cache Docker layers
        uses: actions/cache@v3
        with:
          path: /tmp/.buildx-cache
          key: ${{ runner.os }}-buildx-${{ github.sha }}
          restore-keys: |
            ${{ runner.os }}-buildx-
      
      - name: Build Docker image
        uses: docker/build-push-action@v5
        with:
          context: .
          load: true
          tags: ${{ env.IMAGE_NAME }}
          cache-from: type=local,src=/tmp/.buildx-cache
          cache-to: type=local,dest=/tmp/.buildx-cache-new,mode=max
      
      - name: Move cache
        run: |
          rm -rf /tmp/.buildx-cache
          mv /tmp/.buildx-cache-new /tmp/.buildx-cache
      
      - name: Test the image
        run: docker run ${{ env.IMAGE_NAME }}
```

This configuration includes layer caching which:
- Speeds up builds by reusing previously built layers
- Reduces build time and resource usage
- Persists cache between workflow runs

Use this when you need to:
- Build custom applications
- Test your Dockerfile changes
- Run specific tests in your container

### 2. Using Existing Docker Images

For simpler cases, you can directly use existing Docker images:

```yaml
steps:
  - name: Run Docker container
    run: docker run hello-world:latest
```

Use this when you:
- Need to run standard containers
- Don't require custom builds
- Want to use official images from Docker Hub

## How it Works

On every push to the repository, GitHub Actions will:
1. Check out the code
2. Build a Docker image using the Dockerfile
3. Run the container to test it works

## Local Testing

To test this locally:

```bash
# Build the image
docker build -t my-app:latest .

# Run the container
docker run my-app:latest
```

### Expected Output
```
Hello from my-app!
Container is running successfully.
```

## Troubleshooting

### 1. Permission Denied Errors
Try running the command with `sudo`:
```bash
sudo docker run my-app:latest
```

### 2. Cache Not Working
If cache layers aren’t being restored, ensure the workflow has sufficient permissions and storage space.

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.