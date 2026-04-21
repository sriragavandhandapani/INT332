# Lab Activities: Module 6

---

## Challenge 1: Single Variable Injection
- Execute an interactive bash shell injecting a specific identifier:
  `docker run -it -e WORKSPACE=lab_env ubuntu bash`
- Inside the container, dump the variable to prove it exists:
  `echo $WORKSPACE`
- Validate and close.

---

## Challenge 2: Network Accessibility
- Test a blind run: `docker run nginx` (Fails to be accessible locally).
- Test a routed run: `docker run -p 8080:80 nginx` (Succeeds via `curl localhost:8080`).

---

## Challenge 3: Parameter Overloading
- Launch an instance with both network bindings and environment params:
  `docker run -it -p 8888:80 -e PROXY_PORT=8888 nginx`

---

## Challenge 4: Complete Static Deployment
- Obtain the base server: `docker pull httpd`
- Deploy to the background on local port 8080: `docker run -d --name apache-cluster -p 8080:80 httpd`
- Manipulate internal file logic dynamically: `docker exec -it apache-cluster bash -c "echo '<h1>Systems Operational.</h1>' > /usr/local/apache2/htdocs/index.html"`
- Validate visually via browser or curl.
- Clean the stage by stopping and tearing down the container.

---

## Challenge 5: Massive Execution Sequence
- Fire off a container leveraging almost all parameters:
  `docker run -it --name full_test -e DEPLOY=staging -v ~/data:/opt/data ubuntu bash`
- Prove the parameters held: `echo $DEPLOY`
- Test the filesystem mount: 
  `echo "Initiating protocol" > /opt/data/test.log`
  `echo "Sequence 2" >> /opt/data/test.log`
- Examine the combined output.

---

## Challenge 6: Stream Tailing
- Kick off a silent daemon: `docker run -d --name silent_server nginx`
- Manually poll its startup logs: `docker logs silent_server`
- Re-run and lock your terminal to follow the stream: `docker logs -f silent_server`
