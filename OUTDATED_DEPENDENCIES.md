# Outdated Dependencies Analysis

**Analysis Date:** 2026-06-16  
**Project Version:** 1.1.10  
**Repository:** pri-fidoiot

This document lists dependencies that may be outdated based on the versions specified in `pom.xml`. Note that some dependencies may be intentionally kept at specific versions for compatibility or stability reasons.

---

## Critical Updates Recommended

### Security-Sensitive Dependencies

| Dependency | Current Version | Latest Known | Severity | Notes |
|------------|----------------|--------------|----------|-------|
| **commons-beanutils** | 1.9.4 | 1.9.4 | ⚠️ MEDIUM | Last release 2019, consider migration to alternatives |
| **slf4j** | 1.7.36 | 2.0.x | ⚠️ MEDIUM | Major version behind, but 1.7.x still maintained |
| **postgresql** | 42.5.5 | 42.7.x+ | ⚠️ MEDIUM | Several versions behind |
| **mariadb** | 3.0.5 | 3.4.x+ | ⚠️ MEDIUM | Multiple minor versions behind |
| **mysql** | 8.2.0 | 8.4.x+ | ⚠️ MEDIUM | Several minor versions behind |

---

## Core Dependencies

### Apache Commons Libraries

| Dependency | Current Version | Latest Known | Status | Notes |
|------------|----------------|--------------|--------|-------|
| commons-beanutils | 1.9.4 | 1.9.4 | ⚠️ OLD | Last updated 2019, consider alternatives |
| commons-codec | 1.16.1 | 1.17.x | ⚠️ UPDATE | Minor update available |
| commons-text | 1.11.0 | 1.12.x | ⚠️ UPDATE | Minor update available |
| commons-lang3 | 3.14.0 | 3.15.x+ | ⚠️ UPDATE | Minor update available |
| commons-configuration2 | 2.9.0 | 2.11.x | ⚠️ UPDATE | Multiple minor versions behind |

### Jackson Libraries

| Dependency | Current Version | Latest Known | Status | Notes |
|------------|----------------|--------------|--------|-------|
| jackson | 2.15.0 | 2.17.x | ⚠️ UPDATE | Multiple minor versions behind |
| jackson-dataformat | 2.16.1 | 2.17.x | ⚠️ UPDATE | Minor version behind |
| jackson-databind | 2.16.1 | 2.17.x | ⚠️ UPDATE | Minor version behind |

**Recommendation:** Update all Jackson libraries to the same version (2.17.x) for consistency.

### Logging Libraries

| Dependency | Current Version | Latest Known | Status | Notes |
|------------|----------------|--------------|--------|-------|
| log4j | 2.23.0 | 2.23.x | ✅ CURRENT | Up to date |
| slf4j | 1.7.36 | 2.0.x | ⚠️ MAJOR | Major version available, but 1.7.x still supported |

**Note:** SLF4J 2.0.x requires Java 8+. Migration may require code changes.

### Security & Cryptography

| Dependency | Current Version | Latest Known | Status | Notes |
|------------|----------------|--------------|--------|-------|
| bcfips | 2.1.1 | 2.2.x | ⚠️ UPDATE | Minor update available |
| bcpkix-fips | 2.1.9 | 2.2.x | ⚠️ UPDATE | Minor update available |
| cose-java | 1.1.0 | 1.1.0 | ✅ CURRENT | Up to date |

### Web & HTTP

| Dependency | Current Version | Latest Known | Status | Notes |
|------------|----------------|--------------|--------|-------|
| javax.servlet-api | 4.0.1 | 6.0.x (Jakarta) | ⚠️ MAJOR | Consider migration to Jakarta EE |
| tomcat | 11.0.10 | 11.0.x | ✅ CURRENT | Recent version |
| apache-httpcomponents | 4.5.14 | 5.3.x | ⚠️ MAJOR | Major version available (HttpClient 5) |

