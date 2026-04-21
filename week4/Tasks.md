# Lab Activities: Module 4

---

## Walkthrough 1: Interactive Shells
1. Fire up a fresh instance of the Ubuntu environment and bind to its terminal: `docker run -it ubuntu bash`.
2. Notice the prompt changes. You are now inside the Linux container. Run `ls /` or `uname -a`.
3. Type `exit` to kill the terminal, which concurrently stops the container entirely.

---

## Walkthrough 2: Exposing Services
1. Fetch the official Apache image: `docker pull httpd`.
2. Construct and start a containerized web service. Route host port 8081 through to container port 80: `docker run -d -p 8081:80 --name web-sandbox httpd`.
3. Launch curl or a browser against `localhost:8081` to observe the default Apache landing page.

---

## Walkthrough 3: Testing Variable Injection
1. Run a container whose sole purpose is to dump its environment out to the console and cleanly exit. Pass a custom variable to it: 
   `docker run -e THEME_PREFERENCE=dark --name config-test ubuntu env`.
2. Examine the stdout dump; verify that `THEME_PREFERENCE=dark` successfully bridged into the container.