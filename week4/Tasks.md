📝 Practical Tasks from Syllabus

Task #1: Basic Interaction
Run an Ubuntu container interactively: docker run -it ubuntu bash.
Execute basic Linux commands like ls or pwd inside the shell.
Exit to stop the container.



Task #2: Web Server Deployment
Pull the Apache image: docker pull httpd.
Run it with port mapping: docker run -d -p 8081:80 --name my-web httpd.
Verify: Access http://localhost:8081 in your browser.



Task #3: Environment Variable Practice
Run: docker run -e APP_COLOR=blue --name color-test ubuntu env.
Confirm: The output will list all variables, including APP_COLOR=blue.