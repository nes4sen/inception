# Developer Documentation

This document covers the technical setup, building processes, and architectural persistence of the Inception project.

## Setting up the Environment from Scratch

### 1. Prerequisites
Ensure the host machine is running a Linux distribution (e.g., Ubuntu/Debian) with `docker`, `docker compose`, and `make` installed.

### 2. Configuration Files
*   **Environment Variables:** Create a `.env` file inside the `srcs/` directory defining variables like `DOMAIN_NAME` and database names.

**Example `.env.example`:**
```env
# ===== General =====
LOGIN=your_login
DOMAIN_NAME=your_login.42.fr
# ===== MariaDB =====
MYSQL_DATABASE=your_database_name
MYSQL_USER=your_db_user
MYSQL_HOST=mariadb
# ===== WordPress site config =====
WP_TITLE=your_site_title
WP_ADMIN_USER=your_admin_username
WP_ADMIN_EMAIL=admin@yourdomain.com
WP_USER=your_wp_user
WP_USER_EMAIL=user@yourdomain.com
# ===== Paths =====
WP_DATA=/var/www/wordpress
DB_DATA=/var/lib/mysql
```
*   **Host Routing:** Modify the host's `/etc/hosts` file to append `127.0.0.1 nosahimi.42.fr` to allow local DNS resolution.

### 3. Secrets Management
Create a directory named `secrets` alongside the `srcs` directory. Populate it with the required text files:
*   `db_password.txt`
*   `db_root_password.txt`
*   `wp_admin_password.txt`
*   `wp_user_password.txt`

**How to create them:**
You can create these files from your terminal using the following commands (replace the placeholder text with your actual secure passwords):

```bash
mkdir ../secrets
echo "your_secure_db_password" > ../secrets/db_password.txt
echo "your_secure_db_root_password" > ../secrets/db_root_password.txt
echo "your_secure_wp_admin_password" > ../secrets/wp_admin_password.txt
echo "your_secure_wp_user_password" > ../secrets/wp_user_password.txt
```

## Building and Launching the Project
The root `Makefile` orchestrates the `docker-compose.yml` file located in `srcs/`.
*   **Build the images:** `make build`
*   **Launch containers in the background:** `make up`
*   **Restart the infrastructure:** `make restart`
*   **Deep Cleanup:** `make fclean` (stops containers, prunes the system, and forcibly deletes the local data directories).

## Managing Containers and Volumes
*   **Execute a shell in a running container:** `docker exec -it <container_name> /bin/sh` (e.g., `docker exec -it nginx /bin/sh`).
*   **View network details:** `docker network inspect inception_network`
*   **Inspect Volumes:** `docker volume ls` followed by `docker volume inspect <volume_name>`.

## Data Storage and Persistence
This project utilizes Docker volumes explicitly mapped to host directories to ensure data persists even if the containers are destroyed.
*   **Database Data (`db_data`):** Mapped to `/home/nosahimi/data/mariadb`. This stores all MariaDB tables and configurations.
*   **Web Files (`wp_data`):** Mapped to `/home/nosahimi/data/wordpress`. This stores the downloaded WordPress core, themes, plugins, and media uploads.
These directories are automatically created by the `make prepare` command prior to spinning up the containers.
