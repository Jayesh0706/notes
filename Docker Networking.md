## Container networking refers to the ==ability for containers to connect to and communicate with each other, or to non-Docker workloads.==

## [User-defined networks](https://docs.docker.com/engine/network/#user-defined-networks)

You can create custom, ==user-defined networks, and connect multiple containers to the same network==. Once connected to a user-defined network, containers can communicate with each other ==using container IP addresses or container names==
```
docker network create -d bridge my-net
docker run --network=my-net -itd --name=container3 busybox
```


### [Drivers](https://docs.docker.com/engine/network/#drivers)

The following network drivers are available by default, and provide core networking functionality:

| Driver    | Description                                                                                       |
| --------- | ------------------------------------------------------------------------------------------------- |
| `bridge`  | The default network driver.                                                                       |
| `host`    | Remove network isolation between the container and the Docker host.(==Insecure==)                 |
| `none`    | Completely isolate a container from the host and other containers.(most secure no way to connect) |
| `overlay` | Overlay networks connect multiple Docker daemons together.                                        |
| `ipvlan`  | IPvlan networks provide full control over both IPv4 and IPv6 addressing.                          |
| `macvlan` | Assign a MAC address to a container.                                                              |
### 1. `bridge`

[Bridge networks](https://docs.docker.com/network/bridge) create a software-based bridge between your host and the container. ==Containers connected to the network can communicate with each other==, but they’re ==isolated from those outside the network.==

==Each container== in the network is assigned its ==own IP address==. Because the network’s bridged to your host, containers are also able to communicate on your LAN and the internet. They will not appear as physical devices on your LAN, however.

### 2. `host`(Insecure)

Containers that use the [`host` network mode](https://docs.docker.com/network/host) ==share your host’s network== stack without any isolation. ==They aren’t allocated their own IP addresses==, and port binds will be published directly to your host’s network interface. This means a container process that listens on port 80 will bind to `<your_host_ip>:80`.

### 3. `overlay`

[Overlay networks](https://docs.docker.com/network/overlay) are ==distributed networks that span multiple Docker hosts==. The network allows all the containers running on any of the hosts to communicate with each other without requiring OS-level routing support.

Overlay networks implement the networking for [Docker Swarm clusters](https://docs.docker.com/engine/swarm), but you can also use them when you’re running two separate instances of Docker Engine with containers that must directly contact each other. This allows you to build your own Swarm-like environments.

### 4.`none` 

Provides full network isolation. A container on the “none” network has== **no network interfaces== except loopback**. This is ==used to completely isolate a container from any external network== (useful for security testing or when no networking is needed). The container can only communicate with itself.


These drivers meet different needs. For example, use **bridge** or custom bridge networks for multi-service apps on one host, **overlay** for cross-host connectivity (Swarm or multi-host), **host** for high-performance or special cases, and **none** to isolate.


You can define custom networks in `docker-compose.yml` under the top-level `networks:` key. Each service then lists the networks it should join. For example:

```
services:
  proxy:
    build: ./proxy
    networks: [frontend]
  app:
    build: ./app
    networks: [frontend, backend]
  db:
    image: postgres
    networks: [backend]

networks:
  frontend:
  backend:

```
Here **proxy** and **app** are on `frontend` (but **db** is not, so proxy↔db cannot talk). **app** and **db** share `backend` (so app↔db can communicate). Only services on a common network can reach each other Compose also supports external (pre-created) networks by using `external: true`.


### Production Best Practices: Networking
1) **Network segmentation:** Avoid using the default network for all containers. **Create custom networks** to isolate groups of services and enforce the principle of least privilege

2) **Avoid host mode unless needed:** Using `--network host` gives performance benefits but ==removes isolation==. Restrict host-network usage to trusted, performance-sensitive containers (e.g. high-throughput proxies) and document it well

3) **DNS names and service discovery:** Rely on built-in ==DNS for service names rather than hard-coding IPs==. In Compose/Swarm, always refer to containers by service name. This ensures that if containers are recreated (with new IPs), peers resolve names to the new address
4) **Use `none` for isolation:** If a container should not have any network access (e.g. a ==security scanner or a very trusted component==), launch it with `--network none`. This guarantees it cannot receive external traffic. 