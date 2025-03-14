# Docker Bridge: Balancing Isolation & Connectivity

## 📌 Objective
The goal of this experiment is to explore and demonstrate network isolation in Docker containers. We will examine how containers within the same custom bridge network can communicate, while those on different networks remain isolated. Understanding this is crucial for securing microservices and containerized applications.

## 🌐 Introduction to Docker Networking
Docker networking is fundamental for containerized applications, allowing containers to communicate while ensuring security and isolation. Docker provides several networking options:

### 🔹 Types of Docker Networks:
- **Bridge Network (Default)** – Allows communication between containers using internal IPs unless restricted.
- **Custom Bridge Network** – Offers better control and supports name-based resolution.
- **Host Network** – Attaches containers directly to the host’s network stack.
- **Overlay Network** – Enables communication across multiple hosts (Docker Swarm).
- **Macvlan Network** – Assigns a MAC address to each container, making them appear as separate devices.
- **None Network** – Completely disables networking.

For this demonstration, we focus on the **custom bridge network**, which improves control and network isolation.

## ⚡ Why Use a Custom Bridge Network?
A custom bridge network offers several advantages:
- ✅ **Improved Security** – Containers on different networks are isolated by default.
- ✅ **Better Performance** – Direct communication without host networking stack overhead.
- ✅ **DNS-Based Resolution** – Containers communicate via names instead of IPs.
- ✅ **Greater Control** – Define specific subnets, IP ranges, and gateways.

To demonstrate, we create a custom bridge network called **bhavesh-bridge** and connect multiple containers.

---

## 🔧 Step 1: Creating a Custom Bridge Network
```sh
docker network create --driver bridge --subnet 192.168.50.0/24 --ip-range 192.168.50.96/28 bhavesh-bridge
```
### 🔍 Explanation:
- `--driver bridge` → Uses the default bridge network mode.
- `--subnet 192.168.50.0/24` → Defines the network’s IP range.
- `--ip-range 192.168.50.96/28` → Allocates IPs dynamically.

---

## 🚀 Step 2: Running Containers in the Custom Network
### Running Redis Container (**bhavesh-database**)
```sh
docker run -itd --net=bhavesh-bridge --name=bhavesh-database redis
```

### Running BusyBox Container (**bhavesh-server-A**)
```sh
docker run -itd --net=bhavesh-bridge --name=bhavesh-server-A busybox
```

### 📌 Check Container IPs
```sh
docker network inspect bhavesh-bridge
```
Expected Output:
```
 bhavesh-database: 192.168.50.100
 bhavesh-server-A: 192.168.50.101
```

---

## 🔄 Step 3: Testing Communication Between Containers
### Ping from `bhavesh-database` to `bhavesh-server-A`
```sh
docker exec -it bhavesh-database ping -c 4 bhavesh-server-A
```
### Ping from `bhavesh-server-A` to `bhavesh-database`
```sh
docker exec -it bhavesh-server-A ping -c 4 bhavesh-database
```
✅ **Expected Outcome:** Both containers should successfully ping each other.

---

## 🚧 Step 4: Demonstrating Network Isolation with a Third Container
We add another container (**bhavesh-server-B**) on the default **bridge** network.

```sh
docker run -itd --name=bhavesh-server-B busybox
```
### 📌 Get IP of `bhavesh-server-B`
```sh
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' bhavesh-server-B
```
_(Example IP: `172.17.0.4`)_

---

## ❌ Step 5: Testing Communication Between Different Networks
### Ping from `bhavesh-database` to `bhavesh-server-B`
```sh
docker exec -it bhavesh-database ping -c 4 172.17.0.4
```
🚨 **Expected Outcome:** The ping should **fail**, as `bhavesh-server-B` is on a different network.

---

## 🔍 Step 6: Confirming Network Isolation
### Inspect Networks
```sh
docker network inspect bhavesh-bridge
docker network inspect bridge
```
✅ **bhavesh-bridge** should contain `bhavesh-database` & `bhavesh-server-A`.
✅ **bridge** should contain `bhavesh-server-B`.

---

## 🏆 Conclusion
✅ **Containers in the same network can communicate.**
✅ **Containers in different networks are isolated by default.**
✅ **Docker’s networking model ensures security and separation unless explicitly connected.**

🚀 Now you have mastered **Docker Bridge Networking**! 🎯

