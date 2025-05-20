# **1) Add user to docker grou**p
   - sudo usermod -aG docker <user_name>
   - eg. sudo usermod -aG docker ubuntu

# **2) Docker's Auto Caching**
Docker ==**automatically caches image layers** during the build proces==s. Each line in a `Dockerfile` creates a new layer. If Docker detects that a layer hasn't changed (based on the command and its inputs), it reuses the **cached layer** rather than re-executing the command.

##  ⚠️ Why You Might Want to Avoid or Be Cautious with Auto Caching

While layer caching improves performance, it **can introduce bugs, security risks, or unwanted behavior** if misused or misunderstood.

---

### 🔁 1. **Stale Builds**

You might get a **false sense of a successful build** if a layer uses a cache but the underlying data or dependencies have changed outside Docker's awareness.

**Example**:
`RUN apt-get update && apt-get install -y curl`

If cached, `apt-get update` might not run, leading to **outdated packages or security vulnerabilities**.

**Fix**: Split update and install, or use `--no-cache` or `--pull`.

---

### 🕳 2. **Security Risks**

Using cached layers may skip pulling the latest security patches from base images or updated libraries.

**Fix**:

- Rebuild base layers regularly (`docker build --no-cache`).
    
- Use CI tools that invalidate cache for critical layers.
    

---

### 🧩 3. **Incorrect Assumptions in Dependency Layers**

For example, if you copy `package.json` and run `npm install`, it might cache that step. But if you update `package-lock.json` (not copied earlier), you’re now working with a mismatched install.

**Fix**:  
Ensure you copy both `package.json` and `package-lock.json` **together**.

---

### 🔄 4. **Unintended Behavior in CI/CD**

CI builds relying on cache might succeed while local or prod builds fail due to different cache states.

**Fix**:

- Set `--no-cache` on CI builds.
    
- Use deterministic builds or explicitly control cache layers (e.g., BuildKit).
    

---

### 🛠 5. **Hard to Debug**

When things go wrong, cached layers obscure what's actually being run.

**Fix**:

- Use `docker build --no-cache` during debugging.
    
- Add `RUN echo "Step X"` in critical points to track flow.

## ✅ When to Use Caching

- Speeds up builds significantly.
    
- Ideal in dev environments where layers don’t change frequently.
    
- Good for large dependencies or compiled assets.
    

---

## 🚫 When to Avoid It

- Security-sensitive environments.
    
- Complex build pipelines that frequently change.
    
- Debugging inconsistent behavior.
---
---


# **3)Docker image tagging**

To use unique tag we can have different methods 
#### 1. **Tag by Git Commit SHA**

### ✅ Ideal for: traceability, reproducibility
```
git_sha=$(git rev-parse --short HEAD)
docker build -t myapp:$git_sha .
```
#### 🔍 Benefits:

- Each image maps to a specific Git snapshot.
    
- Easy to audit and roll back.

### 🕒 2. **Tag by Date/Time**

### ✅ Ideal for: chronological order, nightly builds, CI pipelines
```
timestamp=$(date +%Y%m%d-%H%M%S)
docker build -t myapp:$timestamp .
```

# **4) Docker CPU and Memory fixing**
To assign **real-time CPU and memory limits** to a Docker container, you need to use Docker's resource control flags. These are useful for **performance tuning, isolation, and preventing resource exhaustion** on the host.

   1) `docker run --memory="Xm" --cpu="X" nginx:latest`
     eg. `docker run --memory="512m" --cpu="1.5" nginx:latest`
         "Give this container 512 megabytes of memory."

### ✳️ Other Memory Options:

|Option|What It Does|
|---|---|
|`-m` or `--memory`|Sets a hard memory limit.|
|`--memory-swap`|Adds swap memory (RAM + swap file). E.g., `--memory-swap=1g` means 512MB RAM + 512MB swap.|
|`--memory-reservation`|Sets a soft limit. Container can go over it if needed temporarily.|
|`--oom-kill-disable`|Stops Docker from killing the container if it uses too much memory. Be careful with this!|

### ✅ Example:
`docker run -m 1g --memory-swap=2g myapp`

- Gives 1 GB of RAM.
- Allows 1 GB of swap space.
- Total: 2 GB max usage.
    
---
---

## ⚙️ Easy Ways to Control CPU

