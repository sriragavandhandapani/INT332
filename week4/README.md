

These commands help verify and inspect your Docker environment.

docker --version       # Shows installed Docker version
docker info            # Displays system-wide Docker information
docker history <image> # Shows image layer history

🐳 2. Image & Container Management
Manage Docker images and containers before running applications.
docker images          # List all local images
docker pull <image>    # Download image (e.g., docker pull httpd)

docker ps              # List running containers
docker ps -a           # List all containers (including stopped)



▶️ 3. The docker run Command & Options
Used to create and start a container.
🔹 Common Flags
-d        # Run container in detached (background) mode
-p        # Port mapping (host:container)
--name    # Assign custom container name
-it       # Interactive terminal mode
🔹 Example
docker run -d -p 80:80 --name my_container nginx



🌍 4. Environment Variables
Used to configure applications dynamically.
🔹 Methods to Set Variables
1. Single Variable
docker run -e MY_VAR=value ubuntu
2. Multiple Variables
docker run -e VAR1=value1 -e VAR2=value2 ubuntu
3. Pass from Host
docker run -e HOST_VAR ubuntu
4. Using Environment File (Best Practice)

Create a .env file:
APP_ENV=production
DB_HOST=localhost

Run container with:
docker run --env-file .env ubuntu



🔄 5. Container Lifecycle & Interaction
🔹 Control Commands
docker start <container_id>    # Start stopped container
docker stop <container_id>     # Stop running container
docker restart <container_id>  # Restart container
docker pause <container_id>    # Pause container processes



🔧 Troubleshooting & Cleanup
docker exec -it <container_id> bash  # Enter running container
docker rm <container_id>             # Remove container
docker rmi <image_id>                # Remove image