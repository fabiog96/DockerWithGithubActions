# Docker with GitHub Actions Example

This is a simple example project demonstrating how to use GitHub Actions to build and test a Docker container.

## Project Structure

- `app.sh` - A simple shell script that prints a message
- `Dockerfile` - Instructions for building the Docker image
- `.github/workflows/docker-build.yml` - GitHub Actions workflow configuration

## How it Works

1. On every push to the repository, GitHub Actions will:
   - Check out the code
   - Build a Docker image using the Dockerfile
   - Run the container to test it works

## Local Testing

To test this locally:

```bash
# Build the image
docker build -t my-app:latest .

# Run the container
docker run my-app:latest