### ✅ 1. **Limit the Amount of CPU It Can Use**

```bash
docker run --cpus="1" myapp
```

🟢 Means: “You can use up to 1 full CPU core.”

```bash
docker run --cpus="0.5" myapp
```

🟢 Means: “You can only use 50% of one CPU core.”

---

### ✅ 2. **Pin to Specific Cores**

```bash
docker run --cpuset-cpus="0,1" myapp
```

🟢 Means: “Only use CPU core 0 and 1 — not the others.”

---

### ✅ 3. **Set Relative Priority (Optional)**

```bash
docker run --cpu-shares=512 myapp
```

🟢 Means: “You are a lower priority than others with higher shares.”

Note: This doesn’t stop it from using CPU — it just gets **less** if others are competing.

---

## 🖥️ What Happens If I Don’t Set CPU Limits?

- Your container might **use 100% of CPU**, slowing down the rest of your system.
    
- Good in development or for fast builds, but **not safe in production**.
    

---

## 🔍 Want to See It in Action?

Run:

```bash
docker stats
```

It shows you live CPU and memory usage for each container — like a task manager.

---

## ✅ Summary

|What You Want|What to Use|
|---|---|
|Limit total CPU|`--cpus="1.5"`|
|Pick exact CPU cores|`--cpuset-cpus="0,1"`|
|Give a container lower priority|`--cpu-shares=512`|
|Watch real-time usage|`docker stats`|

# **5)Run container as Non-Root user**

To **run a Docker container as a specific user**, rather than the default `root` inside the container, you can use the **`--user` flag** in the `docker run` command. This is ==very useful for **security**, **file permission management**, and **sandboxing**.==

---
## 👤 Why Run as a Specific User?

By default, containers run as **root inside the container**, even if the host user is not root. This can cause:

- **Security risks** (privilege escalation)
- **Permission errors** when accessing mounted volumes
- **Inconsistent file ownership** when writing to host
---

## ✅ 1. **Run with a Specific User ID (UID)**

```bash
docker run --user 1000 myimage
```

🟢 This runs the container as user ID `1000`. Commonly, UID `1000` is your first non-root user on Linux.

---

## ✅ 2. **Run with a Username**

If the container image has a user named `appuser`:

```bash
docker run --user appuser myimage
```

If it doesn’t, you'll get an error — so it's safer to use UID/GID if you're unsure.

---

## ✅ 3. **Run with UID and GID**

```bash
docker run --user 1001:1001 myimage
```

🟢 This explicitly sets **user and group IDs**, useful for file permission consistency.

---

## 🛠️ 4. **Create a User Inside Dockerfile (Optional)**

If you want your image to have a non-root user: #veryImp #bestway 

```Dockerfile
FROM ubuntu

RUN useradd -m myuser
USER myuser
```

This ensures the image **always runs as a non-root user** unless overridden.

---

## 📁 5. **File Access with Mounted Volumes**

Running containers with the host user’s UID ensures files created in mounted volumes are owned by the **host user**, avoiding permission issues.

```bash
docker run --user $(id -u):$(id -g) -v $(pwd):/app myimage
```

---

## 👀 6. **Check Current User Inside Container**

To verify which user the container is using:

```bash
docker run --rm myimage whoami
```

Or:

```bash
docker run --rm myimage id
```

---

## 🚀 Examples

### Example 1: Run as current host user

```bash
docker run --user $(id -u):$(id -g) myimage
```

### Example 2: Always use non-root user in image

```Dockerfile
FROM node:18
RUN useradd -m appuser
USER appuser
```

### Example 3: Run as root but drop to user for app

```bash
docker exec -u appuser container_name bash
```

---

## 🛡️ Pro Tip: Use `USER` in Dockerfile + `--user` at runtime

This gives flexibility: a **safe default**, and the ability to override when needed



# **6)Automatic container restart**

To configure **automatic container restarts in Docker**, you can use the `--restart` policy option when running containers with `docker run`.

## Restart Policy Options

| Policy           | Description                                                              |
| ---------------- | ------------------------------------------------------------------------ |
| `no`             | (default) Do not automatically restart the container.                    |
| `always`         | Always restart the container if it stops, regardless of the exit status. |
| `on-failure`     | Restart only if the container exits with a non-zero exit code.           |
| `unless-stopped` | Restart unless the container is explicitly stopped by the user.          |
## 📌 Examples

