# Security Policy

## Supported Versions

We release patches for security vulnerabilities. Which versions are eligible for receiving such patches depends on the CVSS v3.0 Rating:

| Version | Supported          |
| ------- | ------------------ |
| 1.0.x   | :white_check_mark: |
| < 1.0   | :x:                |

## Reporting a Vulnerability

The SFTP Action team and community take security bugs seriously. We appreciate your efforts to responsibly disclose your findings, and will make every effort to acknowledge your contributions.

### How to Report a Security Vulnerability

**Please do not report security vulnerabilities through public GitHub issues.**

Instead, please report them via one of the following methods:

1. **GitHub Security Advisories** (Preferred)
   - Go to the [Security tab](https://github.com/wearesection/sftp-action/security) of this repository
   - Click "Report a vulnerability"
   - Fill out the form with details about the vulnerability

2. **Email**
   - Send an email to the repository maintainers
   - Include the word "SECURITY" in the subject line
   - Provide detailed information about the vulnerability

### What to Include in Your Report

To help us better understand the nature and scope of the issue, please include as much of the following information as possible:

- Type of issue (e.g., buffer overflow, SQL injection, cross-site scripting, etc.)
- Full paths of source file(s) related to the manifestation of the issue
- The location of the affected source code (tag/branch/commit or direct URL)
- Any special configuration required to reproduce the issue
- Step-by-step instructions to reproduce the issue
- Proof-of-concept or exploit code (if possible)
- Impact of the issue, including how an attacker might exploit it

### What to Expect

- **Acknowledgment**: We will acknowledge your email within 48 hours
- **Communication**: We will send a more detailed response within 7 days indicating the next steps in handling your report
- **Updates**: We will keep you informed about the progress towards a fix and full announcement
- **Credit**: We may ask you how you would like to be credited for discovering the issue

## Security Best Practices for Users

When using this action, please follow these security best practices:

### 1. Always Use GitHub Secrets

Never hardcode sensitive credentials in your workflow files:

**Bad** :x:
```yaml
with:
  host: 'myserver.com'
  username: 'admin'
  password: 'mypassword123'  # Never do this!
```

**Good** :white_check_mark:
```yaml
with:
  host: ${{ secrets.SFTP_HOST }}
  username: ${{ secrets.SFTP_USER }}
  password: ${{ secrets.SFTP_PASSWORD }}
```

### 2. Prefer SSH Key Authentication

Private key authentication is more secure than password authentication:

```yaml
with:
  host: ${{ secrets.SFTP_HOST }}
  username: ${{ secrets.SFTP_USER }}
  password: ${{ secrets.SFTP_PASSWORD }}
  privateKey: ${{ secrets.SSH_PRIVATE_KEY }}
```

### 3. Rotate Credentials Regularly

- Rotate SSH keys every 90 days
- Update GitHub Secrets when credentials change
- Revoke old keys from your servers

### 4. Limit Permissions

- Use a dedicated service account with minimal permissions
- Restrict write access to only necessary directories
- Consider using read-only access where possible

### 5. Pin Action Versions

Use specific version tags instead of branches:

**Better** :white_check_mark:
```yaml
uses: wearesection/sftp-action@1.0.1
```

**Less Secure** :warning:
```yaml
uses: wearesection/sftp-action@main
```

### 6. Review Workflow Logs

- Ensure credentials are not leaked in logs
- This action masks sensitive inputs automatically
- Review GitHub Actions logs after each run

## Known Security Considerations

### SSH Connection Security

This action uses the `ssh2-sftp-client` library which:
- Supports modern SSH protocol versions
- Uses secure encryption algorithms
- Validates host keys (ensure you trust the target server)

### Credential Handling

- All sensitive inputs (`password`, `privateKey`, `passphrase`) are marked as secrets
- GitHub Actions automatically masks these values in logs
- Credentials are only used during the workflow execution and are not persisted

### Network Security

- Connections use SSH/SFTP protocol (secure by default)
- Ensure your SFTP server uses strong SSH configurations
- Consider using firewall rules to restrict access

## Disclosure Policy

When we receive a security bug report, we will:

1. Confirm the problem and determine the affected versions
2. Audit code to find any similar problems
3. Prepare fixes for all supported versions
4. Release new security patch versions as soon as possible
5. Publish a security advisory on GitHub

## Comments on this Policy

If you have suggestions on how this process could be improved, please submit a pull request or open an issue to discuss.

---

Thank you for helping keep SFTP Action and our users safe!
