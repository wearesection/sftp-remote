# SFTP Remote Upload Action

[![GitHub release](https://img.shields.io/github/v/release/wearesection/sftp-action)](https://github.com/wearesection/sftp-action/releases)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-SFTP%20Remote-blue)](https://github.com/marketplace/actions/sftp-remote)

A GitHub Action for uploading files and directories to remote servers via SFTP with SSH authentication.

## Features

- **Multiple Authentication Methods**: Password, private key, SSH agent support
- **Flexible Upload**: Single files, directories, or multiple files with `additionalPaths`
- **Auto-Create Directories**: Automatically creates remote directories if they don't exist
- **Secure**: Works seamlessly with GitHub Secrets for credential management
- **Simple Configuration**: Easy-to-use inputs with sensible defaults

## Inputs

### `host`
**Required** The hostname of the SFTP server. Default: `"localhost"`

### `port`
**Optional** The SSH port of the SFTP server. Default: `22`

### `username`
**Required** The SSH username for authentication. Default: `"root"`

### `password`
**Required** The SSH password for authentication. Default: `"password"`

### `localPath`
**Required** Path to a local file or directory to upload.

### `remotePath`
**Required** Path to the remote destination (directories will be created if they don't exist).

### `agent`
**Optional** Path to local SSH Agent for key forwarding.

### `privateKey`
**Optional** SSH private key content or path to key file (use with GitHub Secrets).

### `privateKeyIsFile`
**Optional** Set to `"true"` if `privateKey` is a file path. Default: `"false"`

### `passphrase`
**Optional** Passphrase for encrypted private keys.

### `additionalPaths`
**Optional** Upload multiple files. JSON object with key/value pairs. Example: `'{"./src":"./dest"}'`

## Usage Examples

### Basic Upload with Password Authentication

```yaml
name: Deploy to Server

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Upload file via SFTP
        uses: wearesection/sftp-action@1.0.1
        with:
          host: ${{ secrets.SFTP_HOST }}
          username: ${{ secrets.SFTP_USER }}
          password: ${{ secrets.SFTP_PASSWORD }}
          localPath: './dist/index.js'
          remotePath: '/var/www/app/index.js'
```

### Upload with Private Key Authentication (Recommended)

```yaml
name: Deploy with SSH Key

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Upload with private key
        uses: wearesection/sftp-action@1.0.1
        with:
          host: ${{ secrets.SFTP_HOST }}
          port: 22
          username: ${{ secrets.SFTP_USER }}
          password: ${{ secrets.SFTP_PASSWORD }}
          privateKey: ${{ secrets.SSH_PRIVATE_KEY }}
          localPath: './build'
          remotePath: '/var/www/app/'
```

### Upload Multiple Files

```yaml
name: Deploy Multiple Files

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Upload multiple files
        uses: wearesection/sftp-action@1.0.1
        with:
          host: ${{ secrets.SFTP_HOST }}
          username: ${{ secrets.SFTP_USER }}
          password: ${{ secrets.SFTP_PASSWORD }}
          localPath: './dist/main.js'
          remotePath: '/var/www/app/main.js'
          additionalPaths: |
            {
              "./dist/styles.css": "/var/www/app/styles.css",
              "./dist/assets": "/var/www/app/assets"
            }
```

### Upload with SSH Agent

```yaml
name: Deploy with SSH Agent

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup SSH Agent
        uses: webfactory/ssh-agent@v0.9.0
        with:
          ssh-private-key: ${{ secrets.SSH_PRIVATE_KEY }}

      - name: Upload via SSH Agent
        uses: wearesection/sftp-action@1.0.1
        with:
          host: ${{ secrets.SFTP_HOST }}
          username: ${{ secrets.SFTP_USER }}
          password: ${{ secrets.SFTP_PASSWORD }}
          agent: ${{ env.SSH_AUTH_SOCK }}
          localPath: './dist'
          remotePath: '/var/www/app/'
```

## Security Considerations

### Always Use GitHub Secrets

**Never** hardcode credentials in your workflow files. Always use GitHub Secrets:

1. Go to your repository → Settings → Secrets and variables → Actions
2. Click "New repository secret"
3. Add your credentials:
   - `SFTP_HOST` - Your server hostname
   - `SFTP_USER` - Your SSH username
   - `SFTP_PASSWORD` - Your SSH password (if using password auth)
   - `SSH_PRIVATE_KEY` - Your private key content (if using key auth)

### Prefer Private Key Authentication

Private key authentication is more secure than password authentication:

- Generate an SSH key pair: `ssh-keygen -t ed25519 -C "github-actions"`
- Add the public key to your server's `~/.ssh/authorized_keys`
- Store the private key in GitHub Secrets as `SSH_PRIVATE_KEY`
- Use the `privateKey` input in your workflow

### SSH Key Rotation

Rotate your SSH keys regularly:
- Generate new keys every 90 days
- Revoke old keys from the server
- Update GitHub Secrets with new keys

## Troubleshooting

### Connection Timeout

If you encounter connection timeouts:
- Verify the host and port are correct
- Check firewall rules allow SSH connections from GitHub Actions IPs
- Ensure the server is accessible from the internet

### Authentication Failed

If authentication fails:
- Double-check your credentials in GitHub Secrets
- Verify username and password/key are correct
- For private keys, ensure the format is correct (include headers/footers)
- Check server logs for detailed error messages

### Permission Denied

If uploads fail with permission denied:
- Verify the user has write permissions to the remote path
- Check directory ownership and permissions on the server
- Ensure remote directories exist or can be created

### Invalid additionalPaths JSON

If `additionalPaths` fails:
- Ensure JSON is valid (use a JSON validator)
- Use proper JSON formatting: `{"localPath": "remotePath"}`
- Escape quotes if needed in YAML

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'feat: add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

Built with:
- [ssh2-sftp-client](https://github.com/theophilusx/ssh2-sftp-client) - SFTP client library
- [@actions/core](https://github.com/actions/toolkit/tree/main/packages/core) - GitHub Actions toolkit

---

![Repobeats Analytics](https://repobeats.axiom.co/api/embed/e320e58b935a223f36b9e7c5da1f9ff91bd817ed.svg "Repobeats analytics image")
