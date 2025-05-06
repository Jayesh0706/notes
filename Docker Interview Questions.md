Thanks! I’ll gather a tailored set of Docker and Kubernetes interview questions and detailed preparation notes specifically designed for a DevOps Engineer with around 1 year of experience.

The content will include separate sections for Docker and Kubernetes, covering basic to intermediate concepts, hands-on commands, common interview scenarios, and real-world troubleshooting use cases. I’ll let you know as soon as it’s ready.

# Docker and Kubernetes DevOps Interview Preparation Guide (1 Year Experience)

## Docker

### Interview Questions (Basic to Intermediate)

**Q1.** _What is Docker and how is it different from a virtual machine?_  
**A1.** Docker is an open-source containerization platform that packages applications into **containers** – standardized units containing your code and its dependencies. This ensures the app runs the same regardless of environment. Unlike a full **virtual machine** that emulates hardware and includes a full guest OS, Docker containers share the host OS kernel and isolate the application at the process level. This makes containers much lighter-weight, faster to start, and more resource-efficient than VMs. (In essence, VMs virtualize an entire machine, while Docker provides isolated user-space instances on the host OS.)

**Q2.** _What is a Docker image and how is it different from a container?_  
**A2.** A **Docker image** is a lightweight, standalone package that includes everything needed to run an application: the code, runtime, system tools, libraries, etc.. An image is essentially a **template** for creating containers – it’s a read-only snapshot built from a series of layers (each layer usually comes from a Dockerfile instruction). A **container** is a running instance of an image. When you start a container from an image, Docker adds a writable layer on top of the image layers; the container is the actual process (or set of processes) executing the application inside this isolated environment. You can have multiple containers (instances) all created from the same image, each with its own isolated runtime.

**Q3.** _What is a Dockerfile? Can you describe a simple Dockerfile and how you would build an image from it?_  
**A3.** A **Dockerfile** is a text file that contains a series of instructions on how to assemble a Docker image. It’s basically a recipe for Docker to build your image. For example, a simple Dockerfile for a Python app might look like:

```
FROM python:3.9-slim  
WORKDIR /app  
COPY requirements.txt .  
RUN pip install -r requirements.txt  
COPY . .  
CMD ["python", "app.py"]  
```

Each instruction (like `FROM`, `COPY`, `RUN`, `CMD`) creates a new image layer. To build an image from this file, you’d navigate to the Dockerfile’s directory and run a build command, e.g. `docker build -t myapp:1.0 .`. Docker then executes the Dockerfile instructions to produce an image (the `-t` flag tags the image with a name:tag). **Docker images are built from a Dockerfile** using the Docker engine, which runs the specified commands to assemble the image layers. Once built, you can run a container from the image with `docker run myapp:1.0`.

**Q4.** _What are the main components of Docker’s architecture (e.g., Docker Daemon, Docker client, and registry)?_  
**A4.** Docker uses a **client-server architecture**. The Docker **daemon** (sometimes called `dockerd`) runs in the background on the host machine and does the heavy lifting of building, running, and managing containers and images. The Docker **client** (`docker` CLI) is what you use to issue Docker commands – it communicates with the daemon through a REST API (over a UNIX socket or network). For example, when you run `docker build` or `docker run`, the client sends those instructions to the daemon, which executes them. Docker also uses **registries** to store images. A **Docker registry** (like Docker Hub or a private registry) holds Docker images in repositories. The daemon interacts with registries to pull images (when you `docker run` something not present locally) and to push images (with `docker push`) for sharing. In summary: the Docker daemon = server that builds/runs containers; Docker CLI = client to send commands; Docker registry = image storage and distribution system.

**Q5.** _How do you run a container in Docker? Give an example of a typical command to start a container._  
**A5.** The basic command to start a container is `docker run`. You need to specify an image and can provide options for runtime configuration. For example, a typical command might be:

```bash
docker run -d -p 8080:80 --name myweb nginx:latest
```

