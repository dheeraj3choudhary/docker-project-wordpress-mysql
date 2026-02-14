<div align="center">

# WordPress with MySQL - Docker Compose Setup

A beginner-friendly Docker Compose project that demonstrates multi-container orchestration by deploying WordPress with MySQL database. Perfect for DevOps and Cloud engineers learning Docker containerization and container networking.

<img width="4000" height="2250" alt="Wordpress_Arch_Diag" src="https://github.com/user-attachments/assets/73b44a27-2a56-4bf7-aca5-5f6eff806623" />

<a href="https://www.buymeacoffee.com/Dheeraj3" target="_blank">
  <img src="https://cdn.buymeacoffee.com/buttons/v2/default-blue.png" alt="Buy Me A Coffee" height="50">
</a>

## [Subscribe](https://www.youtube.com/@dheeraj-choudhary?sub_confirmation=1) to learn more About Artificial-Intellegence, Machine-Learning, Cloud & DevOps.

<p align="center">
<a href="https://www.linkedin.com/in/dheeraj-choudhary/" target="_blank">
  <img height="100" alt="Dheeraj Choudhary | LinkedIN"  src="https://user-images.githubusercontent.com/60597290/152035581-a7c6c0c3-65c3-4160-89c0-e90ddc1e8d4e.png"/>
</a> 

<a href="https://www.youtube.com/@dheeraj-choudhary?sub_confirmation=1">
    <img height="100" src="https://user-images.githubusercontent.com/60597290/152035929-b7f75d38-e1c2-4325-a97e-7b934b8534e2.png" />
</a>    
</p>
</div>

## 📚 Learning Objectives

By completing this project, you will learn:

- How to use Docker Compose to manage multi-container applications
- Container networking and service discovery (containers communicating by service name)
- Data persistence using Docker volumes
- Environment variable configuration for containerized applications
- Container dependency management and health checks
- Port mapping and exposing containerized applications
- Best practices for running web applications with databases in Docker

## 📋 Prerequisites

Before starting this project, ensure you have the following installed:

- **Docker Engine**: Version 20.10 or higher
  - Check version: `docker --version`
  - [Install Docker](https://docs.docker.com/engine/install/)

- **Docker Compose**: Version 2.0 or higher
  - Check version: `docker compose version`
  - Included with Docker Desktop, or [install separately](https://docs.docker.com/compose/install/)

- **Git**: For cloning the repository
  - Check version: `git --version`

- **System Requirements**:
  - Minimum 2GB RAM available for Docker
  - At least 2GB free disk space
  - Ports 8080 must be available on your host machine

## 🚀 Step-by-Step Instructions

### 1. Clone the Repository

```bash
git clone https://github.com/yourusername/docker-wordpress-mysql-compose.git
cd docker-wordpress-mysql-compose
```

### 2. Review the Configuration (Optional)

Open `docker-compose.yml` to understand the setup:
- **MySQL Service**: Database container with credentials
- **WordPress Service**: Application container connected to MySQL
- **Volumes**: Persistent storage for database and WordPress files
- **Network**: Custom bridge network for container communication

### 3. Start the Application

Run the following command to start both containers:

```bash
docker compose up -d
```

**What happens:**
- `-d` flag runs containers in detached mode (background)
- Docker pulls the `mysql:8.0` and `wordpress:latest` images (first time only)
- Creates two containers: `wordpress-mysql` and `wordpress-app`
- Creates named volumes: `db_data` and `wordpress_data`
- Creates a custom network: `wordpress-network`

### 4. Verify Containers are Running

Check container status:

```bash
docker compose ps
```

Expected output:
```
NAME                IMAGE               STATUS              PORTS
wordpress-app       wordpress:latest    Up                  0.0.0.0:8080->80/tcp
wordpress-mysql     mysql:8.0           Up (healthy)        3306/tcp
```

### 5. Access WordPress

Open your browser and navigate to:

```
http://localhost:8080
```

You should see the WordPress installation page.

### 6. Complete WordPress Setup

Follow the WordPress installation wizard:

1. Select your language
2. Fill in site information:
   - Site Title: Your choice
   - Username: Your choice (admin username)
   - Password: Create a strong password
   - Email: Your email address
3. Click "Install WordPress"
4. Log in with your credentials

### 7. View Container Logs (Optional)

To see what's happening inside containers:

```bash
# View all logs
docker compose logs

# View WordPress logs only
docker compose logs wordpress

# View MySQL logs only
docker compose logs db

# Follow logs in real-time
docker compose logs -f
```

### 8. Stop the Application

When you're done, stop the containers:

```bash
docker compose down
```

**Note:** This stops and removes containers but **keeps your data** (volumes persist).

### 9. Stop and Remove Everything (Including Data)

To completely remove containers, networks, AND volumes:

```bash
docker compose down -v
```

**Warning:** This deletes all WordPress content and database data!

## 📁 Directory Structure

```
docker-wordpress-mysql-compose/
│
├── docker-compose.yml          # Multi-container orchestration configuration
├── .dockerignore              # Files to exclude from Docker context
├── .env.example               # Environment variables template (optional)
├── .gitignore                 # Git ignore rules
└── README.md                  # This file
```

**Note:** No Dockerfile is needed because we're using official pre-built images from Docker Hub.

## 🔧 Troubleshooting

### Issue 1: Port 8080 Already in Use

**Error:**
```
Error response from daemon: driver failed programming external connectivity on endpoint wordpress-app: 
Bind for 0.0.0.0:8080 failed: port is already allocated
```

**Solution:**
- Check what's using port 8080: `lsof -i :8080` (Mac/Linux) or `netstat -ano | findstr :8080` (Windows)
- Stop the conflicting service or change the port in `docker-compose.yml`:
  ```yaml
  ports:
    - "8081:80"  # Change 8080 to 8081
  ```

### Issue 2: MySQL Container Keeps Restarting

**Error:** `wordpress-mysql` container status shows "Restarting"

**Solution:**
```bash
# Check MySQL logs for errors
docker compose logs db

# Common causes:
# - Not enough memory allocated to Docker
# - Corrupted volume data

# Reset by removing volumes:
docker compose down -v
docker compose up -d
```

### Issue 3: WordPress Cannot Connect to Database

**Error:** "Error establishing a database connection"

**Solution:**
```bash
# 1. Ensure MySQL container is healthy
docker compose ps

# 2. Check if MySQL is ready
docker compose exec db mysqladmin ping -h localhost -u root -prootpassword

# 3. Verify environment variables match in docker-compose.yml
# WORDPRESS_DB_NAME must match MYSQL_DATABASE
# WORDPRESS_DB_USER must match MYSQL_USER
# WORDPRESS_DB_PASSWORD must match MYSQL_PASSWORD

# 4. Restart containers
docker compose restart
```

### Issue 4: Permission Denied Errors

**Error:** Permission denied when accessing WordPress files

**Solution:**
```bash
# On Linux, ensure Docker has proper permissions
sudo usermod -aG docker $USER

# Log out and log back in, then try again
docker compose down
docker compose up -d
```

### Issue 5: Images Not Pulling

**Error:** "failed to pull image"

**Solution:**
```bash
# Check internet connectivity
ping docker.io

# Try pulling images manually
docker pull mysql:8.0
docker pull wordpress:latest

# Then start compose
docker compose up -d
```

### Issue 6: Containers Start but WordPress Shows White Screen

**Solution:**
```bash
# Clear browser cache and cookies
# Wait 30-60 seconds for WordPress to fully initialize
# Check WordPress logs:
docker compose logs wordpress

# Restart WordPress container
docker compose restart wordpress
```

## ✅ Validation Steps

### Verify Successful Deployment

Run through this checklist to confirm everything works:

1. **Check Container Status**
   ```bash
   docker compose ps
   ```
   ✅ Both containers should show "Up" status
   ✅ MySQL should show "(healthy)" status

2. **Check Networks**
   ```bash
   docker network ls | grep wordpress
   ```
   ✅ Should see `wordpress-network` network

3. **Check Volumes**
   ```bash
   docker volume ls | grep docker-wordpress
   ```
   ✅ Should see `db_data` and `wordpress_data` volumes

4. **Test WordPress Access**
   ```bash
   curl -I http://localhost:8080
   ```
   ✅ Should return HTTP 200 or 302 response

5. **Verify Database Connection**
   ```bash
   docker compose exec db mysql -u wordpress_user -pwordpress_password -e "SHOW DATABASES;"
   ```
   ✅ Should list `wordpress_db` database

6. **Check WordPress Installation**
   - Open http://localhost:8080 in browser
   ✅ Should see WordPress setup page or installed site
   
7. **Test Data Persistence**
   ```bash
   # Stop containers
   docker compose down
   
   # Start again
   docker compose up -d
   
   # Access http://localhost:8080
   ```
   ✅ WordPress site should retain all data (posts, pages, settings)

8. **Verify Container Communication**
   ```bash
   docker compose exec wordpress ping -c 3 db
   ```
   ✅ WordPress container should successfully ping MySQL container

### Expected Results

- WordPress site loads at `http://localhost:8080`
- You can log in to WordPress admin at `http://localhost:8080/wp-admin`
- You can create posts, pages, and upload media
- Data persists after stopping and restarting containers
- Containers can communicate with each other by service name

## 🎯 Key Concepts Demonstrated

This project showcases:

- **Multi-container orchestration**: Managing two interdependent services
- **Service discovery**: WordPress connects to MySQL using service name `db`
- **Data persistence**: Using named volumes to survive container restarts
- **Health checks**: Ensuring MySQL is ready before WordPress starts
- **Environment variables**: Configuring containers without hardcoding values
- **Port mapping**: Exposing containerized applications to the host
- **Custom networks**: Isolating container communication

## 🧹 Cleanup

When you're finished with the project:

```bash
# Stop containers (keeps data)
docker compose down

# Stop containers and remove volumes (deletes all data)
docker compose down -v

# Remove images (optional, saves disk space)
docker rmi wordpress:latest mysql:8.0
```

## 📚 Next Steps

After mastering this project, try:

- Adding phpMyAdmin for database management
- Using `.env` file for sensitive credentials
- Configuring WordPress with custom themes and plugins
- Setting up SSL/TLS with a reverse proxy (Nginx)
- Implementing backup strategies for volumes
- Deploying to a cloud provider (AWS, Azure, GCP)

## 🤝 Contributing

Feel free to submit issues and enhancement requests!

## 📄 License

This project is open source and available for educational purposes.

---

**Happy Learning! 🐳**