### Database & ORM

| Dependency | Current Version | Latest Known | Status | Notes |
|------------|----------------|--------------|--------|-------|
| hibernate | 6.4.4.Final | 6.5.x+ | ⚠️ UPDATE | Minor updates available |
| h2db | 2.2.224 | 2.3.x | ⚠️ UPDATE | Minor version behind |
| mariadb | 3.0.5 | 3.4.x | ⚠️ UPDATE | Multiple minor versions behind |
| mysql | 8.2.0 | 8.4.x | ⚠️ UPDATE | Multiple minor versions behind |
| postgresql | 42.5.5 | 42.7.x | ⚠️ UPDATE | Multiple minor versions behind |

### Testing

| Dependency | Current Version | Latest Known | Status | Notes |
|------------|----------------|--------------|--------|-------|
| junit-jupiter | 5.10.2 | 5.11.x | ⚠️ UPDATE | Minor update available |

### Other Dependencies

| Dependency | Current Version | Latest Known | Status | Notes |
|------------|----------------|--------------|--------|-------|
| snakeyaml | 2.2 | 2.3 | ⚠️ UPDATE | Minor update available |

---

## Maven Plugins

### Build Plugins

| Plugin | Current Version | Latest Known | Status | Notes |
|--------|----------------|--------------|--------|-------|
| maven-checkstyle-plugin | 3.1.0 | 3.5.x | ⚠️ UPDATE | Multiple versions behind |
| maven-clean-plugin | 3.3.2 | 3.4.x | ⚠️ UPDATE | Minor update available |
| maven-compiler-plugin | 3.12.1 | 3.13.x | ⚠️ UPDATE | Minor update available |
| maven-dependency-plugin | 3.6.1 | 3.8.x | ⚠️ UPDATE | Minor updates available |
| maven-jar-plugin | 3.3.0 | 3.4.x | ⚠️ UPDATE | Minor update available |
| maven-javadoc-plugin | 3.6.3 | 3.10.x | ⚠️ UPDATE | Multiple minor versions behind |
| maven-project-info-reports-plugin | 3.5.0 | 3.7.x | ⚠️ UPDATE | Minor updates available |
| maven-resources-plugin | 3.3.1 | 3.3.1 | ✅ CURRENT | Up to date |
| maven-site-plugin | 4.0.0.M13 | 4.0.0.M16+ | ⚠️ UPDATE | Milestone updates available |
| maven-surefire-plugin | 3.2.5 | 3.5.x | ⚠️ UPDATE | Multiple minor versions behind |
| maven-surefire-report-plugin | 3.2.5 | 3.5.x | ⚠️ UPDATE | Multiple minor versions behind |
| maven-war-plugin | 3.4.0 | 3.4.0 | ✅ CURRENT | Up to date |
| pgpverify-plugin | 1.18.2 | 1.19.x | ⚠️ UPDATE | Minor update available |
| exec-maven-plugin | 3.2.0 | 3.4.x | ⚠️ UPDATE | Minor updates available |

---

## Update Priority Recommendations

### High Priority (Security & Stability)

1. **Database Drivers** - Update PostgreSQL, MySQL, MariaDB drivers
   - Security patches and bug fixes
   - Better performance and compatibility

2. **Jackson Libraries** - Update to 2.17.x
   - Security fixes
   - Keep all Jackson dependencies at same version

3. **BouncyCastle** - Update bcfips and bcpkix-fips
   - Cryptographic library updates often include security fixes

4. **Apache Commons** - Update commons-text, commons-lang3, commons-codec
   - Bug fixes and performance improvements

### Medium Priority (Features & Improvements)

5. **Hibernate** - Update to 6.5.x
   - Performance improvements
   - Bug fixes

6. **H2 Database** - Update to 2.3.x
   - Bug fixes for embedded database

7. **JUnit** - Update to 5.11.x
   - Testing framework improvements

