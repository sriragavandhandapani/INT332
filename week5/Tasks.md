# Lab Activities: Module 5

## Part 1: Sandbox Interactions
**Objective:** Confirm file capabilities and standard execution behavior.
1. Poke around the root filesystem non-interactively: `docker exec -it <id> ls /`
2. Drill into the environment: `docker exec -it <id> /bin/bash`
3. Generate content inside the container: `echo "Internal testing" > /tmp/flag.txt`
4. Jump out: `exit`
5. Extract your new file to the host system: `docker cp <id>:/tmp/flag.txt ~/Desktop/`
6. Inject a new file back: `docker cp ~/Desktop/flag.txt <id>:/tmp/flag2.txt`

## Part 2: Workflow Emulation
**Objective:** Create a multi-step project folder setup.
1. Force container to generate a working dir: `docker exec -it <id> mkdir /workspace`
2. Append logs externally: `docker exec -it <id> sh -c "echo 'Operation Alpha' > /workspace/log.txt"`
3. Append more logs: `docker exec -it <id> sh -c "echo 'Operation Beta' >> /workspace/log.txt"`
4. Verify from the outside: `docker exec -it <id> cat /workspace/log.txt`

## Part 3: Service Injection & Toggling
**Objective:** Evaluate container restarts.
1. Boot Nginx with variables: `docker run -e TIER=frontend nginx`
2. Monitor real-time performance: `docker logs -f <nginx-id>`
3. Restart the engine: `docker stop <nginx-id>` followed by `docker start <nginx-id>`

## Part 4: Foundational Volumes
**Objective:** Deploy and cleanup basic volume mounts.
1. Initialize storage: `docker volume create shared_blob`
2. Validate exists: `docker volume ls`
3. Attach it: `docker run -dit --name worker-node -v shared_blob:/mnt/blob ubuntu`
4. Confirm path exists internally: `docker exec -it worker-node ls /mnt/blob`
5. Clean sweep: delete container, then try `docker volume rm shared_blob`.

## Part 5: Proving Data Persistence
**Objective:** Demonstrate that container death does not equal data death.
1. Stand up the source: `docker volume create persistent_vol`
2. Start writer container: `docker run -dit --name writer -v persistent_vol:/data ubuntu`
3. Write state: `docker exec -it writer sh -c "echo 'Invaluable Data' > /data/state.txt"`
4. Annihilate the writer: `docker rm -f writer`
5. Start reader container: `docker run -dit --name reader -v persistent_vol:/data ubuntu`
6. Read the state: `docker exec -it reader cat /data/state.txt`. The output proves the data survived!

## Part 6: Overriding HTTPD
**Objective:** Override default web content using volume injection.
1. Cache HTTPD out-of-band: `docker pull httpd`
2. Allocate volume: `docker volume create web_data`
3. Establish the portal: `docker run -dit --name portal -p 8080:80 -v web_data:/usr/local/apache2/htdocs httpd`
4. Override the index page via exec: `docker exec -it portal bash -c "echo '<h1>Custom Override Live</h1>' > /usr/local/apache2/htdocs/index.html"`
5. Test against `http://localhost:8080`.
6. Terminate setup logically.
