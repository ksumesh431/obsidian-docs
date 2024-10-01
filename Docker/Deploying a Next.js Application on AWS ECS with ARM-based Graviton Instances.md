## Issue: `exec format error`

When trying to run a Docker container on AWS EC2 Graviton instances (ARM architecture), you might encounter the following error in the container logs:

`exec /usr/local/bin/docker-entrypoint.sh: exec format error`

### Root Cause:

This error occurs because the Docker image you're trying to run is built for the `x86_64` architecture but the EC2 Graviton instances are based on the ARM (`aarch64`) architecture. The `exec format error` means that the binary inside the Docker image is incompatible with the underlying ARM architecture.

### Solution: Multi-Architecture Docker Images

To resolve this issue, you need to create a Docker image that supports both `x86_64` (for standard EC2 instances) and `arm64` (for Graviton instances). This can be done using Docker's **Buildx** tool, which allows for building multi-architecture images.

## Step-by-Step Guide to Build Multi-Architecture Docker Images

### 1. **Install Docker Buildx**

`Docker Buildx` is usually included with Docker Desktop (for macOS and Windows). On Linux, you may need to enable it:

`docker buildx create --use`

### 2. **Modify Your Dockerfile**

Ensure your Dockerfile is compatible with both `x86_64` and `arm64` architectures. The `node:18-alpine` base image you’re using already supports multi-architecture, so no changes are needed for this step. 
### 3. **Build a Multi-Architecture Docker Image**

Use the following command to build and push a multi-architecture Docker image:

`docker buildx build --platform linux/amd64,linux/arm64 -t 316770681739.dkr.ecr.us-east-1.amazonaws.com/next:latest --push .`

#### Explanation:

- `--platform linux/amd64,linux/arm64`: Specifies the platforms for `x86_64` and `arm64` architectures.
- `-t 316770681739.dkr.ecr.us-east-1.amazonaws.com/next:latest`: Tags the image with your ECR repository URI.
- `--push`: Automatically pushes the multi-architecture image to Amazon ECR.

### 4. **Verify the Multi-Architecture Image**

To confirm that the image was built for both architectures, you can inspect the image:

`docker buildx imagetools inspect 316770681739.dkr.ecr.us-east-1.amazonaws.com/next:latest`

The output should show support for both `linux/amd64` and `linux/arm64`.

### 5. **Deploy the Image on AWS ECS**

Once the multi-architecture image is pushed to Amazon ECR, you can deploy the ECS service as usual. AWS ECS will automatically pull the correct image based on the architecture of the underlying EC2 instance.
