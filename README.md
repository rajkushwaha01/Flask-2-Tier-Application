# Flask and MySQL Two-Tier Application Using Docker

## 📌 Project Overview

I developed this project to understand how a web application communicates with a database using Docker containers.

The application is built using **Python Flask** as the backend and **MySQL** as the database. Users can submit messages through a web interface, and the application stores those messages in the database.

I used Docker to containerize the application and MySQL database. I also configured container networking so that both containers can communicate with each other.

This project helped me gain practical experience with containerization, environment variables, database connectivity, Docker networking, and application deployment.

## 🎯 Project Objectives

* Containerize a Python Flask web application using Docker.
* Configure MySQL as the application's database.
* Establish communication between the Flask application and MySQL.
* Manage application configuration using environment variables.
* Use Docker Compose to manage multiple containers.
* Deploy the application using Docker containers without Docker Compose.
* Understand data persistence using Docker volumes.
* Learn the fundamentals of deploying a two-tier application.

## 🏗️ Project Architecture

The application follows a two-tier architecture consisting of the application layer and the database layer.

```text
              User / Web Browser
                      |
                      v
              Frontend Web Page
                      |
                      v
              Flask Application
                 Port 5000
                      |
                      v
              Docker Network
                  (twotier)
                      |
                      v
                MySQL Database
                  Port 3306
                      |
                      v
                Docker Volume
               (Persistent Data)
```

### Architecture Components

| Component             | Purpose                                                                           |
| --------------------- | --------------------------------------------------------------------------------- |
| Frontend              | Provides a web interface for submitting messages.                                 |
| Python Flask          | Handles application logic and database interactions.                              |
| MySQL                 | Stores messages submitted by users.                                               |
| Docker                | Packages the application and database into containers.                            |
| Docker Compose        | Manages the application and database containers together.                         |
| Docker Network        | Enables communication between the Flask and MySQL containers.                     |
| Docker Volume         | Persists MySQL data beyond the lifecycle of an individual container.              |
| Environment Variables | Configure the database connection without hardcoding settings in the application. |

## 🛠️ Technologies Used

* **Programming Language:** Python
* **Backend Framework:** Flask
* **Database:** MySQL
* **Containerization:** Docker
* **Container Orchestration:** Docker Compose
* **Networking:** Docker Bridge Network
* **Storage:** Docker Volumes
* **Version Control:** Git and GitHub
* **Configuration Management:** Environment Variables

## 📂 Project Structure

The repository should contain the following files, depending on the implementation:

```text
flask-mysql-docker/
│
├── app.py
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
├── .env.example
├── .gitignore
├── README.md
│
└── templates/
    └── index.html
```

**Note:** The structure above is illustrative. Keep the filenames and folders consistent with your actual project files.

### File Descriptions

* `app.py` — Contains the Flask application and database interaction logic.
* `Dockerfile` — Defines the instructions for building the Flask application image.
* `docker-compose.yml` — Defines and configures the application and MySQL services.
* `requirements.txt` — Lists the Python dependencies required by the application.
* `.env.example` — Provides an example of the environment variables required to configure the application.
* `.gitignore` — Prevents sensitive files and unnecessary files from being committed to GitHub.
* `templates/index.html` — Contains the frontend HTML template, if the application uses Flask templates.
* `README.md` — Documents the project, architecture, setup instructions, and commands.

## ⚙️ Prerequisites

Before running this project, ensure that you have installed:

* Docker
* Docker Compose
* Git (optional, for cloning the repository)

Basic knowledge of Python, SQL, and Docker commands is helpful.

## 🚀 How to Run the Project Using Docker Compose

### Step 1: Clone the Repository

Replace the example repository URL with your actual GitHub repository URL.

```bash
git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
```

Navigate to the project directory:

```bash
cd YOUR-REPOSITORY
```

### Step 2: Configure Environment Variables

Create a `.env` file in the project directory.

Example configuration:

```env
MYSQL_HOST=mysql
MYSQL_USER=root
MYSQL_PASSWORD=replace_with_a_local_password
MYSQL_DB=mydb
MYSQL_ROOT_PASSWORD=replace_with_a_local_root_password
```

**Important:** These variable names must match the configuration expected by your actual application and `docker-compose.yml`.

Do not upload your actual `.env` file or real database credentials to GitHub.

### Step 3: Build and Start the Containers

Run the following command:

```bash
docker compose up --build -d
```

This command builds the application image if required and starts the services in the background.

Check the running containers:

```bash
docker ps
```

Check the application logs:

```bash
docker compose logs -f
```

### Step 4: Access the Application

Open your browser and visit the appropriate URL based on the port mappings configured in your project.

* **Frontend:** `http://localhost`
* **Flask Backend:** `http://localhost:5000`

The frontend URL assumes that your project includes a frontend service or web server mapped to port 80.

### Step 5: Create the Messages Table

If your application does not create the table automatically, connect to MySQL and execute:

```sql
CREATE TABLE messages (
    id INT AUTO_INCREMENT PRIMARY KEY,
    message TEXT
);
```

Make sure you execute this command in the database configured for your application.

### Step 6: Test the Application