This command does the following: `-d` runs the container in detached mode (in the background), `-p 8080:80` maps port 80 in the container to port 8080 on the host (so you can access the nginx web server via [http://localhost:8080](http://localhost:8080/)), `--name myweb` gives the container a friendly name, and `nginx:latest` specifies the image (nginx, latest tag). Docker will pull the image from Docker Hub if it’s not already downloaded. After running this, the nginx web server is up in a container. You could also run containers interactively (for example, `docker run -it ubuntu:latest /bin/bash` to get a shell inside an Ubuntu container). In general, `docker run [options] <image> [command]` is how you launch containers, with options to configure things like networking, volumes, environment variables, etc.

**Q6.** _How do you list running containers, and how can you stop or remove a container?_  
**A6.** To list running containers, use the command `docker ps`. This will show container IDs, names, statuses, ports, etc., for all currently running containers. (Add `-a` as ==`docker ps -a` to list **all** containers==, including stopped ones.) To stop a running container, use `docker stop <container>` – this sends a graceful termination signal (SIGTERM) to the main process in the container, allowing it to shut down properly. For example: `docker stop myweb` will stop the **myweb** container. To **remove** a container, first ensure it’s stopped, then use `docker rm <container>` (e.g., `docker rm myweb`). This deletes the container and frees up its resources. (You can combine steps with `docker rm -f <container>` which forces a stop and removal in one go.) Similarly, to remove an image you no longer need, use `docker rmi <image>:<tag>`. In practice, lifecycle management involves commands like `docker start` (to start an existing stopped container), `docker restart` (to reboot a container), `docker pause`/`unpause` (to suspend processes), and so on, but **ps/stop/rm** are the most common for basic control of containers.

**Q7.** _What is the difference between `docker stop` and `docker kill`? When would you use each?_  
**A7.** Both commands will halt a running container, but the method differs:

- `docker stop` tries to **gracefully stop** a container. It sends a SIGTERM to the process, giving the application a chance to clean up (for example, finish requests or close files). Docker will wait up to 10 seconds by default. If the container hasn’t exited by then, Docker sends SIGKILL to force termination. So `docker stop` is like politely asking the container to exit, then forcing it if it doesn’t comply within the timeout.
    
- `docker kill` on the other hand **immediately kills** the container process by sending a SIGKILL (by default). This is a forcible shutdown with no grace period. It’s like pulling the plug. You might use `docker kill` if a container is hung and not responding to stop, or you need it terminated right away.
    

In summary, **stop is graceful** (SIGTERM, then SIGKILL as fallback) and **kill is abrupt** (SIGKILL straight away). In practice, you generally use `docker stop` to shut down containers in a clean way, and only use `docker kill` if stop isn’t working or you need an immediate halt.

**Q8.** _How do you expose or map ports in Docker? What does the `-p 8080:80` option do, and how is it different from the Dockerfile `EXPOSE` instruction?_  
**A8.** In Docker, **containers by default are isolated** and their internal ports aren’t accessible from the host. To allow access, you map container ports to host ports. The `-p` (publish) flag on `docker run` does this port mapping. For example, `-p 8080:80` maps port 80 in the container to port 8080 on the host machine. That means if a web server inside the container is listening on port 80, you can reach it at **localhost:8080** on the host. You can specify multiple `-p` flags for different ports as needed.

The Dockerfile `EXPOSE` instruction, on the other hand, **does not actually publish the port** to the host – it’s more of documentation (and a hint to Docker) about which port the container will listen on. `EXPOSE 80` in a Dockerfile tells anyone reading it (and Docker itself) that the container expects to use port 80, but by itself it doesn’t make it accessible. To actually expose it externally, you still need to use `-p` (or `-P` to publish all exposed ports to random host ports). In short, use `-p` (publish) at runtime to map container ports to host ports; `EXPOSE` is a declaration in the image that has no effect on host networking without an explicit port mapping.

**Q9.** _How can you persist data generated by a Docker container? Explain volumes vs. bind mounts._  
**A9.** By default, data created inside a container is stored in the container’s writable layer, which is ephemeral – it goes away when the container is removed. To **persist data**, Docker provides **volumes** and **bind mounts** as mounting mechanisms:

- A ==**volume** is managed by Docker==. When you use a volume (e.g., with `docker volume create` or by using `-v volume_name:/path`), ==Docker creates a directory on the host ==(in Docker’s storage area, typically under ==`/var/lib/docker/volumes/`==) and mounts it into the container at the specified path. Docker ==**volumes** are the recommended way to persist data== because Docker handles them, they ==are isolated from the core host filesystem==, and they can be easily backed up or migrated. ==For example, `-v mydata:/var/lib/mysql` in a MySQL container will store the database files in a Docker volume named “mydata”.==
    
- A **bind mount** ==maps a directory or file from the host filesystem into the container==. For example, `-v /home/user/app/config:/app/config` will mount the local folder into the container. ==Bind mounts allow you to directly use host files== (good for dev environments or when the container needs to read/write specific host paths).
    

**Key difference:** ==With a bind mount, you specify an existing path on the host to share with the container; with a volume, Docker creates and manages the storage location for you==. Volumes are decoupled from the host’s directory structure, whereas bind mounts are tightly coupled to it. In practice, volumes are used for persistent application data (databases, etc.) in production, while bind mounts are often used in development (e.g., mounting source code into a container) or when you explicitly need to access host files.

**Q10.** _How can you pass configuration or environment variables into a Docker container, without modifying the image?_  
**A10.** Docker allows you to inject environment variables at container run time using the `-e` or `--env` flag with `docker run`. For example: ==`docker run -e APP_ENV=production -e DB_HOST=db.example.com myimage:1.0`==. This passes `APP_ENV` and `DB_HOST` into the container’s environment. You can also use ==`--env-file`== to specify a file containing ==multiple env vars==. These methods don’t require rebuilding the image – you’re parameterizing the container startup.

Another approach is to use **Docker Compose** or Kubernetes (when running containers in those contexts) to define environment variables in a YAML file. But sticking to plain Docker: environment variables defined at run-time override any defaults that might be set via an `ENV` instruction in the Dockerfile. This is a common way to configure containers (for example, providing database connection strings, feature flags, etc.). Besides env vars, you can also use **volumes or bind mounts** to inject config files (for example, mount a config file from host into the container). This decouples configuration from the image, adhering to the 12-factor app principles. In summary, ==use `docker run -e VAR=value` (or `--env-file`) to pass configs/secrets into containers at runtime, instead of baking them into the image.==

**Q11.** _What is Docker Compose and how is it different from using `docker run`? When would you use Docker Compose?_  
**A11.** ==**Docker Compose** is a tool that helps you define and run multi-container Docker applications==. ==Instead of starting containers one by one with separate `docker run` commands, you describe your application’s services in a YAML file (typically `docker-compose.yml`)==. In that file, you can specify multiple services (containers), their images, configurations (environment variables, volumes, ports, dependencies, networks, etc.), and then ==start everything with a single command `docker-compose up`. Compose will create a custom network and link the containers, manage the startup order if needed (via `depends_on`), and allow all defined services to be spun up (or torn down) together.==

The difference from plain `docker run` is that Compose is orchestration on a single host for multiple containers. For example, ==if you have a web app container and a database container, Compose can start both, ensure the web app can talk to the DB by hostname, and apply all the settings from the YAML ==(instead of you typing a long `docker run` for each). It’s especially useful in development and testing environments for working with complex apps. ==You would use Docker Compose whenever you have more than one container that needs to work together== (e.g., a **LAMP stack** with separate containers for Apache, MySQL, PHP; or a microservice architecture in development). It simplifies managing relationships and shared configuration. In production, one might use Kubernetes or Docker Swarm for orchestration, but for a single host and simpler setups, Docker Compose is a convenient choice.

**Q12.** _In a Dockerfile, what is the difference between the `CMD` and `ENTRYPOINT` instructions?_  
**A12.** Both `CMD` and `ENTRYPOINT` ==define what command to run when a container starts==, but they serve different purposes:

- ==**ENTRYPOINT** specifies the _main command_ that always runs for the container==. Think of it as the program that will be executed. For example, an entrypoint could be a script or binary that your container should run.
    
- ==**CMD** provides _default arguments_ to that entrypoint, or a fallback command if no other command is provided.==
    

==In practical terms, if you define an ENTRYPOINT (in exec form, e.g., `ENTRYPOINT ["python", "app.py"]`), that command is fixed – when the container starts, it will always run `python app.py`. If you also have `CMD ["--port", "8000"]`, those would be arguments passed to `app.py` by default.== If a user runs `docker run image <other args>`, those args override the CMD (not the entrypoint). If the user wants to override the entrypoint itself, they have to use `--entrypoint` flag in `docker run`. A succinct way to remember: ==**ENTRYPOINT is the command, CMD is the default parameters**==. ==If you only use CMD (with no entrypoint), then CMD is the default command== (which can be overridden by specifying a command in `docker run`). ==If you use both, ENTRYPOINT + CMD, then by default container runs ENTRYPOINT + CMD==.

#veryImp 
==Example: In an _Ubuntu_ image, if you set `ENTRYPOINT ["ping"]` and `CMD ["localhost"]`, running the container with no arguments will execute `ping localhost`. If you run `docker run image 8.8.8.8`, it will execute `ping 8.8.8.8` (CMD got overridden by the argument). Without an entrypoint, if Dockerfile had just `CMD ["ping", "localhost"]`, you could override the whole command by providing a new command. So, use ENTRYPOINT when you want a container to _always_ run a specific program, and use CMD to provide defaults (which can be overridden). Often ENTRYPOINT is used for the main application, and CMD for user-customizable options.==

**Q13.** _What is the difference between the `ADD` and `COPY` instructions in a Dockerfile? When might you use one over the other?_  
**A13.** Both `ADD` and `COPY` place files into your image, but `ADD` has some extra capabilities:

- ==**COPY** is straightforward – it copies files/directories from the build context== (your host machine directory from which you build) into the image. It only works with local files. For example, `COPY src/ /app/src/` will take the `src` folder on your host (in context) and put it into `/app/src` in the image. **It does not handle URLs or archives** – it’s literally just copying files.
    
- **ADD** does everything COPY does _and more_. ==ADD can accept a URL instead of a local file – in which case Docker will download the content from the URL into the image. ADD also can handle compressed tar archives: ==if you ADD a local `.tar` file, it will automatically **extract** the archive into the image (at the destination path).
    

These extra features can be convenient but also can introduce unintended behavior (like accidentally extracting an archive when you just wanted to copy it). Because of that, **best practice is to use COPY by default**, and ==only use ADD if you specifically need the archive auto-extraction or URL-fetching capability==. For example, use ADD if you want to grab a file from the internet during build or unpack a tarball as part of image assembly; otherwise, stick to COPY for predictability. (COPY is also a bit more transparent – with ADD, someone reading the Dockerfile has to remember these extra behaviors.)

**Q14.** _How does Docker’s image build cache work? Why is the order of instructions in a Dockerfile important?_  
**A14.** Docker builds images in layers, and it caches those layers to speed up subsequent builds. ==Each instruction in the Dockerfile (FROM, RUN, COPY, etc.) creates a layer==. When you rebuild an image, Docker will go through the Dockerfile instructions in order and check if it can reuse a cached layer for each step. **==If nothing changed in a given instruction _and_ all previous layers are the same as before, Docker will reuse the cached layer instead of executing that step again**==. The build cache is keyed by the instruction plus the context (files) it depends on.

Because of this, the ==**order of instructions matters a lot**==. ==If you make a change early in the Dockerfile (say the first `RUN` command), then all subsequent layers’ cache become invalid – Docker has to rebuild everything after that change.== Conversely, ==if a later instruction changes but earlier ones remain the same, Docker will reuse the early layers from cache==. Optimizing build cache usage means structuring your Dockerfile from the least frequently changing parts to the most frequently changing. For example, you might `COPY package.json` and run `npm install` early (so that dependency installation is cached and won’t rerun if only application code changes), and then `COPY . .` the rest of the source code later. This way, if you edit application source code but don’t change dependencies, Docker will reuse the cached layers for base image and dependencies, and only rebuild the last layer(s) with your app code. If you had done the opposite (copy all source, then install dependencies), any change in source would bust the cache and force a reinstall of dependencies, making builds slower.

In short, ==Docker’s layer caching makes builds faster by reusing unchanged layers, and ordering instructions properly== (e.g., static/install steps before dynamic code) helps maximize cache hits. It’s a ==key Dockerfile best practice to be mindful of instruction order to avoid unnecessary rebuilds==.

**Q15.** _How do you push a Docker image to a registry, and what is Docker Hub?_  
**A15.** To push an image, you need to have it tagged in a way that corresponds to a repository in a registry. Docker Hub is the default public registry (hosted by Docker) where many images are stored, though you can use others (e.g., Google Container Registry, AWS ECR, etc.). The process:

1. **Tag the image** with the registry/repository name. If it’s Docker Hub and your username is (say) `alice`, and you want a repo `myapp`, you’d tag your image as `alice/myapp:tag`. For example: `docker tag myapp:1.0 alice/myapp:1.0`.
    
2. **Log in** to the registry (if it’s private or Docker Hub with your account) using `docker login`. This will prompt for credentials (not needed for public pulls but needed for push or private repos).
    
3. **Push the image** using `docker push alice/myapp:1.0`. Docker will upload the image layers to the registry repository. Once completed, the image is stored on the registry.
    

If using Docker Hub, the image is now available in the `alice/myapp` repository on Hub (and if it’s public, anyone can pull it with `docker pull alice/myapp:1.0`). Docker Hub is basically a cloud registry for Docker images – think of it as GitHub for Docker images. It hosts official images (like `nginx`, `redis` etc.) and user images. By default, `docker pull ubuntu` or `docker push alice/myapp` will go to Docker Hub unless you specify a different registry in the image name (like `myregistry.com/alice/myapp`). In summary: tag appropriately, login, and push. Then others (or your deployment environment) can pull the image from the registry by that name.

**Q16.** _A container you ran is crashing or not behaving as expected. What steps would you take to troubleshoot it?_  
**A16.** Troubleshooting a misbehaving container involves a few steps:

- **Check the container’s logs:** Run ==`docker logs <container>`==to see the stdout/stderr output from the container’s main process. This often shows error messages or stack traces explaining why the application failed. You can use ==`docker logs -f <container>` to follow logs in real time==, or `docker logs --tail=N` to see the last N lines. If the container has multiple services or you need more detail, ensure the app inside is logging properly to stdout so Docker can capture it.
    
- **Examine the container’s state and config:** Use `docker ps -a` to see if the container exited with a certain exit code. ==Use `docker inspect <container>` to dump low-level info, including environment variables, mount points, entrypoint/command, etc.==, to verify it has the correct configuration. Sometimes a mis-set environment variable or missing file volume can cause crashes.
    
- **Run the container in an interactive mode for debugging:** For example, if a container exits immediately, you can try to run it with an overridden entrypoint to drop into a shell. E.g., `docker run -it --entrypoint /bin/bash myimage` – this gives you a shell inside the container’s filesystem to poke around, see if files are present, run the app manually and see errors.
    
- **Check for common issues:** If the container **crashed immediately** (e.g., status is “Exited (1)”), likely the application inside encountered an error and quit. The logs will show it. If the container is in a **CrashLoopBackOff** pattern (restarting repeatedly), it means it keeps failing quickly after start – again logs are vital, or run it without `-d` (detached) to watch it live. If the container is **stuck** (e.g., not responding), maybe the process hung – in that case you might connect with `docker exec -it <container> bash` to get a shell inside (if the container OS has a shell) and investigate running processes.
    
- **Network or connectivity issues:** If the symptom is “container is running but I can’t access it,” ensure you published the port (`-p` flag) and that the service inside is listening on the correct interface (in container, typically it should listen on all interfaces or 0.0.0.0, not localhost only). Also check host firewall if any.
    
- **Resource constraints:** Sometimes containers exit due to OOM (out-of-memory) if they hit Docker’s memory limits or host limits – check `docker inspect` for OOMKilled flag or see `dmesg` on host for OOM. If using memory/cpu limits via Docker run options, consider if the app needs more.
    
- In summary, start with logs and `docker inspect`. Re-run or `exec` into the container for interactive debugging if needed. Verify that you launched it with the expected configuration (ports, env vars, volumes). These steps usually pinpoint the issue (application error vs misconfiguration vs environment issue).
    

---

### Docker Key Concepts and Use Cases

- **Containers vs. VMs:** ==Containers provide process-level isolation using the host OS kernel==, whereas ==virtual machines provide hardware-level isolation with separate OS per VM==. ==Containers are lightweight and start quickly== (seconds), making them ideal for packaging microservices and running multiple isolated applications on the same host with minimal overhead. VMs, while stronger in isolation, are heavier (each includes an entire OS). In DevOps, containers help achieve environment consistency (“it works on my machine” issues are reduced) since the container bundles the app and its dependencies. You can run many more container instances on a host than VMs, which is great for scaling out applications in resource-efficient ways.
    
- **Images and Layers:** A Docker **image** is a read-only template composed of layers. Each layer represents a stage in the image’s Dockerfile (for example, one layer for the base OS, another for application files, etc.). Layers are cached and reused: multiple images can share the same base layers (saving space). An image is essentially the application packaged with all prerequisites except the kernel. When you run an image, Docker adds a writable layer on top, creating a **container**. Changes in the running container (new files, modified files) stay in this writable layer, which vanishes if the container is removed (unless you use volumes). This copy-on-write mechanism and layering is why building images in the right order matters for caching.
    
- **Docker Engine Architecture:** Docker uses a **client-server model**. The **Docker daemon** runs on the host machine, managing images, containers, networks, and volumes. You typically interact with it via the **Docker CLI client** (or other tools that speak Docker’s API). For example, running `docker run` from the terminal sends an API call to the daemon, which then allocates resources and starts the container. There’s also a low-level container runtime (containerd/runc under the hood in modern Docker) that the daemon uses to actually interface with Linux kernel features (cgroups, namespaces). For most users, the daemon is the all-in-one service doing everything. Docker registries (like Docker Hub) are remote stores for images – the daemon will pull images from a registry or push images to it as commanded. This architecture allows you to have remote Docker daemons (you can point your client at a remote host’s Docker and manage containers there).
    
- **Docker Hub and Registries:** Docker Hub is the default public registry where you can find millions of images (official images like Ubuntu, Nginx, etc., as well as images published by other users or organizations). A **registry** is essentially a repository server for images (Hub is a public one; companies often use private registries for their images). When you `docker pull ubuntu`, it pulls from Docker Hub’s `library/ubuntu` image by default. Continuous integration pipelines often push built images to a registry, and deployment systems pull from there. Understanding registries is key for real-world use: they enable sharing and deploying containers easily across environments.
    
- **Use Cases for Docker:** Docker is heavily used to containerize applications for **development, testing, and deployment**:
    
    - In **development**, developers run their app in Docker to ensure a consistent environment (e.g., running a Python app in the same container image across all dev machines). Docker Compose is used to spin up a dev stack with databases, caches, etc., with one command.
        
    - For **testing/CI**, Docker allows tests to run in ephemeral containers that mimic production environment, and you can throw them away after. It’s easy to bring up dependencies (like using a MySQL container for integration tests).
        
    - In **deployment**, ==Docker images serve as a unit of release. Instead of “deploying code” you deploy a container image==. This ==makes rollbacks easy (just redeploy an older image)== and helps with scalability (multiple identical containers behind a load balancer).
        
    - **Microservices** architecture hugely benefits from Docker – ==each microservice can be packaged into its own image with its specific runtime and library versions==. This decouples services and avoids conflicts.
        
    - Docker is also used for ==**environment isolation**==: e.g., ==running multiple applications on the same host without them stepping on each other’s dependencies or requiring different versions of software==. Containers isolate them.
        
    - It’s common in **continuous delivery** to build a Docker image for each version of the application and push to a registry; then various environments (QA, Prod) pull the image. This ensures the artifact running in prod is the exact one tested in QA.
        
    - **Portability**: because ==Docker abstracts away OS differences==, you can run the same container on Linux, Mac (via Docker Desktop’s VM), or Windows (if image supports Windows), or on any cloud VM that has Docker. This makes migrating workloads or switching environments much simpler.
        
- **Containers in Production:** In real-world use, you often have many containers to manage. That’s where orchestrators like Kubernetes or Docker Swarm come in (to schedule containers on clusters, handle scaling, etc.). Even with Kubernetes, understanding Docker is important because Kubernetes uses container images and runtimes under the hood. With a single host, Docker by itself (or with Compose) might be enough, but at scale, an orchestrator helps manage dozens of containers across multiple servers.
    
- **Docker vs. Other Container Runtimes:** Docker was the pioneer, but you might hear about alternatives like Podman, CRI-O, etc. Many of these adhere to the same Open Container Initiative (OCI) standards. With one year of experience, it’s good to know Docker’s architecture, but also be aware that Kubernetes doesn’t actually require Docker specifically (since late 2020 Kubernetes uses CRI-compatible runtimes like containerd). Still, Docker remains a prevalent tool for building images and local development.
    

### Common Docker Commands

Docker has a rich CLI, but here are some of the most common and essential commands you should be comfortable with (as a one-year DevOps engineer, you’re likely running these daily):

- **Image Management:**
    
    - `docker build -t <name>:<tag> .` – Build an image from a Dockerfile in the current directory. `-t` tags the image (e.g., `myapp:1.0`).
        
    - `docker images` – List local images you have.
        
    - `docker pull <image>:<tag>` – Pull an image from a registry (defaults to Docker Hub).
        
    - `docker push <image>:<tag>` – Push an image to a registry (after tagging and login).
        
    - `docker rmi <image>` – Remove an image (you might add `-f` to force remove if containers exist using it).
        
- **Container Lifecycle:**
    
    - `docker run [options] <image> [command]` – Run a new container from an image. Important options:
        
        - `--name <name>` to name the container (otherwise Docker gives a random name).
            
        - `-d` to run in detached mode (in background).
            
        - `-it` to run interactively with a TTY (for running containers you want a shell in).
            
        - `-p <hostPort>:<containerPort>` to publish a container’s port to the host.
            
        - `-v <hostPath>:<containerPath>` to mount a host directory (bind mount) or `<volumeName>:<path>` to use a Docker volume.
            
        - `-e VAR=value` to set environment variables.
            
        - `--network <network>` to connect to a specific Docker network (else default bridge).
            
    - `docker ps` – List running containers (add `-a` to show all, including stopped).
        
    - `docker stop <container>` – Gracefully stop a running container (sends SIGTERM, with SIGKILL after timeout).
        
    - `docker kill <container>` – Abruptly stop a container (sends SIGKILL immediately).
        
    - `docker start <container>` – Start a container that was stopped (e.g., after `docker stop`).
        
    - `docker restart <container>` – Stop and start container (useful to pick up new env vars or configs if they changed).
        
    - `docker rm <container>` – Remove a stopped container (you can’t rm a running one without `-f`). It frees up the name and resources.
        
    - `docker pause <container>` / `docker unpause <container>` – Pause/unpause processes in a container (less common, but good for temporarily freezing a container’s execution).
        
- **Inspecting and Debugging:**
    
    - `docker logs <container>` – Fetch the logs of a container. ==Add `-f` to follow (stream) the logs==, similar to `tail -f`. Add `--tail 100` to see last 100 lines, etc.
        
    - `docker inspect <container or image>` – ==Return low-level JSON data about the object. For containers, this includes configuration, networking info (IP addresses), volume mounts, environment variables, and the container’s exit code/status==. You can add `-f` with a Go template to extract specific fields, but initially, just piping it to a JSON viewer or searching within is fine.
        
    - `docker exec -it <container> <command>` – Run a command inside a running container. Commonly used as `docker exec -it <container> bash` (if the container has Bash or sh) to get an interactive shell inside the container for debugging. You can also run one-off commands like `docker exec mydb pg_dump ...` to execute something inside.
        
    - ==`docker top <container>` – Show processes running inside the container (like a ps for that container’s namespace).==
        
    - `docker stats` – Live performance stats (CPU, memory, network) for containers (container ID, CPU%, mem usage, etc.), helpful for monitoring resource usage.
        
    - `docker diff <container>` – Show changes to filesystem since container started (which files were added/modified), can be insightful for debugging but not frequently used in everyday work.
        
- **Volumes and Networks:**
    
    - `docker volume create <name>` – Pre-create a volume (though you can also create implicitly by `docker run -v name:path`).
        
    - `docker volume ls` – List volumes, `docker volume inspect` to see details.
        
    - `docker network create <name>` – Create a user-defined network. By default, Docker has a `bridge` network that all containers join if not specified. User-defined bridges enable containers to communicate by name (Docker sets up DNS for containers on the same network).
        
    - `docker network ls` – list networks. `docker network inspect <name>` to see connected containers, subnet info, etc.
        
    - Typically, for simple setups, you might not explicitly create networks – but if you use Docker Compose, it usually makes one for your app.
        
- **Docker Compose:** (though it’s a separate tool, it’s part of common Docker toolkit)
    
    - `docker-compose up -d` – Start all services defined in `docker-compose.yml` (with `-d` to run in background).
        
    - `docker-compose down` – Stop and remove those containers (and networks by default).
        
    - `docker-compose logs -f [service]` – Show logs for all or one service.
        
    - `docker-compose exec <service> <command>` – Like `docker exec` but references the container by service name.
        
    - In newer Docker, you can also use `docker compose up` (as a subcommand of docker, note the space, not hyphen) if Docker Compose v2 is integrated.
        
- **Prune/Cleanup:**
    
    - `docker system prune` – Remove all stopped containers, unused networks, dangling images (one-liner cleanup; add `-a` to remove unused images too, not just dangling). Good for recovering disk space in dev environments.
        
    - `docker container prune`, `docker volume prune`, etc., exist for specific resource types.
        

Knowing these commands and options is critical. As a DevOps engineer, you’ll frequently run containers with various flags (networking, volumes, env). One year in, you should be comfortable running containers, exec-ing into them, viewing logs, and managing their lifecycle.

### Writing Dockerfiles: Best Practices

When creating Dockerfiles, following best practices will lead to smaller, more efficient, and more secure images:

- **Choose a minimal base image:** ==Use the smallest base image that makes sense for your app==. For example, `alpine` Linux images are very small (good for simple Go or C apps). If using Python or Node, use slim variants (e.g., `python:3.9-slim` instead of full `python:3.9`). Smaller base images reduce your attack surface and speed up pulls.
    
- **Order instructions to leverage caching:** As discussed, ==put instructions that change less frequently at the top.== For instance, do all your package installation and OS updates early, and ==add your application code later==. This way, if you edit app code, you don’t bust the cache for installing packages. Conversely, ==if you frequently change something, put it towards the bottom. ==This maximizes reuse of cached layers and speeds up rebuilds.
    
- **Minimize the number of layers (where reasonable):** Each `RUN`, `COPY`, `ADD` creates a layer. You ==can combine commands in one RUN using `&&` to reduce layers ==(e.g., `RUN apt-get update && apt-get install -y gcc make && rm -rf /var/lib/apt/lists/*`). However, don’t overdo combining if it hurts readability. Since Docker now has a 125-layer limit which is high, clarity can trump micro-optimizing layers. But definitely remove temporary files in the same RUN that created them (as in the apt example above removing apt cache) – otherwise they stay in an earlier layer and bloat the image.
    
- **Use `.dockerignore`:** ==Always include a `.dockerignore` file to exclude files/folders from the build context that aren’t needed in the image== (e.g., `.git` directory, local node_modules, temp files, etc.). This reduces the context size sent to daemon and avoids accidentally copying in secrets or unnecessary stuff. It also can prevent cache busts due to files that change often but shouldn’t be in the image.
    
- **Avoid secrets in Dockerfiles:** ==Don’t hardcode passwords, API keys, or other secrets in the Dockerfile== (or in the image). If you add a secret file and then delete it in a later layer, it’s still present in the previous image layer. ==Instead, use environment variables at runtime or Docker secrets mechanisms== (or build-time ARGs for less sensitive things, and even then, ARG values can end up in images if not careful). Treat images as potentially public.
    
- **Use multi-stage builds for optimized images:** A multi-stage build means you can have one stage for building your application (which might include compilers, heavy build tools), and a final stage with just the runtime. For example, compile a Go binary in a `golang` image, then copy the binary into an `alpine` base in a second stage. This results in a tiny final image with only the binary and necessary files. ==Multi-stage builds are great for keeping images lean (especially for compiled languages, or front-end builds where you can discard dev dependencies).==
    
- **ENTRYPOINT vs CMD usage:** If you want your container to always execute a specific binary (regardless of what arguments the user passes), use `ENTRYPOINT`. If you just want to provide a default command that can be overridden, use `CMD`. You can combine them to allow flexibility. For instance, `ENTRYPOINT ["nginx", "-g", "daemon off;"]` in the official Nginx image ensures Nginx always starts, and they use `CMD ["nginx.conf"]` to allow specifying an alternate config file as an argument. For most simple apps, using just CMD is fine (and the user can override the whole command by providing their own when running).
    
- **Keep containers ephemeral and stateless:** The image should contain the app and its _immutable_ components. Don’t bake data into the image that changes per environment (config should be via env or volume). Also, the ==container should ideally not write important data to its own filesystem – use volumes for any persistent data==. This way, containers can be killed and replaced without losing state (state is in volumes or external DBs). This aligns with the idea that containers are replaceable units.
    
- **Minimize privileges:** ==Where possible, run the application in the container as a non-root user==. By default, containers run as root inside, which can be a risk if someone breaks out or if you mount the host filesystem, etc. You can create a user in Dockerfile (`RUN adduser ...` or use specific base images that come with non-root users) and use the `USER` instruction to switch. ==Also, only open needed ports and no more==. Consider using tools like Docker Bench for security to check other best practices (like not mounting Docker socket inside containers, etc., but that’s more advanced).
    
- **Testing Dockerfile builds:** It’s often helpful to build and run your Dockerfile locally (or in CI) to test that it actually works (the app starts, etc.). Also, use linter tools (like `hadolint`) that can static analyze your Dockerfile for improvements.
    

By following these practices, you’ll create Docker images that are smaller, safer, and more maintainable. For example, here’s a snippet of a well-structured Dockerfile for a Node.js app:

```Dockerfile
FROM node:16-slim

# Set working directory
WORKDIR /app

# Install dependencies (only package.json and package-lock.json are copied to leverage caching)
COPY package*.json ./
RUN npm install --production

# Copy the rest of the application code
COPY . .

# Ideally, switch to a non-root user (for Node, could use USER node if base image provides it)
# USER node

# Expose port (for documentation)
EXPOSE 3000

# Start the app
CMD ["npm", "start"]
```

In this example,==if you change only the app code but not the package.json, the `npm install` layer is cached.== We also used a slim base and only installed production dependencies. This results in a relatively slim image. Always review official images of similar applications – they often demonstrate best practices that you can emulate.

### Docker Troubleshooting Tips

When working with Docker in real projects, you’ll encounter various issues. Here are common scenarios and tips to resolve them:

- **Container won’t start / exits immediately:** First, run `docker logs <container>` to see error output. Common causes: the application inside might have crashed due to an exception (stack trace in logs), or it started and then immediately exited because it finished work (some programs just run and exit). If it’s the latter (e.g., a script that ends), remember that a container will stop when its main process ends. For example, running `docker run ubuntu echo "hello"` will print hello and exit – that’s expected. If you intended it to keep running (maybe you expected an interactive shell or a server), ensure you launched the right command. For servers, sometimes forgetting `-D` (daemon mode) is an issue (though many server processes in containers are best run in foreground). If the app crashed, the logs and possibly the exit code (`docker ps -a` will show an exit code) are useful. Exit code 139 (segfault) or 137 (killed, often OOM) can hint at issues like memory limits.
    
- **“CrashLoopBackOff” behavior:** This term is from Kubernetes, but you can observe similar rapid restart loops manually (like using `--restart` policy). ==It means the container starts, fails, and Docker (or some orchestrator) keeps restarting it==. In Docker alone, a container won’t restart unless you ran it with a restart policy. If you see a container restarting (check `docker ps` repeatedly or `watch docker ps`), again logs are key. Suppose it’s a web service that can’t connect to a DB and exits – it might repeatedly fail until the DB is up. Solution: ensure dependencies are ready or add retry logic. If using `--restart` policy, maybe disable it while debugging so the container stays down and you can inspect logs and state.
    
- **Cannot connect to container service:** If you run a web server inside Docker but your browser can’t hit it, check: Did you publish the port (`-p` flag)? Is the service listening on the correct network interface? Inside containers, if a process listens on `127.0.0.1` (loopback), it won’t be reachable from outside the container. It should listen on `0.0.0.0` to accept external connections. Most official images do this by default. Also ensure no firewall is blocking the host port. If you run on Linux, Docker handles iptables automatically to allow the mapping. On Windows/Mac Docker Desktop, ports are forwarded – usually not an issue. Use `docker exec <container> netstat -tulpn` (or similar) to see if the port is actually open inside.
    
- **Image not found / pull failed:** If ==`docker pull` can’t find an image, check the name/tag==. Typos or forgetting to include the right tag (it defaults to `:latest` if unspecified) are common. If it’s a private registry (including a private Docker Hub repo), ensure you `docker login` first, or that you included the registry in the image name (like `myregistry.com/myimage:tag`). ==For CI/CD, make sure credentials are passed properly for private registries==. If behind a proxy, ensure Docker daemon is configured for proxy so it can access the internet to pull.
    
- **“Cannot remove image/container – resource busy/in use”:** This happens if you try to remove an image that still has containers referencing it, or remove a volume that a container is using, etc. Use `docker rm` to remove the container first (or `docker rm -f` to force remove running container if needed) then remove the image. For volumes, remove the container or use `docker rm -v` to remove associated volumes with the container.
    
- **Disk space issues:** ==Docker can consume a lot of disk (images, containers, volumes). If you notice disk filling up, run `docker system df` to see space usage==. Dangling images (untagged images leftover from builds) can pile up. `docker image prune` (dangling images) or ==`docker system prune` (dangling images + stopped containers + unused networks==) are handy. Also, volumes with data can use space – remove ones not used (`docker volume ls` to list, `docker volume prune` for unused).
    
- **Volume permission problems:** If you mount a host directory, the UID/GID inside the container might not match your host user, causing permission denied issues. For example, mounting a folder that the container’s process (maybe running as root or some user) can’t write to. Solutions include adjusting permissions on the host folder, or running the container as a user that matches host ownership (via `--user` flag). When using named volumes, Docker typically sets the ownership to root:root unless image entrypoint changes it. If your container runs as non-root, you may need to ensure the volume’s mount point has correct ownership (some images do an `chown` in entrypoint).
    
- **Networking between containers:** ==If you have multiple containers that need to talk (without using host networking), ensure they are on the same Docker network== (the ==default `bridge` network allows communication by IP, but not by container name unless you link or use a user-defined network==). A user-defined bridge network enables name-based resolution. In Compose, this is usually handled for you. ==If one container can’t reach another by name, it’s likely they aren’t on the same network.== Use `docker network ls` and `docker network inspect` to troubleshoot. You can connect containers to networks with `docker network connect`.
    
- **Container is running but something inside doesn’t work:** Sometimes the issue is with the application itself, not Docker. For example, an app might not start because of a missing config, or it’s running but a certain functionality fails due to environment differences. In such cases, treat the container like a normal Linux environment: exec in, check config files, test connectivity (ping, curl within container), etc. Docker basically provides the environment; you then debug the app as you would on a VM.
    

In all cases, ==**Docker’s tooling (logs, inspect, exec)**== are your go-to debugging aids. And remember, because containers are quick to tear down and recreate, you can often iterate rapidly – modify a setting, rebuild (or adjust run command) and re-run the container fresh to see if the issue resolves.

---

## Kubernetes

### Interview Questions (Basic to Intermediate)

**Q1.** _What is Kubernetes, and what problem does it solve?_  
**A1.** **Kubernetes** (often abbreviated “K8s”) is an open-source platform for ==**automating deployment, scaling, and management of containerized applications**.== In simpler terms, it’s a container orchestration system. ==While Docker runs containers on a single host, Kubernetes allows you to manage a cluster of machines and schedule containers (packaged as Pods) across them==. It provides mechanisms for ==service discovery, load balancing, automatic restarts of failed containers, rolling updates of applications, and much more==( #imp ). The problem it solves is that running containers in production at scale is hard – you need to handle things like: what if a server (node) goes down? Kubernetes will reschedule those containers on other nodes. Need to scale an application from 2 instances to 20? Kubernetes can do that with a one-line change or automatically via an autoscaler. It also helps manage configuration and secrets, and generally acts as a robust management layer for clusters of containers. In summary, Kubernetes is an orchestration system that ensures your desired state (e.g., “I want 5 instances of this app running”) matches the actual state (it will launch or kill containers to make it so), providing reliability and scalability for containerized apps.

**Q2.** _What are the main components of a Kubernetes cluster?_  
**A2.** A Kubernetes cluster has two broad parts: the **Control Plane (Master)** components and the **Worker Node** components.

- **Control Plane Components:** These run on the master node(s) and govern the cluster:
    
    - **API Server (`kube-apiserver`):** The core interface for Kubernetes – it exposes a REST API that kubectl and other clients use. All cluster state changes go through the API server.
        
    - **etcd:** This is the distributed key-value **store** that holds all cluster data (the desired state of objects, such as how many replicas for a deployment, etc.). etcd is the **source of truth** for the cluster.
        
    - **Scheduler (`kube-scheduler`):** It watches for new or unscheduled Pods and assigns them to nodes based on resource availability and other constraints. Essentially decides “which node should this Pod run on?”.
        
    - **Controller Manager (`kube-controller-manager`):** A set of controller processes that run to ensure the cluster is always converging to the desired state. For example, the Deployment controller ensures the correct number of pods for a deployment, the Node controller notices node failures, etc.. (There’s also Cloud Controller Manager if in cloud, to manage cloud-specific resources.)
        
- **Worker Node Components:** These run on every node (worker machine) where containers (pods) run:
    
    - **Kubelet:** An agent on each node that takes Pod specs from the API server and ensures the containers are running and healthy on that node. The kubelet communicates with the container runtime (like containerd/Docker) to start/stop containers.
        
    - **Container Runtime:** The software that actually runs containers on a node. It could be Docker, containerd, CRI-O, etc.. Kubernetes talks to it via the CRI interface.
        
    - **Kube-proxy:** A network proxy on each node that configures networking rules so that services (cluster IPs) and other Kubernetes networking features work. It maintains iptables or IPVS rules to route traffic to the correct pods.
        

Additionally, there are **Add-ons** like CoreDNS (for cluster DNS resolving Service names), ingress controllers, etc., but the above are the critical components. To summarize: the control plane (API server, etcd, scheduler, controllers) handles the decisions and state of the cluster, and each worker node (running kubelet, kube-proxy, and a container runtime) carries out those decisions by running the actual workloads (pods).

**Q3.** _What is a Pod in Kubernetes? Why is it often said to be the “smallest deployable unit”?_  
**A3.** A ==**Pod** is the smallest deployable unit in Kubernetes== – it’s essentially a ==wrapper around one or more containers that are meant to be managed as a single entity.== Usually, a Pod contains only one container (the common case), but if you have multiple tightly coupled containers (that must share resources like disk or network identity), you can put them in the same Pod so they run together on the same node.

Key aspects of Pods:

- All containers in a Pod **share the same network namespace** – meaning they share an IP address and port space. They can communicate with each other via localhost. This makes containers in a Pod like an “atomic unit”: they are co-located and co-scheduled. For example, ==you might have a main web container and a sidecar container (like a logging agent) in the same Pod.==
    
- They can also share **storage volumes**. A ==volume in Kubernetes is defined at the Pod level and can be mounted into any container in the Pod.==
    
- If a Pod is moved to a different node (e.g., rescheduled after failure), it gets a new IP. Pods are mortal and can die; Kubernetes encourages treating them as ephemeral. Higher-level controllers (like Deployments) will create new Pods to replace ones that die.
    

When you deploy an application on Kubernetes, you don’t directly deploy containers; you deploy Pods (even if it’s just one container per Pod). Think of a Pod as “a running instance of your application” – if you scale your app to 3, that typically means 3 Pods, each with a container running your app. ==Pods are ephemeral==: if a node goes down, those Pods are lost and new ones may be spun up elsewhere (with new IPs). This is why ==Kubernetes has Services to abstract and provide stable networking to wherever Pods are running== (since pods come and go).

In summary, a ==Pod is the basic unit of scheduling in Kubernetes. It encapsulates one or more containers that should run together== #imp, and it’s the entity upon which higher-level constructs (Deployments, Jobs, etc.) act. **It’s the smallest thing you can create or manage** (you cannot deploy a container directly, it has to be in a Pod). Kubernetes will always manage containers through Pods.

**Q4.** _What is a Kubernetes Deployment? How does it relate to ReplicaSets and Pods?_  
**A4.** A **Deployment** is a higher-level Kubernetes object ==used for managing stateless applications (though you can technically deploy anything with it).== It provides a declarative way to ensure a certain number of **replicas** of a Pod are running, and it facilitates rolling updates and rollbacks.

When you create a Deployment, you specify a **Pod template** (what the Pod should contain: containers, images, ports, etc.) and a ==desired number of replicas. The Deployment controller will ensure that that many Pods are always running based on the template==, by creating an intermediary object called a **ReplicaSet**. The **ReplicaSet** is responsible for maintaining a set of replica Pods – it will create new pods or delete excess pods to match the replica count. But you typically don’t manage the ReplicaSet directly when using Deployments; the Deployment manages it for you.

Key features of Deployments:

- **Self-healing:** If some Pods under the Deployment are deleted or crash, the ==Deployment’s ReplicaSet will create new ones to maintain the desired count.==
    
- **Rolling Updates:** If you update the Deployment (say, change the container image tag or alter the Pod template), the Deployment will perform a rollout: it will create a new ReplicaSet with the updated template, then ==gradually scale up new Pods and scale down the old Pods, achieving a rolling update with zero (or minimal) downtime==. By default, it ensures at most a certain number of pods are unavailable at a time and at most a certain number of extra pods are created during the transition (controlled by `.spec.strategy` settings).
    
- **Rollback:** Deployments keep a history of revisions (by default, it retains the last few ReplicaSets). ==If a new rollout goes bad, you can undo it== – `kubectl rollout undo deployment/<name>` will rollback to the previous ReplicaSet. The Deployment makes it easy to revert to a stable state if a deployment update failed.
    

In essence, a **Deployment** is the go-to way to run a scalable, stateless application on Kubernetes. The relationship is: _Deployment owns ReplicaSet which in turn manages Pods_. The Deployment is to ReplicaSet what ReplicaSet is to Pods – each adds a layer of control/management. You describe your desired state in a Deployment (e.g., “run 5 Nginx pods of this image version”), and Kubernetes (via the Deployment controller and ReplicaSets) makes that happen and keeps it that way. If you scale the Deployment or change its template, it orchestrates the changes smoothly.

**Q5.** _How do you create and deploy an application on Kubernetes? Describe the typical workflow or commands._  
**A5.** There are a couple of ways to deploy an app on Kubernetes: **declarative** (using YAML manifests with `kubectl apply`) or **imperative** (using `kubectl create/run` commands). The typical production workflow is declarative via manifests, but I’ll mention both:

- **Using YAML manifest (declarative):** You write a YAML file that defines the Kubernetes objects you need, usually at least a Deployment (for your pods) and a Service (for exposing them). For example, a Deployment YAML might specify 3 replicas of a Pod running your container image, and a Service YAML might expose that deployment on a certain port. Once you have the YAML, you run `kubectl apply -f <your-file>.yaml` (or apply a directory of many YAMLs). Kubernetes will then create those resources. For instance:
    
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: myapp-deployment
    spec:
      replicas: 3
      selector:
        matchLabels:
          app: myapp
      template:
        metadata:
          labels:
            app: myapp
        spec:
          containers:
          - name: myapp-container
            image: myregistry/myapp:1.0
            ports:
            - containerPort: 80
    ```
    
    This manifest defines a Deployment for 3 replicas of “myapp”. You’d save this to myapp-deployment.yaml and do `kubectl apply -f myapp-deployment.yaml`. Kubernetes will schedule 3 Pods as defined by the template. Similarly, you might have a `Service` YAML:
    
    ```yaml
    apiVersion: v1
    kind: Service
    metadata:
      name: myapp-service
    spec:
      type: ClusterIP
      selector:
        app: myapp
      ports:
        - port: 80
          targetPort: 80
          protocol: TCP
    ```
    
    and apply that to create a Service that load balances across pods with label app: myapp on port 80.
    
    Declaratively, you manage changes by editing the YAML and re-applying (`kubectl apply` intelligently patches differences).
    
- **Imperative with kubectl:** For quick tasks or learning, you can use commands. For example: `kubectl create deployment myapp --image=myregistry/myapp:1.0 --replicas=3`. This will create a Deployment object named myapp. If you need to expose it, you could then do `kubectl expose deployment myapp --port=80 --target-port=80 --type=ClusterIP` (or type=NodePort, etc.) to create a Service. There’s also `kubectl run` (older command) which could create a Pod or Deployment; e.g., `kubectl run mypod --image=nginx` (though in newer kubectl, `run` creates a Pod by default, whereas `create deployment` is preferred for a Deployment).
    
- **Helm or higher-level tools:** In practice, many teams use Helm charts or other config management to templatize YAMLs. But underlying that, it’s still applying manifests.
    

So the typical workflow:

1. Write your app code and containerize it (build/push the Docker image).
    
2. Prepare Kubernetes config (Deployment, etc.) referencing that image.
    
3. Use `kubectl apply` to deploy to the cluster (or an imperative command for quick deploy).
    
4. Kubernetes scheduler will schedule pods on appropriate nodes. You can check status with `kubectl get deployments`, `kubectl get pods`.
    
5. Expose it via a Service or Ingress so it’s reachable (if needed).
    
6. Iterate: if you update the image, you can change the Deployment (edit the image tag) and apply, which triggers a rolling update.
    

For a simple example, if you just want a quick Pod: `kubectl run nginx --image=nginx` will create a pod running nginx. But for a typical app, you’d use a Deployment for management benefits. After deploying, you’d use `kubectl get`/`describe` to ensure everything is running and debug if not.

**Q6.** _What is `kubectl` and can you give some common commands you use to interact with a Kubernetes cluster?_  
**A6.** `kubectl` is the command-line tool for Kubernetes – it allows you to **control the Kubernetes cluster** by calling the Kubernetes API. It’s like the Kubernetes equivalent of Docker CLI for Docker. You use kubectl to deploy applications, inspect and manage cluster resources, and troubleshoot issues.

Some very common `kubectl` commands (and what they do): #imp

- **Context and config management:**
    
    - `kubectl config get-contexts` / `kubectl config use-context` – If you have multiple clusters, contexts let you switch between them. Your kubeconfig (usually at `~/.kube/config`) can store credentials for multiple clusters.
        
    - `kubectl config current-context` – Shows which cluster/context you are operating on (very important to ensure you’re not accidentally pointing to prod when you think it’s dev!).
        
- **Getting resource status:**
    
    - `kubectl get pods` – List pods in the current namespace. Add `-n <namespace>` to specify namespace if not default. You can also do `kubectl get deployments`, `services`, etc. Almost any Kubernetes object can be listed with `get`.
        
    - `kubectl get pods -o wide` – Adds more info, like node the pod is on, pod IP, etc.
        
    - `kubectl get pods -l app=myapp` – Filters by label (in this case, all pods with label app=myapp).
        
    - `kubectl describe pod <pod-name>` – Detailed information about a specific pod, including events. The events section is crucial for debugging (it shows why a pod might be failing scheduling, killing, etc.).
        
    - `kubectl describe node <node-name>` – Info about a node (like what pods are on it, capacity, conditions).
        
    - `kubectl get events` – Shows recent cluster events (which can reveal issues like image pull errors, scheduling failures).
        
- **Deploying and editing resources:**
    
    - `kubectl apply -f <file-or-directory>` – Apply a config file (create or update the resources defined in it). This is used for the declarative approach.
        
    - `kubectl create deployment ...` or `kubectl create -f file` – Imperatively create resources.
        
    - `kubectl delete -f <file>` or `kubectl delete pod <name>` – Delete resources by file or type/name.
        
    - `kubectl scale deployment <name> --replicas=N` – Change the number of replicas in a Deployment.
        
    - `kubectl edit deployment <name>` – This opens the deployment’s YAML in an editor for you to modify on the fly (upon save, changes are applied). This is handy for quick changes, but similar to doing `apply` with a modified manifest.
        
    - `kubectl set image deployment/<name> <container>=<new-image>` – Imperatively set a different image for a deployment’s container (which triggers a rolling update). E.g., `kubectl set image deployment/myapp myapp-container=myimage:v2`.
        
- **Interacting with running Pods:**
    
    - `kubectl logs <pod-name>` – Fetch logs of the pod’s main container (add `-c <container-name>` if the pod has multiple containers and you want a specific one). Use `-f` to follow logs. For example, `kubectl logs -f myapp-pod-abc123`.
        
    - `kubectl exec -it <pod-name> -- /bin/bash` – Open an interactive shell inside a container of the Pod (by default targets the first container if not specified). Useful for debugging (just like `docker exec`). You can run any command, e.g., `kubectl exec mypod -- ls /app` for a one-off command.
        
    - `kubectl port-forward <pod-or-service> <hostPort>:<podPort>` – Forward a port from your local machine to the pod (or service). For example, if you have a database pod only accessible inside cluster, you can run `kubectl port-forward pod/db-pod 5432:5432` and then locally connect to `localhost:5432` to reach that DB. This is great for debugging or accessing something without exposing it as a service.
        
    - `kubectl cp <pod>:<containerPath> <localPath>` – Copy files to/from a pod container (like scp for pods). For example, extract logs or copy a config in.
        
- **Namespace and context usage:**
    
    - Many commands default to the “default” namespace. Use `-n <namespace>` or `--namespace=<ns>` to specify. Or set a default namespace in your kubeconfig context. For example, `kubectl get pods -n kube-system` to see system pods.
        
    - `kubectl get all` – Convenient command to get all major resources (pods, svc, deployments, etc.) in a namespace at once.
        
- **Cluster management:**
    
    - `kubectl cordon <node>` / `kubectl drain <node>` – Mark a node as unschedulable and evict pods (used when you want to maintenance a node).
        
    - `kubectl cluster-info` – See basic info about the cluster (master and DNS addresses).
        
    - `kubectl top nodes` / `kubectl top pods` – Requires metrics server installed, but shows resource usage (CPU/mem).
        

In everyday usage, you’ll often do: `kubectl get pods` (or deployments) to see what’s running, `kubectl describe` to troubleshoot something that’s wrong, `kubectl logs` to check application logs, `kubectl apply` to push updates, and maybe `kubectl exec` for on-the-fly debugging. Being comfortable with these commands is key to operating Kubernetes.

**Q7.** _A Pod is in a “CrashLoopBackOff” state (or continually restarting). How would you troubleshoot this?_  
**A7.** A **CrashLoopBackOff** indicates that a pod’s container is failing, exiting, and being restarted repeatedly, with Kubernetes backing off the restart interval (it waits progressively longer between restarts). To troubleshoot:

1. **Check the Pod’s events and status:** Run `kubectl describe pod <pod-name>`. In the describe output, look at the “Events” section at the bottom. Often it will show something like “Back-off restarting failed container” along with the reason the last restart happened. It might also show events like “Error: Exit Code X” or OOMKill messages.
    
2. **Inspect container logs:** Use `kubectl logs <pod-name> --previous` (since the container likely crashed, `--previous` shows logs from the last failed instance) or just `kubectl logs <pod-name>` if the container is still running briefly and producing output. The logs usually contain the error message or exception causing the crash. For example, it might show a stack trace that indicates a missing file, misconfiguration, cannot connect to a database, etc.
    
3. **Identify the cause:** Common causes of CrashLoopBackOff include:
    
    - Application exception on startup causing it to exit (e.g., misconfiguration like cannot find a service or file).
        
    - A required dependency is not reachable (so app exits with failure). E.g., your service tries to connect to a database service that isn’t available yet, then crashes. Kubernetes restarts it, and it crashes again – maybe eventually the dependency comes up and then it stabilizes, or it might continue to crash.
        
    - The process completes and exits quickly when it’s not supposed to (e.g., someone used a wrong command so the container just runs a script and exits instead of staying alive).
        
    - OOMKilled (Out Of Memory): if the container exceeds its memory limit (or node capacity), it could be killed by Kubernetes. Describe or `kubectl get pod <pod> -o yaml` might show `lastState: terminated: reason: OOMKilled`. If that’s the case, you may need to increase resources or find a memory leak.
        
    - Liveness probe misconfiguration: If you have a liveness probe that is incorrectly causing restarts (though that would show as liveness probe failures in events). That would be a “RestartCount” increasing but not exactly CrashLoopBackOff (CrashLoop is when the process itself crashes).
        
4. **Use `kubectl logs` and `describe` info:** For example, if logs show a config file missing, maybe the ConfigMap volume mount failed or was mis-specified. Or if logs show “connection refused to DB”, maybe your DB service name is wrong or the DB isn’t up.
    
5. **Execute into a running container (if it stays up briefly or you remove the startup command):** In tricky cases, you can copy the pod’s YAML (`kubectl get pod <name> -o yaml > pod.yaml`), edit the command to something like sleep (so it doesn’t crash immediately), apply it (or run a debug container), then exec in to poke around. Kubernetes 1.18+ also has `kubectl debug` which can initiate an ephemeral container for debugging.
    
6. **Check for CrashLoop pattern:** `kubectl get pods` will show the restart count increasing. If it’s CrashLoopBackOff, Kubernetes is backing off restarts (it will show status CrashLoopBackOff after a few rapid failures, and you’ll see in `describe` the back-off timing).
    

In summary, the general steps: **describe the pod to see events**, **check logs** of the crashing container, and from there, **identify the root cause** (application error, missing dependency, resource limit, etc.). Each cause has its remedy (fix config, ensure dependency available, increase memory or fix leak, adjust startup timing or probes). CrashLoopBackOff is a symptom; the real debug is finding _why_ the container keeps crashing.

**Q8.** _What is a Service in Kubernetes and what are the different types of Services?_  
**A8.** A **Service** in Kubernetes is an ==abstraction that defines a logical set of Pods and a policy by which to access them==. ==Services provide a stable network endpoint (IP address + port) for a group of Pods, enabling loose coupling between dependent components. Even if Pods come and go (get rescheduled, IPs change), the Service IP/port stays the same.== #veryImp 

Key points:

- Services by default get a cluster-internal IP (ClusterIP) and a DNS name. Pods can reach the service to talk to any of the member pods behind it (the service will load-balance requests, typically via simple round-robin).
    
- ==Services select Pods using **label selectors**==. For example, a service might have `selector: app: myapp`, and it will ==automatically include any Pods with that label in its pool of endpoints==. If new Pods with that label start, they’re added; if pods die, they’re removed.
    

**Types of Services:**

- **ClusterIP:** _(default)_ – ==The service is only accessible inside the cluster==. It gets a cluster IP (virtual) that you can access from any node/pod within the cluster. This is great for internal communication (e.g., frontend pods connecting to a backend service via its cluster IP or DNS name). ==It’s not reachable from outside the cluster==.
    
- **NodePort:** This ==exposes the service on a static port on each node’s IP. Essentially, each worker node will listen on that port and forward traffic to the service.== NodePort is often used to expose a service externally for simple setups or for on-prem clusters where you don’t have a cloud load balancer. The NodePort range is typically 30000-32767 by default. NodePort services are accessible at `<NodeIP>:<NodePort>`. They also have a ClusterIP internally. (Often people use NodePort in conjunction with an external load balancer or ingress when cloud LB isn’t available.)
    
- **LoadBalancer:** This is used in cloud environments – it will ==provision an actual external load balancer (with a public IP) from the cloud provider (e.g., AWS ELB, GCP LB) and route traffic to the Service’s pods==. Under the hood, a LoadBalancer-type service usually still has a ClusterIP and NodePort, and the cloud integration sets up a cloud LB that targets those node ports. From the user perspective, you get an external IP to hit. This is the easiest way to expose a service on the internet (if your k8s is in a supported environment).
    
- **ExternalName:** This service doesn’t actually define a proxy or IP for pods at all – it simply maps a service name to an external DNS name. For example, you could create an ExternalName service called “db-service” that maps to an external host like “actual-db.example.com”. Pods that lookup “db-service” will get a CNAME to the external host. It’s basically a way to integrate external (non-k8s) services into Kubernetes DNS.
    
- **Headless service (ClusterIP None):** Not a separate `type`, but if you create a Service with `ClusterIP: None`, it won’t get a cluster IP. Instead, it allows direct DNS resolution to pod endpoints. It’s used for stateful sets or cases where you want client-side load balancing or to discover all pod IPs. The service DNS will return the A records of the pods’ IPs.
    

So to summarize types in an interviewer-friendly way:

- _ClusterIP_ – internal only, gives a stable cluster-internal IP for the group of pods.
    
- _NodePort_ – opens a specific port on all nodes to the service, allowing external access via `<nodeIP:nodePort>`.
    
- _LoadBalancer_ – (cloud environments) provisions an external load balancer and exposes the service externally (typically uses NodePort under the hood, but abstracted).
    
- _ExternalName_ – maps service to external DNS name (no proxying by cluster).
    

A typical usage: you ==deploy your app with a Deployment (creating pods) and then create a Service (ClusterIP) to group those pods==. ==Other pods in the cluster can now reach the app via Service name (thanks to DNS) and the Service will distribute traffic to one of the app pods.== If you need this app accessible outside the cluster, you might use a LoadBalancer service or an Ingress resource (with the Service still as ClusterIP behind it). #imp

**Q9.** _How do ConfigMaps and Secrets work in Kubernetes? What’s the difference between them and how would you use them with pods?_  
**A9.** **ConfigMaps** and **Secrets** are both ways to inject configuration data into Kubernetes pods, decoupling configuration from container images. The main difference is that **Secrets are intended for confidential data**, whereas ConfigMaps are for plain configuration (not sensitive).

- **ConfigMap:** Stores non-confidential key-value pairs (strings). It might contain things like config file snippets, environment variables, URLs, etc. A ConfigMap can be created from literal values, from a file, or defined directly in YAML. ConfigMaps do **not** provide encryption or secrecy – they are stored in etcd in plaintext. Use cases: configuring application settings (e.g., external service URLs, feature flags, etc.) which can differ between environments. Pods can consume ConfigMaps in two primary ways:
    
    - **Environment variables:** You can reference a ConfigMap’s values to populate env vars in a container. In the pod spec, you’d do something like:
        
        ```yaml
        env:
        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: my-config      # ConfigMap name
              key: log_level       # the key in the ConfigMap
        ```
        
    - **Mounted files:** You can mount a ConfigMap as a volume. Each key becomes a file in the volume, with the content being the value. For example, if you have a ConfigMap with key “app.properties” and some config text as value, you can mount it so that `/config/app.properties` appears in the container filesystem with that content. Apps can then read that file.
        
    
    ConfigMaps are great for things like providing configuration without baking it into images (so the same image can be used in dev/prod with different settings). Note: If a ConfigMap is updated, pods don’t automatically see the change unless they are programmed to (configMap volumes do update eventually, but env vars won’t update in a running pod).
    
- **Secret:** Similar to ConfigMap in usage (can be used as env vars or mounted as files), but meant for sensitive data like passwords, API keys, tokens, certificates. The data in a Secret is base64-encoded in the resource (which is just an encoding, not encryption – though etcd can be configured to encrypt secrets at rest). By default, Kubernetes will hide the secret’s values when you do `kubectl get secret` (you’ll see `<redacted>` or base64). Access to Secret objects can be restricted by RBAC. Also, when Secrets are mounted into pods, the data is mounted into memory (tmpfs) so it’s not written to node disk.
    
    You create a Secret similarly, either from literal values or from files (often for things like TLS certs). There are also specific secret types like `kubernetes.io/dockerconfigjson` for image pull secrets, etc. But for an app secret, you might do:
    
    ```yaml
    apiVersion: v1
    kind: Secret
    metadata:
      name: db-secret
    type: Opaque
    data:
      username: <base64>   # base64 encoded "admin"
      password: <base64>   # base64 encoded "s3cr3t"
    ```
    
    Then you use it in a pod spec analogously:
    
    - As env vars via `secretKeyRef` (just like configMapKeyRef but for secret).
        
    - As a volume (each key becomes a file; e.g., mount a TLS secret to /tls, you get cert.pem and key.pem files).
        

**Differences:** In addition to the confidentiality aspect, another difference is size limits and usage intended. ConfigMaps can be larger (1MB limit), whereas Secrets are also similar in size (also 1MiB), but you generally keep secrets small. Also, pulling secrets requires the kubelet to have the rights to them; if a pod references a secret, the system ensures that secret is delivered to the node (kubelet) securely. For configmaps, it’s not as sensitive.

**Usage together:** Often, you might use a ConfigMap for non-sensitive configs (like app mode, or URLs) and a Secret for things like credentials. For example, you have a database Pod: you’d give it a ConfigMap with DB_HOST, DB_NAME, etc. and a Secret with DB_USER and DB_PASSWORD. In your Deployment spec for the DB client, you’d map those into env vars. This way, if the password rotates, you just update the Secret (and maybe restart pods or they pick up new on next start).

So, ConfigMap = **plain config** (not encrypted, just a convenience object), Secret = **sensitive config** (with some minor protection and intended for things you wouldn’t put in a ConfigMap). Both help avoid hardcoding values in Pod specs or images, enabling separation of config from code.

**Q10.** _What is a Namespace in Kubernetes and how is it used?_  
**A10.** A **Namespace** is a way to divide a Kubernetes cluster into multiple virtual clusters. In Kubernetes, _namespaces provide a mechanism to isolate groups of resources within a single cluster_. They are like separate environments. Resources in different namespaces are logically separate – for instance, you can have a Deployment named “web” in namespace A and another Deployment named “web” in namespace B, and they won’t conflict because their full names are different (web.A vs web.B effectively).

Key points about namespaces:

- Namespaces are intended for multi-team or multi-project environments where you want to segregate resources. For small clusters or single-team setups, often everything is just in the “default” namespace.
    
- Certain resources are **namespaced** (like Pods, Services, Deployments, etc.), meaning they exist within a namespace. Other resources are cluster-wide (not namespaced), like Nodes, PersistentVolumes, or cluster roles.
    
- Namespaces can be used to apply resource quotas (you can limit how much CPU/Memory a namespace can use, or how many objects it can have) and to set up network policies that restrict communication, etc. It’s a security and organization boundary.
    
- By default, all user-created pods/services/etc. go to the “default” namespace unless you specify otherwise. You can create a namespace via `kubectl create namespace <name>` or in YAML. Common ones that exist by default: `kube-system` (for system pods like DNS, etc.), `kube-public` (mostly unused, some public info), and `default`.
    
- Kubernetes does not isolate namespaces at the network level by default (all pods can talk to all pods, regardless of namespace, unless restricted by NetworkPolicies). The isolation is more about naming and resource management.
    
- In a company, you might give each team its own namespace. They can manage their resources without stepping on another team’s resource names. You can also tie RBAC roles to namespaces, e.g., Team A’s service account can only CRUD in namespace A, not in others.
    

Think of it like this: If you have multiple environments (dev, staging, prod) on one cluster, you might implement those as separate namespaces to isolate them. Or if multiple applications share a cluster, separate them by namespace. Names within a namespace must be unique, but not across the whole cluster.

One nuance: Services in different namespaces get their own DNS domain like `<service>.<namespace>.svc.cluster.local`, so a Service is discoverable within its namespace by just its name, and from other namespaces by `<service>.<namespace>`. This further shows how namespaces partition the cluster.

To use a namespace, you can specify it in YAML metadata or via `kubectl -n`. Also you can set a default namespace in your kubectl context.

**Q11.** _How do you scale an application in Kubernetes?_  
**A11.** Scaling in Kubernetes can be done **manually** or **automatically**:

- **Manual scaling:** You adjust the number of replicas of a Deployment (or ReplicaSet). For example, if you have a Deployment running 3 replicas of your app and you want to handle more load, you might scale it to 10 replicas. You can do this via `kubectl scale deployment myapp --replicas=10` or by editing the deployment YAML (either via `kubectl edit` or updating your config and doing `apply`). The Deployment controller will then create the additional Pods (via its ReplicaSet) and schedule them on nodes. Conversely, scaling down removes Pods. This is a pretty quick process – if resources are available, new pods come up within seconds (plus whatever time to pull images if needed and start containers).
    
- **Horizontal Pod Autoscaler (HPA):** Kubernetes can scale your application automatically based on metrics (like CPU utilization or custom metrics). You set up an HPA object that targets a Deployment (or ReplicaSet/StatefulSet) and defines min/max replica count and a target metric (say, keep average CPU at 50%). The Kubernetes Metrics Server (or Prometheus adapter for custom metrics) will feed metrics to the HPA, and the HPA will increase or decrease the replica count accordingly. For example, if CPU usage goes high, HPA might scale out from 3 pods to 6 pods. HPA is horizontal scaling (more pods). There’s also Vertical Pod Autoscaling (adjusts resource requests of pods, not as commonly used in all setups).
    

To scale _stateful_ applications (e.g., StatefulSets), the concept is similar, but you might have considerations (like ordering or unique identities). Deployments (stateless) are easiest to scale.

So practically: Let’s say your web Deployment is currently at 2 replicas and you need more capacity:

```bash
kubectl scale deploy/web --replicas=5
```

This will issue an update to the deployment’s spec. You can verify with `kubectl get pods` – you’ll see new pods being created (with new names) until there are 5. The service (if any) that fronts these pods automatically starts sending traffic to the new pods as they become Ready.

If using an HPA, you would have set that up (e.g., `kubectl autoscale deploy/web --min=2 --max=10 --cpu-percent=50`). Then you typically wouldn’t manually scale; the HPA controller would adjust replicas up/down between 2 and 10 based on metrics.

Another angle is scaling at the cluster level (adding more nodes to handle more pods), but I suspect the question is about application scaling (pods).

**Q12.** _What is a StatefulSet, and how is it different from a Deployment? When would you use a StatefulSet?_  
**A12.** **StatefulSet** is a Kubernetes workload object designed for managing stateful applications, where each instance (pod) is not identical and may need persistent storage and stable network identities. Key differences from a Deployment:

- **Stable Pod Names:** Pods in a StatefulSet have a stable, unique identity. They get named with an index (e.g., my-db-0, my-db-1, my-db-2). Even if they are rescheduled to another node, they keep the same name. Deployment’s pods are anonymous and interchangeable, while StatefulSet pods are like pets (though automated).
    
- **Ordered Deployment and Scaling:** StatefulSet can ensure pods start in order (0 then 1 then 2, etc.) and terminate in reverse order if you scale down. This can be important for things like Kafka, Zookeeper, or databases that need sequential bring-up or tear-down.
    
- **Stable Storage:** Typically you use PersistentVolume Claims (PVCs) with StatefulSets. Each pod gets its own PVC, and that is uniquely associated with that pod (via naming). So my-db-0 will use pvc my-data-my-db-0. If that pod dies and is recreated, it will reattach the same volume, so data persists. With Deployments, if you used PVCs, it’s trickier to ensure each pod attaches to the right one because Deployments don’t maintain one-to-one identity as easily.
    
- **No default load balancing by Service (Headless service often used):** Usually, you use a **Headless Service** (`ClusterIP: None`) with a StatefulSet so that each pod can be addressed individually (DNS for each pod like my-db-0.my-service.default.svc.cluster.local). With Deployments, you typically use a Service to load-balance across replicas. With StatefulSet, you might still have a service for clients to connect generally, but you often care about each specific pod (like in a DB cluster where pod-0 is the master, etc.).
    

When to use StatefulSet: any scenario where you have stateful applications requiring stable identity or ordering. Examples: databases (MySQL cluster, MongoDB replica sets), distributed consensus systems (etcd, Zookeeper), Kafka and other message brokers, or any service where each node has data that should persist and perhaps a fixed identity (like node indices). If an application expects to be “node 1” in a cluster and keep that identity, StatefulSet is appropriate.

By contrast, a **Deployment** is ideal for stateless, replicated workloads (web servers, stateless microservices) where any pod can service any request and pods are interchangeable. Deployments do rolling updates nicely, but they don’t guarantee ordering or stable identity.

To highlight difference: **Deployments** don’t guarantee the same pod will get the same volume or IP each time – if you scale down and up, you might get new pod names. **StatefulSets** provide guarantees about pod ordering and uniqueness. Each StatefulSet pod keeps a constant name and usually gets a persistent volume attached that remains even if the pod is rescheduled. Use StatefulSet when your app needs these guarantees (for example, a legacy system that registers node names, or a cluster that elects a leader by node name).

If asked about how Kubernetes achieves stable network ID: when you create a Headless Service for the StatefulSet, each pod gets a DNS entry `<podname>.<service name>.<namespace>.svc.cluster.local` pointing directly to its IP. For storage, the PVCs created by the StatefulSet have the pod index in their name.

**Q13.** _What is a DaemonSet in Kubernetes and when would you use it?_  
**A13.** A **DaemonSet** ensures that **each (or some) node** in the cluster runs a copy of a particular Pod. In other words, it’s used for deploying pods that you want to run on every node (or every node of a certain type or group). When new nodes are added to the cluster, the DaemonSet will automatically schedule the pod onto those nodes. When nodes are removed, the DaemonSet’s pod is garbage-collected.

Typical use cases for DaemonSets:

- **Cluster services that need to run on all nodes**: e.g., log collection agents (like Fluentd/Fluent Bit) that collect logs from each node, node monitoring agents (like Prometheus node exporter, Datadog agents), or network agents (like CNI plugins or network proxies).
    
- **Storage daemons**: if using something like GlusterFS or Ceph, you might use a DaemonSet to run the storage daemon on each node.
    
- **Node-specific tooling**: anything that should run once per node as a background service.
    

For example, Kubernetes by default uses a DaemonSet in the kube-system namespace for things like `kube-proxy` on each node (depending on setup) or core DNS node-local caches, etc. If you wanted to ensure a certain security agent runs on all nodes, you’d use a DaemonSet.

How is it different from a Deployment? A Deployment would schedule pods wherever there’s room, not necessarily one per node. A DaemonSet, however, ignores the typical scheduler for counting replicas – it ties pod lifecycle to nodes. If you have 5 nodes, a DaemonSet will ensure 5 pods (one on each). If node 6 joins, it spawns a pod there. If node 2 goes away, its pod is gone and not rescheduled elsewhere (because the idea is one per node, not maintaining a count).

Key details:

- DaemonSet pods usually have a `NodeSelector` or tolerations if they only target certain nodes (e.g., maybe you have linux and windows nodes and you schedule DS only on linux nodes by nodeSelector, or you tolerate master node taints to run on master if needed).
    
- If you update a DaemonSet (like image version), it will roll out changes to each pod similarly to a rolling update but often sequentially node by node.
    
- You typically don’t scale a DaemonSet (scaling concept doesn’t apply, it scales with nodes). If you try to scale via replicas, it’ll likely not do what you expect – the DS manages count based on nodes.
    

So you’d use a DaemonSet whenever you have a piece of infrastructure that should always be present on every node for proper operation or management of the cluster. A concrete example: “We need a filebeat log shipper on all nodes to send logs to Elasticsearch” – implement that as a DaemonSet of filebeat pods. Without DaemonSet, you’d have to manually run one pod per node or something brittle.

**Q14.** _What are liveness and readiness probes in Kubernetes? Why are they important and how do they work?_  
**A14.** **Liveness and Readiness Probes** are Kubernetes mechanisms to monitor the health of containers in a pod and take action based on that health.

- **Liveness Probe:** Answers the question “Is my container _still alive_ (healthy) or has it hung/failed in a way that it cannot recover?” If a liveness probe fails, Kubernetes will restart the container (kill it, and depending on restart policy, the kubelet will create a new instance). This helps auto-recover from certain application failures (like deadlocks, or if the process is stuck). For example, a liveness probe could be an HTTP GET on `/health` endpoint of your app, or an exec of a command like `pgrep someProcess` or even a simple `true/false` check. If your app fails this check (say it doesn’t respond or returns an unhealthy status), Kubernetes assumes the container is bad and will kill+restart it.
    
- **Readiness Probe:** Answers “Is my container _ready_ to serve requests?” Readiness probes determine if the Pod should receive traffic. If a readiness probe fails, Kubernetes will remove the Pod’s IP from the endpoints of the Service, meaning no traffic is sent to it until it passes again. The container is not killed when readiness fails (unlike liveness). This is useful for cases where an application may be alive but not ready to serve (e.g., still initializing, or temporarily overloaded). Readiness probe could check that the app can accept traffic – maybe the same endpoint as liveness or a more application-specific check (like can it connect to its DB, or has it loaded necessary data).
    

Why they are important:

- They enable **self-healing**: liveness probes can automatically remedy certain failure modes by restarting the container. Without it, a hung process would just sit there forever.
    
- They ensure **correct traffic routing**: readiness probes prevent sending requests to pods that aren’t actually ready to handle them. For example, during startup, your app might take 30 seconds to load data – readiness stays false, so the Service won’t send traffic yet (important during rolling updates to not send traffic to new pods until they’re ready).
    
- They also can coordinate with deployment rolling updates: A Deployment will wait for new pods to be ready (readiness probe success) before considering an update complete (and depending on strategy, it might not scale down old ones until new are ready).
    

How they work:

- In the Pod spec, under `containers`, you can define `livenessProbe` and `readinessProbe` (and there’s also `startupProbe` for a special case). You specify a probe type: HTTP GET (to a specified path and port), TCP socket check, or Exec (run a command in the container). You also specify things like initialDelaySeconds (time after container start to begin probing, so your app has time to start), interval, timeout, successThreshold, failureThreshold.
    
- The kubelet on each node is responsible for actually performing these probes on the schedule.
    
    - If a liveness probe fails `failureThreshold` times in a row, kubelet will consider the container unhealthy and kill it (the restart policy on the Pod governs what happens next – usually it’s Always restart, so it will be restarted).
        
    - If a readiness probe fails, the kubelet marks the pod as not ready (this affects Endpoints for Services: the pod’s IP is removed). When the readiness probe starts succeeding again, the pod is marked ready and traffic can flow to it.
        
- Example:
    
    ```yaml
    readinessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 10
    livenessProbe:
      httpGet:
        path: /healthz
        port: 8080
      initialDelaySeconds: 5
      periodSeconds: 15
    ```
    
    This might check /healthz. If at any time /healthz returns a non-200 or times out for liveness, container is restarted. For readiness, as long as /healthz fails, pod is out of service endpoints (no traffic).
    
- Common pattern: readiness probe might be more stringent (e.g., checks dependencies) whereas liveness might be a simpler sanity check (like process up). Or they might even hit the same endpoint but with different effect.
    

One important note: if a pod is _not ready_ (readiness failing), it’s removed from Service endpoints but still exists. A Deployment will not reduce its count below the desired, because the pod is alive (just not ready). If a pod is _unhealthy (dead)_ due to liveness failing, it’s restarted – which also triggers unready state during restart.

In sum, **liveness = when to restart** a container, **readiness = when to send traffic**. They improve resilience and maintain service availability during updates and failures.

**Q15.** _How can you perform a rolling update (deploy a new version) in Kubernetes, and how can you roll back if something goes wrong?_  
**A15.** Rolling updates and rollbacks in Kubernetes are usually handled via the Deployment (or StatefulSet, etc.) mechanisms:

- **Rolling Update:** If you have a Deployment, you update the Pod template (for example, change the container image tag to a new version). This can be done by:
    
    - Editing the deployment (`kubectl edit deployment myapp`) and changing the image field.
        
    - Running an imperative command like `kubectl set image deployment/myapp mycontainer=myimage:v2`.
        
    - Applying a new config (`kubectl apply -f deployment.yaml`) with the updated image or other changes.
        
    
    Kubernetes Deployments by default use a rolling update strategy. This means it will create a new ReplicaSet for the new version and gradually shift pods from the old ReplicaSet to the new one:
    
    - It will bring up a few new pods (controlled by `spec.strategy.rollingUpdate.maxSurge`, default 25% of replicas can be above desired during update).
        
    - As those become Ready, it will terminate some old pods (controlled by `maxUnavailable`, default 25%).
        
    - It continues this until all pods are the new version. This achieves a rollout with zero downtime (if `maxUnavailable` is at least 1 less than number of pods, service always has some).
        
    - You can monitor this with `kubectl rollout status deployment/myapp` which will tell you when the rollout is complete or if it’s stuck.
        
    
    So basically, to perform a rolling update, you just declare the new state (new image or config) and Kubernetes handles the transition. If your Deployment has, say, 4 replicas, by default it might spin up one new pod (5 total, 1 surge) then kill one old (back to 4), then another new, etc., maintaining service availability.
    
- **Rollback:** If the new version has an issue, Kubernetes Deployments allow easy rollback:
    
    - You can run `kubectl rollout undo deployment/myapp` to rollback to the previous revision. Deployment keeps the last few revisions (revision history).
        
    - You can also rollback to a specific revision if you have multiple (using `--to-revision=n`).
        
    
    When you rollback, Kubernetes will basically do another rolling update but this time to the old ReplicaSet (scaling it up and scaling down the bad new one). The Deployment’s history is updated (the rollback itself counts as a new revision).
    
    For example, if you noticed your Deployment is unhealthy after an update (perhaps pods are CrashLooping), you could quickly execute the rollback. The system will then start spinning up pods of the old version and terminate the new ones, again in a rolling fashion (to minimize downtime).
    

A few additional notes:

- You can pause a rollout (`kubectl rollout pause deployment/myapp`), make multiple changes, then resume it (`kubectl rollout resume deployment/myapp`), which is useful if you want to batch config changes in one rollout.
    
- `kubectl rollout status` is great to wait for completion or see progress. It might say “Waiting for rollout to finish: 2 out of 4 new pods have been updated…” etc.
    
- If a rollout fails (e.g., pods never become ready), by default it will keep trying unless you have configured a deadline (`progressDeadlineSeconds`). If that deadline is exceeded, the deployment is marked as failed (and kubectl rollout status would indicate that). At that point, `rollout undo` is needed to recover.
    

For StatefulSets, rolling updates are a bit different (ordered, often one by one), and rollback is typically manual (as of Kubernetes now, you might have to deploy old version again, since StatefulSets don’t have revision history like Deployments).

In summary: to do a rolling update, **update the Deployment’s pod spec** (typically the image tag). Kubernetes will rollout in increments (new pods and killing old pods gradually). To rollback, use **`kubectl rollout undo`** which will revert to the last working spec (previous revision). This mechanism helps achieve zero-downtime updates and quick recovery if the new deployment is bad.

**Q16.** _Kubernetes stores cluster state in etcd. What is etcd and what role does it play in Kubernetes?_  
**A16.** **etcd** is a distributed, consistent key-value store used as Kubernetes’ backing store for all cluster data. It’s often described as the “brain” of Kubernetes – every configuration, state detail, and metadata about the cluster is stored in etcd (API objects like Deployments, Pod definitions, Service configurations, secrets, etc., as well as status info).

Role of etcd in Kubernetes:

- When you use `kubectl` to create or modify an object, that request goes to the API Server, which then writes the new desired state to etcd. etcd is the only place where cluster state persists; control plane components are mostly stateless (they can be restarted and they reconstruct state from etcd).
    
- Controllers (like the Deployment controller, node controller, etc.) watch the API server for changes (which essentially means watching etcd for changes via the API). For example, you create a Deployment, the API server stores it in etcd, then the Deployment controller sees the new Deployment object and starts creating ReplicaSet/Pods to fulfill it.
    
- etcd is a **consistent** datastore (it uses the Raft consensus algorithm). This is crucial because in a distributed control plane (multiple API server instances etc.), you need a single source of truth that’s fault-tolerant. etcd typically runs on master nodes (in a 3 or 5 node cluster for HA).
    
- If etcd is down or lost, the cluster cannot function correctly. The API server becomes read-only or unresponsive because it can’t read/write state. So etcd reliability is critical. That’s why Kubernetes clusters often have 3 or 5 etcd members (odd number for Raft quorum) for high availability.
    
- Also etcd is where things like **cluster state and secrets** reside. So securing etcd (with encryption at rest, restricting access) is important, because if someone can read etcd, they basically can get all config and secret data of the cluster.
    
- etcd stores not only your deployed objects, but also cluster state like what nodes exist, what pods are running where (though that is also reflected by Node and Pod objects in etcd), role-based access control definitions, etc. Essentially any Kubernetes API object is a record in etcd.
    

In sum, etcd’s role: **persistent storage of the Kubernetes cluster’s desired state and some of the observed state**. Controllers work to make the real world match the desired state stored in etcd. etcd is to Kubernetes what a database is to an application – without it, Kubernetes has no memory of what should be running. That’s why backing up etcd is effectively backing up your cluster’s state (there are tools and commands for etcd backup).

To illustrate, if you query Kubernetes for all Pods (`kubectl get pods`), the API server is actually retrieving that information from etcd (via cache typically, but etcd is the source). If etcd entries are changed (only should be via API), that reflects in cluster.

Non-Kubernetes context: etcd is a general key-value store. In Kubernetes it’s not directly exposed to users (you normally don’t interact with etcd directly, you go through API). But as an operator, you care about etcd’s health.

So etcd is a **consistent key-value database** that Kubernetes uses to store everything about the cluster’s state. It’s lightweight but very powerful in providing reliability via consensus. The API server makes all modifications to etcd (no other component writes to etcd directly, all go through API server to keep integrity with validation and such). In a HA control plane, etcd itself is the component that has a leader and replicates data among members.

### Kubernetes Key Concepts and Use Cases

- **Kubernetes Cluster and Nodes:** A Kubernetes cluster consists of a set of worker machines called **nodes** (could be VMs or physical), and a **control plane** that manages the nodes. Each node runs a kubelet (agent) and can host one or more Pods. The control plane components (API server, etcd, scheduler, controllers) can run on dedicated master nodes or co-located, depending on setup. This architecture lets Kubernetes abstract the resources of multiple machines as a single pool for your applications.
    
- **Pods:** The basic unit of scheduling in K8s, as discussed. Usually one application container per Pod (plus maybe sidecars). All containers in a Pod share the same network IP and can communicate via `localhost`. They also share any volumes mounted. Pods are ephemeral – they can die and not be resurrected (except by controllers that create new ones). So never assume a specific Pod will always be there; instead use higher-level controllers and Services.
    
- **Labels and Selectors:** Labels are key-value tags attached to objects (like pods, services, nodes, etc.). They are used to organize and select objects. For example, you might label pods with `app=frontend` or `env=staging`. **Label selectors** allow operations like a Service selecting pods (e.g., Service has selector app=frontend to pick up all frontend pods), or a Deployment managing pods with a certain label. They are fundamental for group management and querying. Best practice is to label things clearly (app name, component, environment, tier, etc.).
    
- **Controllers (Deployment, ReplicaSet, etc.):** Controllers are loops that drive the cluster towards desired state:
    
    - **ReplicaSet** ensures a specified number of identical pods are running. You usually don't create these by hand (Deployments manage them for you).
        
    - **Deployment** adds rolling update and versioning on top of ReplicaSets – the most common way to manage stateless app pods.
        
    - **StatefulSet** for stateful apps needing stable identity.
        
    - **DaemonSet** for one-per-node pods.
        
    - **Job** and **CronJob** for running batch or scheduled tasks (Jobs ensure a one-time task’s pods complete N times, CronJobs schedule Jobs periodically).
        
    - **HorizontalPodAutoscaler (HPA)** to automatically adjust replicas of a deployment based on metrics.
        
    - **ConfigMap** and **Secret** are technically objects, not controllers, but they are used by pods for configuration as described.
        
- **Service & Networking:** Kubernetes uses a flat network model where each Pod gets an IP address (usually from an overlay network or CNI plugin). However, Pods come and go, so you rarely use Pod IPs directly. Instead, you define a **Service** which gives a stable address (ClusterIP) and DNS name. The Service routes to the currently healthy pods (via label selector). There are also different Service types (ClusterIP, NodePort, LoadBalancer, ExternalName) as described. Under the hood, kube-proxy sets up iptables rules or IPVS to forward traffic from the Service IP to pod IPs. For Service discovery, Kubernetes automatically creates DNS entries (if CoreDNS is set up, which it is by default in most installs). So if you have a service “myapp” in namespace “demo”, any pod can resolve `myapp.demo.svc.cluster.local` to the Service’s cluster IP.
    
- **Ingress:** While Services of type LoadBalancer can expose services, in many setups you use an **Ingress** resource with an **Ingress Controller** (like nginx ingress, Traefik, etc.) to manage external HTTP/HTTPS access. Ingress allows you to define rules for routing (hostnames, paths) to different services, and handle things like TLS termination. For example, you can expose multiple services under one IP with different URL paths or hostnames via an Ingress. Ingress is a higher-level concept for web traffic entry.
    
- **Volumes & Persistent Storage:** Kubernetes abstracts storage with **Volumes**. Unlike Docker, volumes in K8s can outlive the container (especially persistent volumes). There are many volume types (emptyDir, hostPath, configMap, secret, persistentVolumeClaim, etc). The pattern for persistent storage:
    
    - Define a **PersistentVolume** (PV) which represents an actual storage (like a cloud disk, NFS share, etc.) – or rely on dynamic provisioning.
        
    - Define a **PersistentVolumeClaim** (PVC) which is a request for storage by a pod (ex: “I need 10Gi of storage, of type SSD”). If using a storage class and dynamic provisioning, a PV may be provisioned on the fly. The PVC will bind to a matching PV.
        
    - In the Pod spec (or Deployment), include a volume that references the PVC. Now that volume is mounted in the container, providing durable storage. If the pod dies and is recreated, if it uses the same PVC, it gets the same data.
        
    - For StatefulSets, Kubernetes can manage one PVC per pod automatically (naming them with the pod). For Deployments (stateless), usually all replicas share a PVC is not possible (only one pod can bind to ReadWriteOnce volume in most cases), so for multiple writers you need either a RWX volume (like NFS) or handle state externally.
        
    
    ephemeral volumes: emptyDir (lifetime of pod), configMap and secret (as discussed).
    
- **Namespaces:** (Recap) Useful for multi-tenancy or environment separation. For example, you might have “dev”, “staging”, “prod” namespaces to isolate those. Resource quotas and limits can be applied per namespace to avoid one team hogging all resources. Namespaces also scope names of resources and can scope access control.
    
- **Resource Limits and Requests:** In each container spec, you can optionally specify resource requests and limits for CPU and memory.
    
    - **Request** is how much the pod is guaranteed (scheduler uses requests to decide which node can fit the pod).
        
    - **Limit** is the maximum the container can use. If it tries to use more CPU than limit, it will be throttled; if it uses more memory than limit, it will be killed (OOMKilled).
        
    - It’s important to set these to help Kubernetes do proper bin packing and to ensure one noisy app doesn’t exhaust node memory and crash others. A one-year devops should know how to set basic requests/limits (e.g., in YAML).
        
    - There are also QoS classes: Guaranteed (requests==limits for all containers), Burstable, BestEffort (no requests). Guaranteed pods are last to be evicted under pressure.
        
- **Taints and Tolerations:** Mechanism to control pod placement. A taint on a node means “I repel pods that don’t explicitly tolerate this”. For instance, master nodes often have a taint to prevent general pods from scheduling there (`node-role.kubernetes.io/master:NoSchedule`). If you did want a pod on master, it needs a toleration. This is advanced scheduling concept but sometimes used in devops tasks (like dedicate certain nodes to certain workloads).
    
- **Affinity/Anti-affinity:** You can specify rules so pods either prefer or require to be (or not to be) on the same node or zone, etc., as other pods. For example, anti-affinity to spread replicas of an app across nodes for high availability. Or affinity to put certain workloads together for data locality.
    

**Real-World Use Cases:**

- **Microservices Deployment:** Kubernetes is often used to host microservices architecture. Each microservice might be a Deployment with a set of replicas and a Service for discovery. They can independently scale. Kubernetes will ensure each microservice is healthy (via probes) and can communicate with others via services.
    
- **High Availability Web Applications:** Use Deployment for stateless web frontends, possibly multiple replicas behind a Service. Use an Ingress or LoadBalancer to expose it to the internet. If a node goes down, those pods are recreated on other nodes. The Service and Ingress ensure continuous availability.
    
- **Big Data / Streaming:** Deploy Kafka or Cassandra with StatefulSets so each broker/node is stable. Use PersistentVolumes for storing data. K8s helps automate the recovery of nodes while preserving data.
    
- **CI/CD pipelines:** Some organizations run CI jobs on Kubernetes using Jenkins agents as pods (with Jenkins Kubernetes plugin) or Tekton, etc. They take advantage of dynamic scaling and isolation of pods for builds.
    
- **Batch Processing:** Using Jobs/CronJobs for tasks like nightly data processing. Kubernetes schedules these jobs when resources are available and can retry on failure.
    
- **Hybrid cloud or edge:** A unified K8s control plane can manage workloads across different environments, giving a consistent deployment model.
    

**Kubernetes Best Practices:**

- **Declarative Over Imperative:** Maintain your resource manifests (YAML files) in version control and apply them (possibly with GitOps). This way cluster state is reproducible and changes are tracked. Avoid making ad-hoc changes via kubectl edit in production (except emergency) - instead update the config and reapply.
    
- **Use Health Probes:** Always configure liveness and readiness probes for your pods if applicable. This ensures Kubernetes can manage your app’s lifecycle robustly (don’t route traffic to unready pods, auto-restart stuck pods).
    
- **Resource Requests/Limits:** Set reasonable requests and limits for CPU/memory for each container. This helps the scheduler and improves stability under load. Also consider cluster capacity - if you overcommit too much without limits, you risk instability.
    
- **Use Namespace Separation:** Use namespaces to separate different environments or teams to avoid naming collisions and to apply policies (like RBAC or network policies) more easily. Also possibly use resource quotas in less trusted multi-tenant clusters to prevent one team from consuming all resources.
    
- **Apply Security Practices:**
    
    - Use RBAC to restrict who (and which service accounts) can do what. By default, in modern Kubernetes RBAC is on. Ensure you give least privilege needed (e.g., your app’s service account probably only needs to read certain configmaps, not list all secrets cluster-wide).
        
    - Consider using NetworkPolicies to restrict pod-to-pod communication as appropriate (by default, all pods can talk to all pods). For sensitive components like databases, you might only allow traffic from certain app pods.
        
    - Keep Kubernetes up-to-date with security patches, and similarly keep your base images updated.
        
    - Don’t run unnecessary things in privileged mode. And in your pod specs, drop capabilities your container doesn’t need, run as non-root if possible (SecurityContext).
        
- **Logging and Monitoring:** Implement centralized logging (e.g., EFK stack or a cloud logging service) and monitoring (Prometheus, etc.). Use liveness counts, etc., as signals. Kubernetes emits events and metrics (via metrics-server for basic CPU/memory, and via cAdvisor for more). Set up alerts for issues like CrashLoopBackOff or node not-ready, etc.
    
- **Readiness during Deployments:** Ensure your readiness probes truly reflect readiness, so that rolling updates don’t kill all old pods before new ones are ready. This prevents downtime.
    
- **Rolling updates and Canary:** Use the Deployment rollout for zero downtime. For more complex scenarios, consider canary or blue-green deployments. Kubernetes can do canaries by having multiple Deployments and adjusting a Service selector or using Ingress routing splits, or using service mesh. Tools like Argo Rollouts or Flagger can automate canary processes.
    
- **Stateful workloads caution:** When running databases or any stateful app on Kubernetes, design with persistence and backup in mind. Use StatefulSets and PVCs. Understand that if the whole cluster goes down, you must have backups of etcd (for cluster state) and PV data (if not externally stored) to restore state. For critical data, some prefer managed database services outside the cluster, but many run stateful systems in K8s successfully.
    
- **Autoscaling:** Use HPA for workloads that have variable demand to automatically scale pods. Just ensure you have metrics-server installed and your app exports metrics if using custom ones. Also ensure your cluster has enough nodes or set up cluster autoscaler so it can add nodes when pod demand exceeds current capacity.
    
- **Infrastructure as Code:** Treat cluster setup as code too (maybe using Terraform or CloudFormation for cloud resources, kubeadm or scripts for cluster config). This, along with declarative manifests for workloads, yields reproducibility.
    
- **Testing changes:** Have a staging namespace or cluster to test new config or releases via the same manifests before applying to production. This catches errors in YAML or config issues with less impact.
    
- **Keep etcd safe:** If you manage the control plane, secure etcd (enable encryption at rest for secrets if possible, restrict network access to etcd). Regularly backup etcd data (there are tools or `etcdctl snapshot`).
    
- **Cluster maintenance:** Monitor node health and resource usage. Use taints or cordon/drain when doing maintenance on nodes (drain will gracefully evict pods by signalling to controllers to reschedule them).
    
- **Documentation and Training:** Ensure your team knows how to check on pods (`kubectl get/describe, logs`) and basic troubleshooting. Kubernetes has a learning curve, but a one-year DevOps should be comfortable with these basics by now.
    

### Common kubectl Commands and YAML

Below is an example **Deployment YAML** and **Service YAML** to illustrate structure and how resources link:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-world
  labels:
    app: hello-world
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello-world              # matches labels on Pod template
  template:
    metadata:
      labels:
        app: hello-world           # Pods get this label
    spec:
      containers:
      - name: hello-container
        image: nginxdemos/hello:latest
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: "100m"
            memory: "128Mi"
          limits:
            cpu: "200m"
            memory: "256Mi"
        livenessProbe:
          httpGet:
            path: /                   # this example container serves a webpage
            port: 80
          initialDelaySeconds: 15
          periodSeconds: 20
        readinessProbe:
          httpGet:
            path: /                   # ready when homepage is reachable
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 10
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-service
spec:
  type: ClusterIP
  selector:
    app: hello-world                # will target the pods labeled app=hello-world
  ports:
  - port: 80                        # service port
    targetPort: 80                  # container port
```

In this example, we deploy 3 replicas of a simple web server (`nginxdemos/hello`). We request ~0.1 CPU and 128Mi memory each, and cap at 0.2 CPU/256Mi. We set up probes so that Kubernetes knows when a pod is alive and ready (the app might take ~5s to start serving). The Service will group these pods and provide a stable ClusterIP on port 80, directing traffic to pod port 80. Any pod in the cluster could curl `hello-service:80` to get a response from one of the hello-world pods.

**Kubectl commands for this example:**

- `kubectl apply -f hello-deployment.yaml` (creates the Deployment)
    
- `kubectl apply -f hello-service.yaml` (creates the Service)
    
- `kubectl get deployments` (shows the deployment status, e.g., DESIRED=3, CURRENT=3, UP-TO-DATE=3, AVAILABLE=3)
    
- `kubectl get pods -l app=hello-world` (list pods with that label, should see 3 Running)
    
- `kubectl describe service hello-service` (shows service details, including endpoints i.e. which pod IPs are tied to it)
    
- `kubectl port-forward service/hello-service 8080:80` (you could use this to test the service from your local machine – then browsing [http://localhost:8080](http://localhost:8080/) will hit one of the pods)
    
- `kubectl scale deployment hello-world --replicas=5` (scales up to 5 pods)
    
- `kubectl set image deployment/hello-world hello-container=nginxdemos/hello:latest` (this example already latest, but showing how to change image if needed)
    
- `kubectl rollout status deployment/hello-world` (would show rollout progress if image was updated)
    
- `kubectl delete -f hello-service.yaml` & `kubectl delete -f hello-deployment.yaml` (to cleanup resources).
    

### Kubernetes Troubleshooting Tips

Common things to troubleshoot in Kubernetes and how:

- **Pod Pending:** If `kubectl get pods` shows a pod stuck in **Pending** state, it means it’s not scheduled to a node yet. Use `kubectl describe pod <name>` to see why. Look at the events – often you’ll see something like “0/5 nodes are available: 5 nodes insufficient memory” (meaning scheduler can’t place it due to resource requests > available), or “Unschedulable: no nodes match node selector/affinity” or “node(s) had taints that pod didn’t tolerate”. Solutions: adjust requests, fix selectors/taints, or add nodes. If it’s waiting on a PVC binding, you’d see events about PersistentVolumeClaims – if no PV available and storage class isn’t providing, the pod will pend until volume is bound.
    
- **Image Pull Issues (ImagePullBackOff / ErrImagePull):** This happens if the image name is wrong or credentials are needed. Describe the pod or check events, you’ll see something like “Failed to pull image ... not found” or “authentication required”. If it’s a private registry, ensure you created a Secret for docker creds and linked it via imagePullSecrets in the pod spec. If it’s a typo in image name or tag, correct it in the Deployment and redeploy. Once the image is accessible, Kubernetes will retry pulling (BackOff means it’s backing off retry attempts).
    
- **Crashing containers (CrashLoopBackOff):** As discussed, check `kubectl logs <pod>` for the container’s output (add `-c name` if multiple containers). That usually shows why it’s crashing (exception, misconfig). Then address the root cause (maybe the app can’t connect to something – ensure service/env is correct; or maybe a Secret is missing). Also `kubectl describe pod` will show restart count and last state. If it’s OOMKilled, you’ll see that in describe (and `kubectl logs --previous` might show nothing because it was killed by system). OOMKilled => increase memory limit or find memory leak. If CrashLoopBackOff just started after a config change, consider rolling back that change.
    
- **Pods running but not responding ( readiness issues):** If a pod’s containers are running but your Service isn’t sending traffic, check `kubectl get pods` and see if `READY` column says 0/1 (meaning container running but pod is not ready). That indicates readiness probe failing. Describe the pod to see readiness probe failures in events. If your readiness probe is misconfigured or too strict, fix it or give the container more time. This often happens if your probe expects an endpoint to be up but container isn’t ready that fast. Adjust `initialDelaySeconds` or the probe path/conditions. Until readiness succeeds, Service won’t include the pod.
    
- **Service not working (can’t connect):** If you can’t connect to a Service:
    
    - Ensure the Service has at least one endpoint: `kubectl describe svc <name>` and see Endpoints list. If empty, that means no pods match the selector in Ready state. Maybe your pods have different labels or are failing readiness.
        
    - If endpoints exist, but external connection fails for a NodePort/LoadBalancer, ensure any cloud provider resources are provisioned (for LoadBalancer, `kubectl get svc` should show an external IP). NodePort requires hitting the node’s IP on the NodePort. If on a cloud, might need the cloud’s external IP or open firewall for that port.
        
    - For ClusterIP services being accessed from within cluster: maybe a DNS issue (CoreDNS not working). Check if CoreDNS pods are running (`kubectl get pods -n kube-system -l k8s-app=kube-dns`). If they are down, DNS won’t work. If DNS is an issue, try accessing service by `clusterIP:port` to confirm service works minus DNS.
        
    - If only one pod of many is failing to reach another service, could be a network policy blocking it (check NetworkPolicies in effect).
        
- **Debugging inside Pods:** If logs aren’t enough, `kubectl exec` into the container. You can run tools like `curl` inside to see if it can reach other services, check env vars, etc. If your container image doesn’t have a shell or debugging tools, you can temporarily deploy a debug pod (like `kubectl run debug --rm -it --image busybox sh` in same namespace) and use `nslookup` or `wget` from there to test DNS and connectivity.
    
- **Node Problems:** If many pods on a particular node are having issues, or `kubectl get nodes` shows a node NotReady:
    
    - Describe the node (`kubectl describe node nodename`). It will show conditions and any recent events. NotReady often means the kubelet isn’t healthy or it can’t reach the API server or the node is down. Also if out of disk or network not available, it may mark NotReady.
        
    - If node is NotReady due to network, check if other nodes are fine (maybe a network partition). If due to disk pressure, the condition will show MemoryPressure or DiskPressure. Pods might be evicted if node is out of resources.
        
    - Check kubelet and kube-proxy logs on the node (if you have access to node logs) for deeper.
        
    - Sometimes nodes get tainted automatically (e.g., if a node has disk pressure, a taint is added). That can prevent new pods scheduling there. Fix underlying issue (free disk) and remove taint if necessary.
        
    - For troubleshooting, you might cordon (`kubectl cordon nodename`) to stop scheduling and drain pods (`kubectl drain nodename --ignore-daemonsets`) to move pods off, then investigate node.
        
- **Application Performance issues:** Use `kubectl top pods` to see resource usage if something is throttling or OOMing. If CPU usage is at limit and being throttled, perhaps increase limit or replicas. If memory is climbing, watch for near limit (to avoid OOMKilled). If overall cluster CPU is maxed, consider adding nodes or using cluster autoscaler.
    
- **Troubleshoot RBAC Denials:** If a service account or user can’t do something (“Forbidden” message), describe the role/rolebinding or clusterrolebinding. Use `kubectl auth can-i ... --as=...` to simulate if a user can do an action. Adjust RBAC accordingly. Look at events; sometimes you see “Failed to list pods: (Forbidden)” in logs of a controller, meaning its service account lacks a permission.
    
- **Checking Events:** Remember to run `kubectl get events --sort-by=.metadata.creationTimestamp` in a namespace (or `-A` for all) to see recent events. This is often very insightful as it lists scheduling failures, image pull attempts, kills, etc., in chronological order.
    
- **Using Logs for Controllers:** If a Deployment isn’t working as expected or HPA not scaling, you might check the logs of the kube-controller-manager or metrics-server, etc., but in managed environments you might not have easy access. But usually events and statuses suffice.
    
- **Networking Tools:** Deploy a simple pod with networking tools (like `nicolaka/netshoot` image or a busybox) to test connectivity between pods, or to external endpoints. This helps confirm if cluster networking is working (can pod on node1 reach pod on node2, etc.).
    
- **Keep an eye on etcd and API Server in control plane:** If kubectl commands are sluggish or failing cluster-wide, could be etcd or API server issues. Check their status (if self-managed control plane). If using cloud’s managed control plane, not as much in your control – you’d open support if that was suspected.
    
- **Describe is your friend:** `kubectl describe` on virtually any object gives lots of info. E.g., `kubectl describe deployment`, `describe job`, etc., shows events related to that object.
    
- **Examining Config:** Sometimes issues come from wrong config being applied. `kubectl get <resource> -o yaml` lets you see exactly what is in the cluster (maybe a field was wrong). This is good to compare with what you think you applied.
    

Troubleshooting is often about isolating where the problem lies: Is it my application (check app logs)? Or the environment (pod scheduling, service discovery, etc.)? Or cluster (node or network problems)? The above approaches target each layer. Over time, patterns like CrashLoopBackOff or specific event messages become easy to recognize. The Kubernetes community and docs have many common error scenarios documented.

---

This concludes the comprehensive DevOps interview prep material for **Docker** and **Kubernetes**. By understanding these questions, answers, and guides, a candidate with ~1 year experience should be well-equipped to discuss how containers and orchestration work, and demonstrate practical knowledge of using Docker and Kubernetes in real-world scenarios. Good luck with the interview!