8. **Maven Plugins** - Update build plugins
   - Better build performance
   - Bug fixes

### Low Priority (Consider for Future)

9. **SLF4J** - Consider migration to 2.0.x
   - Requires code review for breaking changes
   - 1.7.x still maintained

10. **Apache HttpComponents** - Consider migration to 5.x
    - Major version change requires code updates
    - Better async support

11. **Servlet API** - Consider Jakarta EE migration
    - Future-proofing for Java EE evolution
    - Requires significant code changes

---

## Update Commands

To check for updates automatically:

```bash
# Display dependency updates
mvn versions:display-dependency-updates

# Display plugin updates
mvn versions:display-plugin-updates

# Display property updates
mvn versions:display-property-updates

# Update all properties to latest versions (use with caution)
mvn versions:update-properties
```

---

## Migration Considerations

### Breaking Changes to Watch For

1. **SLF4J 1.7.x → 2.0.x**
   - API changes in some methods
   - Fluent API additions
   - Requires Java 8+

2. **HttpComponents 4.x → 5.x**
   - Complete API redesign
   - Async-first architecture
   - Significant code changes required

3. **Servlet API 4.x → Jakarta 6.x**
   - Package name changes (javax.* → jakarta.*)
   - Requires code refactoring
   - Tomcat 10+ required

4. **Jackson 2.15.x → 2.17.x**
   - Generally backward compatible
   - Check release notes for specific changes

### Testing Strategy

After updating dependencies:

1. **Run Full Test Suite**
   ```bash
   mvn clean test
   ```

2. **Run Integration Tests**
   ```bash
   mvn verify
   ```

3. **Check for Deprecation Warnings**
   ```bash
   mvn clean compile -Xlint:deprecation
   ```

4. **Security Scan**
   ```bash
   mvn dependency:analyze
   mvn dependency-check:check
   ```

---

## Automated Dependency Management

### Dependabot Configuration

The repository has Dependabot enabled (`.github/dependabot.yml`), which automatically:
- Checks for dependency updates
- Creates pull requests for updates
- Provides security alerts

### Recommended Actions

1. **Review Dependabot PRs regularly**
2. **Test updates in development environment**
3. **Update dependencies in batches by category**
4. **Monitor security advisories**

---

## Security Considerations

### Known Vulnerabilities

Check dependencies for known CVEs:

```bash
# Using OWASP Dependency Check
mvn org.owasp:dependency-check-maven:check

# Using Snyk (if configured)
snyk test
```

### Security Update Policy

- **Critical vulnerabilities:** Update immediately
- **High severity:** Update within 1 week
- **Medium severity:** Update in next release cycle
- **Low severity:** Update during regular maintenance

---

## Notes

- **Current Date:** June 2026 - Some "latest" versions listed may have newer releases
- **Compatibility:** Always test updates in development before production
- **Version Policy:** The project uses Java 11 for compilation, Java 17 for runtime
- **FIPS Compliance:** BouncyCastle FIPS versions must maintain FIPS 140-2 certification

---

## Useful Commands

```bash
# Check for outdated dependencies
mvn versions:display-dependency-updates

# Check for outdated plugins
mvn versions:display-plugin-updates

# Update a specific property
mvn versions:set-property -Dproperty=junit-jupiter.version -DnewVersion=5.11.0

# Generate dependency tree
mvn dependency:tree

# Analyze dependencies
mvn dependency:analyze

# Check for security vulnerabilities
mvn org.owasp:dependency-check-maven:check
```

---

**Recommendation:** Create a dependency update plan that prioritizes security updates first, followed by stability improvements, and finally feature enhancements. Test thoroughly after each batch of updates.

**Next Steps:**
1. Review this list with the development team
2. Prioritize updates based on security and stability needs
3. Create a testing plan for each update batch
4. Update dependencies incrementally
5. Monitor for any issues after updates