1. Open the frontend in your browser.
2. Enter a message in the form.
3. Submit the message.
4. Verify that the application stores the message in MySQL.
5. Check that the saved message appears on the frontend, if this functionality is implemented.

If your Flask application implements the `/insert_sql` route, you can also test it by visiting:

`http://localhost:5000/insert_sql`

The route's behavior depends on how it is implemented in your actual application.

## 🐳 Running the Application Without Docker Compose

I also explored how to run the application and database as separate containers using Docker commands.

This approach helps me understand Docker networking, container configuration, image building, and persistent storage.

### Step 1: Build the Flask Docker Image

Run this command from the directory containing your `Dockerfile`:

```bash
docker build -t flaskapp:latest .
```

### Step 2: Create a Docker Network

Create a custom Docker bridge network:

```bash
docker network create twotier
```

Both containers must be connected to this network to communicate using their container names.

### Step 3: Start the MySQL Container

```bash
docker run -d \
  --name mysql \
  --network twotier \
  -v mysql-data:/var/lib/mysql \
  -e MYSQL_DATABASE=mydb \
  -e MYSQL_ROOT_PASSWORD=replace_with_a_local_password \
  mysql:5.7
```

This command:

* Creates a MySQL container.
* Connects the container to the `twotier` network.
* Creates the `mydb` database on the first initialization of the data directory.
* Uses a Docker volume to persist database files.
* Configures the MySQL root password.

A separate host port mapping is not necessary for Flask-to-MySQL communication because both containers share the same Docker network.

### Step 4: Start the Flask Application Container

```bash
docker run -d \
  --name flaskapp \
  --network twotier \
  -e MYSQL_HOST=mysql \
  -e MYSQL_USER=root \
  -e MYSQL_PASSWORD=replace_with_a_local_password \
  -e MYSQL_DB=mydb \
  -p 5000:5000 \
  flaskapp:latest
```

The environment variables configure the Flask application's database connection.

The hostname `mysql` resolves to the MySQL container through Docker's custom network.

The `-p 5000:5000` option maps port 5000 on the host to port 5000 in the Flask container. The Flask application must listen on the appropriate interface and port for this mapping to work.

### Step 5: Verify the Containers

Check the running containers:

```bash
docker ps
```

View the Flask application logs:

```bash
docker logs flaskapp
```

View the MySQL logs:

```bash
docker logs mysql
```

Inspect the Docker network:

```bash
docker network inspect twotier
```

## 🗄️ Database Persistence

I used a Docker volume to persist MySQL data.

```bash
docker volume create mysql-data
```

The volume is mounted at:

```text
/var/lib/mysql
```

This helps preserve database files when the MySQL container is removed and recreated, provided the same volume is reused and the data directory is not deleted.

## 🧹 Stopping and Cleaning Up

To stop the services started with Docker Compose:

```bash
docker compose down
```

To stop and remove the manually created containers:

```bash
docker stop flaskapp mysql
docker rm flaskapp mysql
```

To remove the custom Docker network after removing its attached containers:

```bash
docker network rm twotier
```

**Warning:** Removing the Docker volume permanently deletes its stored database data.

Only remove it when you intentionally want to delete the database:

```bash
docker volume rm mysql-data
```

## 🔐 Security Considerations

While working on this project, I considered the importance of securing application configuration and database access.

Recommended improvements include:

* Store database credentials in environment variables rather than hardcoding them.
* Keep `.env` files out of version control.
* Use strong passwords and avoid default credentials.
* Use a dedicated MySQL application user instead of the root account.
* Avoid exposing the MySQL port publicly unless access is required.
* Validate user input and use parameterized SQL queries to reduce SQL injection risks.
* Use supported software versions and apply security updates.
* Add health checks and application error handling.
* Use a secrets-management solution for production deployments.

This project is intended for learning and demonstration. Additional hardening would be required before production use.

## 🧠 Key Learnings

Through this project, I gained practical experience with:

* Containerizing a Python Flask application.
* Running a MySQL database inside a Docker container.
* Connecting application and database containers through a Docker network.
* Configuring applications using environment variables.
* Managing multiple containers with Docker Compose.
* Building Docker images using a Dockerfile.
* Persisting database data using Docker volumes.
* Troubleshooting containers using Docker logs and network inspection.
* Understanding the fundamentals of two-tier application deployment.

## 🔮 Future Improvements

I plan to extend this project by exploring:

* Automating builds and deployments using Jenkins.
* Integrating GitHub with a CI/CD pipeline.
* Scanning Docker images for known vulnerabilities.
* Integrating code quality checks into the pipeline.
* Deploying the application on AWS.
* Exploring Kubernetes for container orchestration.
* Adding monitoring, logging, and application health checks.

These are potential improvements and should not be considered implemented unless they are actually configured in the repository.

## 👨‍💻 About Me

I am building hands-on projects to strengthen my knowledge of DevOps, cloud computing, containerization, and CI/CD.

This project is part of my practical learning journey with Docker, Git, Python applications, databases, and deployment workflows.

I am interested in opportunities where I can apply my technical skills, learn from experienced engineers, and contribute to real-world infrastructure and application deployment projects.

## ⭐ Feedback

If you find this project useful, feel free to explore the repository and share suggestions for improvement.
