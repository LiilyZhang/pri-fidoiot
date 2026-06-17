# Build and Release Process Documentation

## Table of Contents
- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [Build Process](#build-process)
- [Release Process](#release-process)
- [Continuous Integration](#continuous-integration)
- [Docker/Podman Build](#dockerpodman-build)
- [Troubleshooting](#troubleshooting)

---

## Overview

This document describes the build and release process for the **FIDO Device Onboard (FDO) Protocol Reference Implementation (PRI)** repository (`pri-fidoiot`). The project is a Java-based implementation of the FDO specification, consisting of multiple components including protocol core, device samples, and service components (Manufacturer, Rendezvous, Owner, Reseller).

**Current Version:** 1.1.11

**Repository:** https://github.com/fido-device-onboard/pri-fidoiot

---

## Prerequisites

### System Requirements

#### Operating Systems
- **Ubuntu:** 20.04, 22.04
- **RHEL:** 8.4, 8.6, 8.8
- **Debian:** 11.4

#### Required Software
- **Java Development Kit (JDK):** 17
- **Apache Maven:** 3.5.4 or higher (3.6.3 recommended)
- **Git:** Latest stable version
- **Docker:** 20.10.10+ (minimum), 20.10.21 (maximum supported)
- **Docker Compose:** 1.29.2+
- **Podman:** 3.4.2+ (for RHEL)
- **Podman Compose:** 1.0.3+ (for RHEL)
- **Haveged:** For entropy generation

#### Optional Tools
- Java IDE (IntelliJ IDEA, Eclipse, VS Code) for development

### Environment Configuration

#### Java Environment
Set up Java proxy configuration if working behind a proxy:

```bash
export _JAVA_OPTIONS="-Dhttp.proxyHost=<proxy_host> -Dhttp.proxyPort=<proxy_port> -Dhttps.proxyHost=<https_proxy_host> -Dhttps.proxyPort=<https_proxy_port>"
```

#### Proxy Configuration
If working behind a proxy, export the following variables:

```bash
export http_proxy=http://<proxy_host>:<proxy_port>
export https_proxy=https://<https_proxy_host>:<https_proxy_port>
export no_proxy=localhost,127.0.0.1
```

#### RHEL Podman Setup
For RHEL systems using Podman:

```bash
cd <fdo-pri-src>/build
bash enable_podman_support.sh
echo $'\nexport PODMAN_USERNS=keep-id' >> ~/.bashrc
source ~/.bashrc
```

---

## Build Process

### Project Structure

The project follows a Maven multi-module structure:

```
pri-fidoiot/
├── pom.xml                    # Root POM (version 1.1.11)
├── protocol/                  # Protocol core module
│   └── pom.xml
├── component-samples/         # Component samples
│   ├── pom.xml
│   ├── device/               # Device implementation
│   ├── aio/                  # All-in-One demo
│   └── demo/                 # Demo configurations
└── build/                    # Build scripts and Docker files
```

### Local Build

#### Standard Maven Build

From the root directory of the repository:

```bash
cd <fdo-pri-src>
mvn clean install
```

This command will:
1. Clean previous build artifacts
2. Compile all modules
3. Run unit tests
4. Package JAR/WAR files
5. Install artifacts to local Maven repository

#### Build Options

**Skip Tests:**
```bash
mvn clean install -Dmaven.test.skip=true
```

**Skip PGP Verification:**
```bash
mvn clean install -Dpgpverify.skip=true
```

**Combined (used in Docker builds):**
```bash
mvn clean install -Dpgpverify.skip=true -Dmaven.test.skip=true
```

#### Build Output

Build artifacts are generated in:
- `component-samples/demo/` - Demo service executables and configurations
- `protocol/target/` - Protocol core JAR
- `component-samples/device/target/` - Device sample JAR
- `component-samples/aio/target/` - All-in-One service JAR

### Building Specific Components

#### Protocol Core Only
```bash
cd protocol
mvn clean install
```

#### Device Component Only
```bash
cd component-samples/device
mvn clean install
```

#### All-in-One Demo
```bash
cd component-samples/aio
mvn clean install
```

---

## Docker/Podman Build

### Using Docker

The repository includes Docker-based build scripts for consistent build environments.

#### Build Local Copy

```bash
cd <fdo-pri-src>/build
docker-compose up --build
```

With sudo (if required):
```bash
sudo docker-compose up --build
```

#### Build from Remote Repository

To build the latest upstream version:

```bash
cd <fdo-pri-src>/build
use_remote=1 docker-compose up --build
```

#### Customizing Remote Build

Edit `build/build.sh` to change the remote repository or branch:

```bash
REMOTE_URL=https://github.com/your-fork/pri-fidoiot.git
REMOTE_BRANCH=your-branch-name
```

### Using Podman (RHEL)

#### Build Local Copy

```bash
cd <fdo-pri-src>/build
podman-compose up --build
```

With sudo (if required):
```bash
sudo podman-compose up --build
```

#### Build from Remote Repository

```bash
cd <fdo-pri-src>/build
use_remote=1 podman-compose up --build
```

### Docker Build Configuration

The Docker build uses:
- **Base Image:** Ubuntu 22.04
- **Java Version:** OpenJDK 17
- **Build User:** fdouser (non-root)
- **Working Directory:** /home/fdouser/pri-fidoiot/

### Build Output Location

After successful Docker/Podman build, artifacts are available at:
```
<fdo-pri-src>/component-samples/demo/
```

This includes:
- `aio/` - All-in-One service
- `owner/` - Owner service
- `rv/` - Rendezvous service
- `reseller/` - Reseller service
- `device/` - Device client
- `db/` - Database configurations
- `scripts/` - Utility scripts

---

## Release Process

### Overview

Releases are created through a GitHub Actions workflow that:
1. Builds the project
2. Creates release tarballs
3. Generates checksums
4. Signs artifacts with Cosign
5. Creates a GitHub release with all artifacts

### Prerequisites for Release

1. **Release Notes:** Create release notes file at `release/notes/release-notes-v<VERSION>.md`
2. **Version Update:** Update version in `pom.xml` files
3. **Permissions:** Write access to the repository
4. **GitHub Token:** Configured in repository secrets

### Release Notes Template

Create a file at `release/notes/release-notes-v<VERSION>.md`:

```markdown
v<VERSION>

Brief description of the release.

## Components

### Protocol Reference Implementation (PRI)

[pri-fidoiot](https://github.com/fido-device-onboard/pri-fidoiot) is a Java-based implementation...

**Supported Cryptographic Modes:**
- **Signing Keys:** ECDSA NIST P-256, ECDSA NIST P-384, RSA2048RESTR, Intel EPID 1.1
- **Key Exchanges:** ECDH256, ECDH384, ASYMKEX2048, ASYMKEX3072, DHKEXid14, DHKEXid15
- **Ciphers:** AES128/CTR/HMAC-SHA256, AES128/HMAC-SHA256, AES256/CTR/HMAC-SHA384, etc.

---

## New Features
- Feature 1
- Feature 2

---

## Fixed Issues

### Security Fixes
- CVE-XXXX-XXXXX

### Dependency Updates
- Updated library X to version Y

---

## Known Issues
- Issue 1 (if any)

---

## SHA256 Checksum

*Following SHA256 checksum is calculated using sha256sum tool:*

```{text}
{{SHA_PRI}} - pri-fidoiot-v<VERSION>.tar.gz
{{SHA_NOTICES}} - pri-fidoiot-NOTICES-v<VERSION>.tar.gz
```

---

## Documentation

https://fido-device-onboard.github.io/docs-fidoiot/<VERSION>
```

**Note:** The placeholders `{{SHA_PRI}}` and `{{SHA_NOTICES}}` are required and will be automatically replaced during the release process.

### Triggering a Release

Releases are triggered manually via GitHub Actions:

1. Navigate to the repository on GitHub
2. Go to **Actions** tab
3. Select **Release PRI** workflow
4. Click **Run workflow**
5. Enter the version number (e.g., `1.1.10`)
6. Click **Run workflow**

### Release Workflow Steps

The automated release workflow performs the following:

1. **Checkout Repository**
   - Fetches the complete repository with tags

2. **Verify Release Notes**
   - Checks that release notes file exists
   - Validates required placeholders are present

3. **Setup Build Environment**
   - Configures Java 17
   - Sets up Maven cache

4. **Build Project**
   ```bash
   mvn -B clean install
   ```

5. **Create PRI Tarball**
   - Packages `component-samples/demo/*` into `pri-fidoiot-v<VERSION>.tar.gz`
   - Generates SHA256 checksum

6. **Create NOTICES Tarball**
   - Packages `NOTICE` and `NOTICES/` directory
   - Generates SHA256 checksum

7. **Sign Artifacts**
   - Uses Cosign to sign both tarballs
   - Generates `.sig` and `.pem` files for each artifact

8. **Render Release Notes**
   - Replaces SHA256 placeholders with actual checksums

9. **Create Git Tag**
   - Creates annotated tag `v<VERSION>`
   - Pushes tag to origin

10. **Create GitHub Release**
    - Creates release with rendered notes
    - Uploads all artifacts:
      - `pri-fidoiot-v<VERSION>.tar.gz`
      - `pri-fidoiot-v<VERSION>.tar.gz.sig`
      - `pri-fidoiot-v<VERSION>.tar.gz.pem`
      - `pri-fidoiot-NOTICES-v<VERSION>.tar.gz`
      - `pri-fidoiot-NOTICES-v<VERSION>.tar.gz.sig`
      - `pri-fidoiot-NOTICES-v<VERSION>.tar.gz.pem`

### Release Artifacts

Each release includes:

1. **PRI Tarball** (`pri-fidoiot-v<VERSION>.tar.gz`)
   - Contains all demo components and configurations
   - Ready-to-deploy services

2. **NOTICES Tarball** (`pri-fidoiot-NOTICES-v<VERSION>.tar.gz`)
   - License information
   - Third-party notices

3. **Signature Files** (`.sig`)
   - Cosign signatures for verification

4. **Certificate Files** (`.pem`)
   - Signing certificates

### Verifying Release Artifacts

To verify the integrity of release artifacts:

```bash
# Verify SHA256 checksum
sha256sum -c <<< "<checksum>  pri-fidoiot-v<VERSION>.tar.gz"

# Verify Cosign signature (requires Cosign installed)
cosign verify-blob \
  --certificate pri-fidoiot-v<VERSION>.tar.gz.pem \
  --signature pri-fidoiot-v<VERSION>.tar.gz.sig \
  pri-fidoiot-v<VERSION>.tar.gz
```

---

## Continuous Integration

### Main CI Workflow

The main CI workflow runs on:
- Push to `master` or `*rel` branches
- Pull requests to `master` or `*rel` branches
- Manual trigger via workflow_dispatch

### CI Pipeline Steps

1. **Checkout Code**
   - Clones the repository

2. **Setup Java 17**
   - Configures Temurin distribution

3. **Build Project**
   ```bash
   mvn clean install
   cd component-samples && tar -czvf demo.tar.gz demo
   ```

4. **Checkout Test Repository**
   - Clones `fido-device-onboard/test-fidoiot`

5. **Configure Test Environment**
   - Adds `host.docker.internal` to `/etc/hosts`
   - Copies demo artifacts to test directory

6. **Generate Test Credentials**
   ```bash
   bash demo_ca.sh
   bash web_csr_req.sh
   bash user_csr_req.sh
   bash keys_gen.sh
   ```

7. **Run Smoke Tests**
   ```bash
   mvn clean test -Dgroups=fdo_pri_smoketest
   ```

8. **Archive Artifacts**
   - Uploads `demo.tar.gz` (retained for 5 days)
   - Only on non-PR builds

### Other CI Workflows

#### CodeQL Analysis
- Automated security scanning
- Runs on schedule and pull requests

#### Scorecard
- OpenSSF security scorecard checks
- Evaluates security best practices

#### Dependabot
- Automated dependency updates
- Configured in `.github/dependabot.yml`

---

## Troubleshooting

### Common Build Issues

#### Heap Size Issues

If you encounter heap size errors:

```bash
export MAVEN_OPTS="-Xmx2048m -XX:MaxPermSize=512m"
mvn clean install
```

#### Proxy Issues

Ensure proxy settings are correctly configured:

```bash
# Check Java proxy settings
echo $_JAVA_OPTIONS

# Check system proxy
echo $http_proxy
echo $https_proxy
```

Update Maven settings if needed (`~/.m2/settings.xml`):

```xml
<settings>
  <proxies>
    <proxy>
      <id>http-proxy</id>
      <active>true</active>
      <protocol>http</protocol>
      <host>proxy.example.com</host>
      <port>8080</port>
    </proxy>
  </proxies>
</settings>
```

#### PGP Verification Failures

If PGP verification fails during build:

```bash
mvn clean install -Dpgpverify.skip=true
```

**Note:** For production builds, configure proper PGP verification in `~/.m2/settings.xml`.

#### Database Issues

If encountering stale database data:

```bash
# Delete persisted database files
rm -rf <fdo-pri-src>/component-samples/demo/db/app-data/*
```

Database files are not cleaned by `mvn clean` and persist across builds.

#### Docker Build Issues

**Permission Denied:**
```bash
# Add user to docker group (Ubuntu)
sudo usermod -aG docker $USER
newgrp docker
```

**Out of Memory:**
```bash
# Increase Docker memory limit in docker-compose.yml
mem_limit: 2000m
mem_reservation: 1500m
```

### Build Verification

After a successful build, verify:

1. **Artifacts Exist:**
   ```bash
   ls -la component-samples/demo/aio/aio.jar
   ls -la component-samples/demo/owner/aio.jar
   ls -la component-samples/demo/device/device.jar
   ```

2. **Services Start:**
   ```bash
   cd component-samples/demo/aio
   java -jar aio.jar
   # Should see: [INFO] Started All-in-one demo Service.
   ```

3. **Run Tests:**
   ```bash
   mvn test
   ```

### Getting Help

- **Documentation:** https://fido-device-onboard.github.io/docs-fidoiot/
- **Issues:** https://github.com/fido-device-onboard/pri-fidoiot/issues
- **Discussions:** https://github.com/fido-device-onboard/pri-fidoiot/discussions

---

## Additional Resources

### Key Configuration Files

- `pom.xml` - Maven project configuration and dependencies
- `build/build.sh` - Docker build script
- `build/Dockerfile` - Docker image definition
- `build/docker-compose.yml` - Docker Compose configuration
- `component-samples/demo/*/service.yml` - Service runtime configuration
- `component-samples/demo/*/service.env` - Service credentials
- `component-samples/demo/*/hibernate.cfg.xml` - Database configuration

### Maven Plugins Used

- **maven-compiler-plugin** (3.12.1) - Java compilation (target: Java 11)
- **maven-checkstyle-plugin** (3.1.0) - Code style validation
- **maven-surefire-plugin** (3.2.5) - Unit test execution
- **maven-war-plugin** (3.4.0) - WAR packaging
- **pgpverify-maven-plugin** (1.18.2) - Dependency signature verification

### Dependency Versions (v1.1.10)

- **Java:** 11 (compile target), 17 (runtime)
- **Hibernate:** 6.4.4.Final
- **Tomcat:** 11.0.10
- **Jackson:** 2.16.1
- **Log4j:** 2.23.0
- **BouncyCastle FIPS:** 2.1.1
- **MySQL:** 8.2.0
- **MariaDB:** 3.0.5
- **PostgreSQL:** 42.5.5

---

## Security Considerations

### Production Deployment

When deploying to production:

1. **Generate Fresh Credentials**
   - Use `component-samples/demo/scripts/keys_gen.sh`
   - Never use demo credentials in production

2. **Certificate Management**
   - Replace self-signed certificates with CA-signed certificates
   - Configure proper certificate validation

3. **Database Security**
   - Use external database (not embedded H2)
   - Configure proper authentication and encryption
   - Regular backups

4. **Network Security**
   - Enable HTTPS/TLS for all communications
   - Configure firewalls appropriately
   - Use mTLS where applicable

5. **Keystore Management**
   - Secure keystore files with proper permissions
   - Use hardware security modules (HSM) for production keys
   - Regular key rotation

### Security Updates

Monitor and apply security updates:
- Check release notes for CVE fixes
- Subscribe to security advisories
- Keep dependencies up to date (Dependabot enabled)

---

**Document Version:** 1.0  
**Last Updated:** 2026-06-16  
**Applicable to PRI Version:** 1.1.11