### 1. Restart always
`docker run --restart always my-image`

### 2. Restart on failure (up to 5 times)
`docker run --restart on-failure:5 my-image`

### 3. Restart unless manually stopped
`docker run --restart unless-stopped my-image`

---
## Best Practices

- Use `always` or `unless-stopped` for long-running services.
    
- Use `on-failure` for jobs that should retry only when errors occur.
    
- Combine with logging/monitoring to detect and address crash loops.

# **7)How to check if process is running inside container or not**

`docker exec -it <container_name/id> sh -c "ps aux | grep -i <process_name>"`
eg. `docker exec -it demo-container sh -c "ps aux | grep -i nginx"`

# **8)Run container in restricted mode**
To run a Docker container in **restricted mode**, you can apply security features to limit its access to system resources, the network, filesystem, and kernel capabilities. Here are key techniques:

---

## 🛡️ Notes on Running Docker in Restricted Mode

#### 1. **User Restrictions**

Run as a non-root user inside the container:

```dockerfile
# In Dockerfile
USER nobody
USER ubuntu
```

Or when running:

```bash
docker run --user 1001:1001 my-image
```

#### 2. **Limit Kernel Capabilities**

Drop unneeded Linux capabilities:

```bash
docker run --cap-drop=ALL --cap-add=NET_BIND_SERVICE my-image
```

#### 3. **Read-Only File System(can't write anything)**

```bash
docker run --read-only my-image
```

#### 4. **Limit CPU & Memory**

```bash
docker run --memory=256m --cpus="0.5" my-image
```

#### 5. **Seccomp Profile**

Use Docker’s default or custom seccomp profile to restrict syscalls:

```bash
docker run --security-opt seccomp=default.json my-image
```

#### 6. **Disable Networking**

```bash
docker run --network none my-image
```

#### 7. **Mount with No-Exec/Read-Only**

```bash
docker run -v /data:/data:ro my-image
```
---

# **9) Limit process number**
| Option           | Description                                            |
| ---------------- | ------------------------------------------------------ |
| `--pids-limit=N` | Limits the container to N processes (threads included) |
| `--pids-limit=0` | Removes the limit (default behaviour if not set)       |
eg. `docker run -it --pids-limit 50 --memory=256m --read-only alpine sh`

This container can spawn **only 50 processes**. Beyond that, Docker will prevent new ones from starting.

- Improves **container isolation**
- Prevents **resource abuse** in shared environments
# **10)Save container log on host machine**
`docker run -d -v $(pwd-present working dir)/logs:/var/log/nginx(any location where logs get generated) <image_name>`

eg. `docker run -d -v $(pwd)/logs:/var/log/nginx nginx`

# **11)Rename docker container name**

`docker rename <old_name> <new_name>`
eg. `docker rename xyz abc'
# **12)Docker image converted to tar file**
- normal ---> tar
`docker save <image_name>:<tag> > file_name.tar`
eg. `docker save py-image:latest > py-file.tar`

- tar ---> docker image 
`docker load < file_name.tar`
eg. `docker load < py-file.tar`

# **13)Copy file from container --> host**

`docker cp <container_name>:path_to_file ./file_to_save`

eg. `docker cp nginx_container:/usr/src/app/jayesh.txt ./jayesh.txt` 


# **14)Docker stats check**
`docker stats --no-stream`


# **15)Docker container health-check**

## ✅ Purpose

A **healthcheck** monitors if a container is running properly. Docker marks it as `healthy` or `unhealthy`.

---

### 🛠️ 1. **In Dockerfile**

```dockerfile
HEALTHCHECK CMD curl -f http://localhost:8080/health || exit 1
```

You can add options:

```dockerfile
HEALTHCHECK --interval=30s --timeout=10s --retries=3 \
 CMD curl -f http://localhost:8080/health || exit 1
```

---

### 🧪 2. **With `docker run`**

```bash
docker run \
  --health-cmd="curl -f http://localhost:8080/health || exit 1" \
  --health-interval=30s \
  --health-timeout=10s \
  --health-retries=3 \
  my-image
```

---

### 🔍 3. **Check Status**

```bash
docker inspect <container_name>
```

Look for `.State.Health.Status`
