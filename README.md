# Docker-Lab2
# ITI - Docker Lab2 🐋

## Task 1:
Run a container using nginx image, and mount a directory from your host into the 
Docker container. example: /home/samy/nginx:/home/nginx (bind mount)
```bash
mkdir nginx_bindMount
cd nginx_bindMount

docker run -d --name nginx_bindMount -v /root/nginx_bindMount:/user/share/nginx/html nginx
docker exec -it nginx_bindMount bash

cd /user/share/nginx/html
echo "Hello Docker" > index.html
exit
docker inspect -f '{{.NetworkSettings.IPAddress}}' nginx_bindMount    ->(my-ip-network) 172.17.0.0
curl (my-ip-network) 172.17.0.0
```
---

## Task 2:
### Steps
#### 1. Create 2 docker network (net-1 & net-2)
```bash
docker network create net-1
docker network create net-2

docker network ls

```
#### 2. Run 2 new containers using nginx:alpine image, and attach the net-1 to them
```bash
docker run -d --name container1 --network net-1 nginx:alpine
docker run -d --name container2 --network net-1 nginx:alpine
```
#### 3.  Run another 1 new containers using nginx:alpine image, and attach the net-2 to them
```bash
docker run -d --name container3 --network net-2 nginx:alpine

```
#### 4. Inspect the 3 containers to know their IPs and write them aside
```bash
docker inspect -f '{{.NetworkSettings.Networks.network_1.IPAddress}}' container1 172.0.0.0
docker inspect -f '{{.NetworkSettings.Networks.network_1.IPAddress}}' container2 172.0.0.1
docker inspect -f '{{.NetworkSettings.Networks.network_2.IPAddress}}' container3 172.0.0.2

```
#### 5. Enter a container in the net-1 network and try to ping a container in the net-2 
network (What do you notice?)
```bash
docker exec -it container1 sh 
ping 172.0.0.2
ping fails, because the two containers exist in different networks so we can see any response in terminal.
```
#### 6. Enter a container in the net-1 network and try to ping the other container in the 
same network (What do you notice?)
```bash
docker exec -it nginx_net1 sh 
curl 172.0.0.0
ping 172.0.0.1
ping successful and return response, because the two containers exist in the same network.

```
---
## Task 3: Explain the difference between Docker volumes and Bind Mount.
```bash
Docker volumes and bind mounts are both mechanisms used to persist data between a Docker container and the host machine, but they have different implementations and use cases.

Docker Volumes:
Managed by Docker: Volumes are managed by Docker itself. When you create a volume, Docker manages where the volume data is stored on the host file system.
Isolation: Volumes are separate from the container's file system. They are not directly accessible from the host's file system, and their data is isolated within Docker's storage area.
Performance: Volumes typically offer better performance compared to bind mounts, especially on Docker for Mac and Docker for Windows, where they use a native filesystem (osxfs or Hyper-V respectively).
Easier to Manage: Docker provides commands to create, remove, and manage volumes (docker volume create, docker volume rm, etc.).
Backing Store: Volumes can use a variety of storage drivers, including local filesystem, cloud providers (like AWS), and networked storage solutions.

Bind Mounts:
Mapped to Host File System: Bind mounts map a directory or file on the host machine directly into a container. There is no intermediate storage area managed by Docker.
Direct Access: Since bind mounts directly map to the host's file system, changes made in the container are immediately visible on the host, and vice versa.
Flexible: Bind mounts offer more flexibility in terms of what can be mounted and where. You can mount individual files, directories, or even block devices.
Performance: Bind mounts might have slightly lower performance compared to volumes, especially on macOS and Windows, where they rely on file system sharing solutions.
Permission Handling: The permissions of files in a bind mount can be controlled by the host's file system permissions.

When to Use Each:
Volumes: Use volumes when you need Docker to manage the data and want better performance or when you need to share data between multiple containers.
Bind Mounts: Use bind mounts when you need direct access to files or directories on the host's file system, or when you want to persistently store configuration files, application code, or any other data outside the container.
```


