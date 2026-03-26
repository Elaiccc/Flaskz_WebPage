# Flaskz-admin

Flaskz-admin is a management system starter template built on top of [Flaskz](https://pypi.org/project/flaskz/). It provides a base project structure, backend framework, and admin-oriented modules so you can quickly build internal systems and extend business-specific features.

Chinese version: [README.zh-CN.md](./README.zh-CN.md)

## Documentation

1. [Database initialization and common helpers](http://zhangyiheng.com/blog/articles/py_flaskz_model_init.html)
2. [Model extension classes](http://zhangyiheng.com/blog/articles/py_flaskz_model_mixin.html)
3. [API wrapping, access control, and system logs](http://zhangyiheng.com/blog/articles/py_flaskz_api.html)
4. [Utility functions](http://zhangyiheng.com/blog/articles/py_flaskz_utils.html)
5. [Flaskz-admin starter walkthrough (Hello World)](http://zhangyiheng.com/blog/articles/py_flaskz_admin.html)
6. [User manual](http://zhangyiheng.com/blog/articles/py_flaskz_manual.html)
7. [Development specification](http://zhangyiheng.com/blog/articles/dev_spec.html)

## Quick Start

1. Update the application configuration in [config.py](./config.py)
   - `SECRET_KEY`: application secret key used to secure client sessions. It should be long and hard to guess.
   - `FLASKZ_DATABASE_URI`: database URI. Default: `./_sqlite/flaskz-admin.db`
2. Update the Alembic configuration in [alembic.ini](./alembic.ini)
   - `sqlalchemy.url`: database URI. Default: `./_sqlite/flaskz-admin.db`
3. Install dependencies
   - `pip install -r requirements.txt`
4. Initialize the database with [cli.py](./cli.py)
   - The SQLite database in `_sqlite/flaskz-admin.db` is already initialized.
   - Set the Flask app environment variable:
     - macOS/Linux: `export FLASK_APP=admin_app.py`
     - Windows: `set FLASK_APP=admin_app.py`
   - Create or upgrade tables:
     - `flask admin db upgrade`
     - Initial password: `taozh`
   - Initialize seed data from [app/sys_mgmt/_init_db.py](./app/sys_mgmt/_init_db.py):
     - `flask admin db init`
     - Initial password: `taozh`
5. Start the application from [admin_app.py](./admin_app.py)
   - `export FLASK_APP=admin_app.py`
   - `flask run --host=0.0.0.0 --port=666`
   - Check the Flask process:
     - `ps aux | grep 'python.*flask run'`
   - Stop the Flask process:
     - `pkill -f "python.*flask run"`
6. Access the app
   - URL: [http://127.0.0.1:666](http://127.0.0.1:666)
   - Default credentials: `admin/admin`

## Project Structure

- `_doc`: project documentation
  - `flaskz-admin.postman_collection.json`: Postman collection for the APIs
  - `sys-mgmt.md`: RBAC and license feature overview
  - `alembic.md`: common Alembic commands
- `_sqlite`: SQLite database directory, used when needed
- `_syslog`: system log directory
- `app`: main application package
  - `_ext`: optional extensions
    - `ncs`: NCS-related extension
    - `redis_ws`: Redis and WebSocket broadcasting extension
  - `api`: application API wrappers
  - `app_page`: static frontend pages based on focus-ui; can also be served by Nginx or another proxy if needed
  - `main`: page serving and application error handling
    - `errors.py`: application exception handling
    - `page.py`: page routes
  - `modules`: system modules and model wrappers
  - `sys_init`: application initialization
    - `status_codes.py`: status codes and internationalization
  - `sys_mgmt`: built-in role-based access control module
    - `license`: license feature
    - `_init_db.py`: system data initialization for modules, roles, and permissions
    - `auth.py`: login and permission checks
    - `_private.py`: internal utilities such as viewing system logs and routes
  - `utils`: utility helpers
  - `__init__.py`: application creation and initialization
- `migrations`: Alembic migration files
  - `versions`: migration version files
- `alembic.ini`: Alembic configuration
- `tests`: unit and performance tests
- `admin_app.py`: application entry point
- `cli.py`: command-line tool for initialization and maintenance, for example `flask admin db help`
- `config.py`: development configuration
- `config.ini`: operations/deployment configuration file; optional, and values here override `config.py`
- `requirements.txt`: dependency list for `pip install -r requirements.txt`

## Contact

Email: taozh@cisco.com / taozh1982@gmail.com

## Versions

- **2.1.2** `2024/06/01`
  - [A] Added the `SysOption` module for managing system options.
  - [A] Added `locale` to `SysUserOption` for user region/language settings.
  - [A] Added edit and delete protections for `SysRole` and `SysUser` so the system always retains an administrative user.
  - [F] Fixed the issue where `updated_at` was not refreshed when editing `SysRole`.
  - [C] Changed the response status code for missing resources (`404 page` / `uri_not_found`) from `200` to `404`.
- **2.1.1** `2024/05/01`
  - [A] Added configuration items to `config.py`:
    - `APP_PAGE_MAPPING` for page mapping
    - `APP_LICENSE_LOAD_INTERVAL` for the license reload interval
    - `APP_LICENSE_OS_TIME_BACKWARD_LIMIT` for the maximum allowed backward system time change
  - [A] Added operating system time checks to `LicenseManager`.
  - [C] Added the `_cli` directory for CLI-related logic.
  - [C] Added `app.utils._utils` for general-purpose helper functions.
  - [C] Changed frontend re-login to a modal dialog flow.
- **2.1.0** `2024/02/01`
  - [A] Added page internationalization for login, system navigation, and system management.
- **2.0.1** `2024/01/01`
  - [C] Removed `Signature/license` and related license details from license operation logs and query results.
  - [F] Changed `LicenseManager.get_license()` to return a copy to avoid accidental mutation.
- **2.0.0** `2023/12/01`
  - [A] Added support for Redis Sentinel URLs in `app._ext.redis_ws`.
  - [A] Added token refresh support and the `APP_REFRESH_TOKEN_EXPIRES_IN` configuration for refresh-token lifetime.
- **1.6** `2023/06/16`
  - [F] Fixed the `app._ext.redis_ws.init_websocket` app config issue. `2023/07/10`
  - [A] Added the `ignore_tables` option to `alembic.ini` to configure tables excluded from Alembic management. `2023/07/10`
  - [A] Added the `example` sample module. `2023/07/11`
  - [A] Added the `SysUserOption` table for storing user options such as login time and login count. `2023/07/20`
  - [F] Fixed frontend filtering in the `Action Logs` module. `2023/08/23`
  - [A] Added `FLASKZ_DATABASE_SESSION_KWARGS = {'expire_on_commit': False}`. `2023/09/01`
  - [A] Added Redis Sentinel support to `app._ext.redis_ws`. `2023/09/01`
  - [A] Removed `password` from `log_operation` when creating or updating users. `2023/09/15`
  - [C] Removed `nullable=False` from `SysUser.email` for AAA compatibility. `2023/09/18`
  - [F] Added `password is None` handling to `SysUser.verify_password`. `2023/09/18`
  - [A] Added the `auth` extension in `app._ext` for third-party authorization, including a TACACS implementation. `2023/09/20`
  - [A] Added the `echo` option to `alembic.ini` to control Alembic echo logging. `2023/09/21`
  - [A] Added `UnittestConfig` to `config.py` and `config.ini` for unit testing. `2023/09/27`
  - [A] Added `perf` and `unit` test suites under `tests`. `2023/09/27`
  - [A] Added the `swagger` extension in `app._ext` for Swagger documentation generation. `2023/10/26`
- **1.5** `2023/05/01`
  - [C] Refactored the RBAC [permission management](http://zhangyiheng.com/blog/articles/py_flaskz_admin.html#toc-rbac) module (`sys_mgmt`).
  - [C] Refactored the system management APIs according to the [development specification](http://zhangyiheng.com/blog/articles/dev_spec.html).
  - [A] Added Basic Auth [user authentication](http://zhangyiheng.com/blog/articles/py_flaskz_admin.html#toc-login). `2023/05/09`
