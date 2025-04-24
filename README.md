# Drupal 11 Starter for Non-Composer Hosting

> **⚠️ NOTICE**: This project will soon be migrated to a DDEV addon to make it more easily distributable and independent from Drupal. This migration will allow for easier integration into any project and simplify maintenance.

This repository contains a **Drupal 11** project configured for **local development using DDEV**, with custom commands to simplify deployment on **shared hosting environments** that do **not support Composer**.

---

## 🛠️ Local Development with DDEV

### Prerequisites

- [DDEV](https://ddev.com/get-started/)

### Setup

1. **Clone the repository**

```bash
git clone git@github.com:mordonez/drupal11-nocomposer-starter.git
cd drupal11-nocomposer-starter
```

2. **Start DDEV**

```bash
ddev configure
ddev start
```

3. **Install dependencies**

```bash
ddev composer install
```

4. **Install Drupal (new site)**

```bash
ddev drush site:install --account-name=mysuperadmin -y
```

5. **Import database (if a production dump exists)**

```bash
ddev import-db --src=path/to/database.sql.gz
```

5. **Update the database and clear cache**

```bash
ddev drush updb
ddev drush cr
```

6. **Open the site in the browser**

```bash
ddev launch
```

7. **Log in as admin (temporary login)**

```bash
ddev launch $(ddev drush uli)
```

### Useful DDEV Commands

Run Drush:

```bash
ddev drush [command]
```

Access the database:

```bash
ddev mysql
```

Export database:

```bash
ddev export-db --file=./dump.sql.gz
```

---

## 🚀 Production Build & Upload

This project includes **custom DDEV commands** to generate a production-ready version of the website that can be uploaded to a non-Composer hosting environment.

### Generate production build

```bash
ddev build
```

This command:

- Creates a `public_html` directory with all necessary files
- Optimizes dependencies for production
- Adjusts paths to work without Composer on the server

### Upload files to the remote server

```bash
ddev upload --host=example.com --user=username --dir=/remote/path [optional parameters]
```

You can customize the transfer using several parameters.

#### Available Parameters

| Parameter   | Description                           | Example                               | Required |
|-------------|---------------------------------------|---------------------------------------|----------|
| `--host`    | Remote server hostname                | `--host=your.server.com`              | **Yes**  |
| `--user`    | SFTP username                         | `--user=my-user`                      | **Yes**  |
| `--dir`     | Remote directory path                 | `--dir=/public_html`                  | **Yes**  |
| `--port`    | SFTP port                             | `--port=22`                           | No       |
| `--password`| SFTP password                         | `--password="mypassword"`             | No*      |
| `--delete`  | Delete remote files not in local      | `--delete`                            | No       |
| `--exclude` | Additional files/folders to exclude   | `--exclude="vendor,core/tests"`       | No       |

_* If password is not provided, it will be requested interactively_

### Examples

```bash
# Exclude additional folders
ddev upload --host=example.com --user=username --dir=/remote/path --exclude="important_file.php,core/tests"

# Exclude specific files
ddev upload --host=example.com --user=username --dir=/remote/path --exclude=".htaccess,robots.txt"

# Combine with other options
ddev upload --host=example.com --user=username --dir=/remote/path --host=example.com --exclude="config,tmp" --delete
```

### Authentication Modes

1. **Interactive**: If no password is provided, it will be requested during execution.

2. **Non-interactive**: Ideal for CI/CD or automation.

> Note: For passwords with special characters, use quotes like `--password='my?secure!password'`

### Remote File Handling

By default, the command **does not delete** remote files that are not present locally. To enable deletion:

```bash
ddev upload --delete
```

> ⚠️ Warning: The `--delete` option will remove any remote files/directories that do not exist in the local source folder. Use with caution.

### Automatically Excluded Files

To protect important data, the following files and directories are never modified on the server:

- `sites/default/files/` (user uploads)
- `sites/default/private/` (private files)
- `sites/default/tmp/` (temporary files)
- `sites/default/settings*.php` (configuration files)
- `sites/default/services*.yml` (service configuration)

---

## 📦 Full Deployment (build + upload)

```bash
ddev deploy [upload parameters]
```

This command runs both `build` and `upload` in a single step, ideal for full deployments. It accepts the same parameters as the `upload` command.

Example:

```bash
ddev deploy --host=server.com --user=myuser --password="mypassword"
```

> Important: During upload, folders like `sites/*/files` and configuration files like `settings.php` are automatically excluded to prevent overwriting content and settings in production.

---

## Project Structure

- `web/`: Drupal root for local development
- `composer.json`: Project dependency configuration
- `public_html/`: (Generated via `ddev build`) Production-ready version of the site

## Contribute

Contributions to improve this project are welcome! Here's how you can contribute:

1. Fork the repository
2. Create a new branch (git checkout -b feature/amazing-feature)
3. Make your changes
4. Commit your changes (git commit -m 'Add some amazing feature')
5. Push to the branch (git push origin feature/amazing-feature)
6. Open a Pull Request

For major changes, please open an issue first to discuss what you would like to change.

## License

This project is licensed under the GNU General Public License v3.0 - see the LICENSE file for details.
