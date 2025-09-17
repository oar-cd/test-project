# Volume Permission Test Suite

This directory contains a comprehensive test suite for the volume permission initialization feature. The `volume-test-compose.yaml` file includes 23 different services that test various scenarios for volume permission handling.

## Test Services Overview

### Basic Volume Tests
- **no-user-with-volumes**: Tests default behavior with no user specified
- **root-user-with-volumes**: Tests explicit root user with volumes
- **nonroot-user-with-volumes**: Tests explicit non-root user requiring permission fixes
- **image-defaults-nonroot**: Tests images that default to non-root users (PostgreSQL)
- **opensearch-nonroot**: Tests images that prohibit root users (OpenSearch)
- **no-volumes**: Tests services without any volumes (should skip initialization)

### Build Context Tests
- **build-no-user**: Tests build context without user specification
- **build-root-user**: Tests build context with root user
- **build-nonroot-user**: Tests build context with non-root user
- **both-build-image-\***: Tests services with both build context and image name

### Volume Type Tests
- **named-volumes-only**: Tests named volumes exclusively
- **bind-mounts-only**: Tests bind mounts exclusively
- **mixed-volumes**: Tests combination of bind mounts and named volumes

### Mount Type Tests
- **file-bind-mount-existing**: Tests file bind mounts where source files already exist
- **file-bind-mount-nonexisting**: Tests file bind mounts where source files don't exist
- **directory-bind-mount**: Tests directory bind mounts
- **multiple-volumes**: Tests services with multiple volume mounts

### User Format Tests
- **user-group-format**: Tests "UID:GID" user specification format
- **numeric-user**: Tests numeric user ID specification
- **complex-paths**: Tests deeply nested directory structures

## Expected Behaviors

### Should Skip Permission Initialization
- Services with no volumes
- Services running as root (user: "0" or no user with root-default image)

### Should Fix Permissions
- Services with non-root users and volumes
- Services using images that default to non-root users
- Services using images that prohibit root execution

## Usage

1. **Deploy the test project**:
   ```bash
   oar project add volume-test /path/to/this/directory volume-test-compose.yaml
   oar project deploy <project-id>
   ```

2. **Check the logs** for volume initialization messages:
   ```bash
   oar project logs <project-id>
   ```

3. **Verify directory creation** in the `test-data/` directory:
   ```bash
   ls -la test-data/
   ```

4. **Check file/directory permissions**:
   ```bash
   find test-data/ -exec ls -ld {} \;
   ```

## Directory Structure Created

The deployment will create the following directory structure:
```
test-data/
├── no-user/
├── root-user/
├── nonroot-user/
├── postgres-data/
├── opensearch-data/
├── build-no-user/
├── build-root/
├── build-nonroot/
├── both-no-user/
├── both-root/
├── both-nonroot/
├── both-defaults/
├── bind-only/
├── mixed-bind/
├── config/
│   ├── existing-nginx.conf      # Pre-existing file
│   └── new-nginx.conf          # Created during deployment
├── logs/
│   ├── existing-access.log     # Pre-existing file
│   └── new-error.log          # Created during deployment
├── html-dir/
├── logs-dir/
├── multi-1/
├── multi-2/
├── multi-3/
├── user-group/
├── numeric-user/
├── complex/
│   └── nested/
│       └── deep/
│           └── path/
└── another/
    └── complex/
        └── structure/
            └── file.conf
```

## Validation

After deployment, verify:

1. **No errors in deployment logs**
2. **All containers start successfully**
3. **Correct ownership** on bind mount directories (should match service user)
4. **Parent directories created** for file bind mounts
5. **Deep directory structures** created for complex paths

## Cleanup

To clean up after testing:
```bash
oar project stop <project-id>
rm -rf test-data/
docker volume rm $(docker volume ls -q | grep test-)
```
