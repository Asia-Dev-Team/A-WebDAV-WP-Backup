# A+ WebDAV WP Backup

WordPress plugin that backs up a whole site — files and database — and uploads the backups to a WebDAV storage. Backups run manually from the admin panel or on a schedule.

## What it does

- Creates two archives per backup in `wp-content/site-backups/`:
  - `<type>_files_<YYYYMMDD_HHMM>.zip` — all site files (the backup directory itself is excluded);
  - `<type>_db_<YYYYMMDD_HHMM>.sql.gz` — database dump made with `mysqldump`.
  `<type>` is `manual` or the scheduled run type.
- Uploads both archives to the configured WebDAV URL (HTTP `PUT`).
- Keeps a set number of the newest backups locally and on WebDAV, and deletes older ones.
- Sends an email with the backup location, size, and time.
- Protects the local backup directory with an `.htaccess` deny rule.
- Lets an administrator download local backups from the admin panel.

## Requirements

- WordPress with administrator access (`manage_options`).
- PHP extensions: `zip` (`ZipArchive`) and `curl`.
- PHP `exec()` enabled, and `mysqldump` and `gzip` available on the server.
- A WebDAV storage that accepts `PUT`, `PROPFIND`, and `DELETE`.

## Installation

1. Copy the plugin folder to `wp-content/plugins/`.
2. Activate **A+ WebDAV WP Backup** in **Plugins**.
3. Open **WebDAV WP Backup** in the admin menu.

## Settings

| Setting | Meaning | Default |
|---|---|---|
| Backup Frequency (in hours) | Interval between scheduled backups | 24 |
| Delay Start (in hours) | Delay before the first scheduled backup | 0 |
| Local Backups to Keep | Number of newest local backups to keep | 1 |
| Remote Backups to Keep | Number of newest WebDAV backups to keep | 2 |
| Notification Email | Where completion emails go | site admin email |
| Email Subject Prefix | Prefix for the email subject | site name |
| Remote Storage URL | WebDAV folder URL | — |
| Username / Password | WebDAV credentials | — |

Use the connection test in the admin panel to check the WebDAV settings before the first backup.

## Project structure

```
webdav-wp-backup.php         Plugin bootstrap: hooks, admin menu, AJAX, download handler, cron interval
includes/backup-functions.php  Backup creation, WebDAV upload, cleanup, email, backup list
includes/connection-tests.php  WebDAV connection test (AJAX)
admin/admin-interface.php      Admin page and settings registration
assets/                        Admin CSS and JS
uninstall.php                  Removes options, local backups, and the log file
```

## Uninstall

Deleting the plugin removes all its options, the `wp-content/site-backups/` directory with local backups, and the plugin log file. Backups on WebDAV are not deleted.

## Known limitations

- SSL certificate and host verification are disabled for WebDAV requests.
- WebDAV credentials are stored in WordPress options as plain text.
- The database password is passed to `mysqldump` on the command line.
- Hosting without `exec()` or `mysqldump` cannot create the database backup.
