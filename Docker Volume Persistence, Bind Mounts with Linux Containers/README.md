# 🚀 Docker Volume Persistence: Bind Mounts on Linux Containers

## 📌 Introduction
This experiment demonstrates how to use **Docker Bind Mounts** with a **Linux container** to persist data beyond a container’s lifecycle. By mounting a local directory into a container, data remains accessible even after the container is removed.

---

## 🔧 Steps & Observations

### 🏗 Step 1: Running a Container with a Bind Mount
Run the following command to start an Alpine container with a bind mount:
```sh
docker run -dit --name alpine_with_bind_mount -v C:/Users/bhave/docker_data:/data alpine:latest sh
```

#### 🔍 What Happens?
- The **Alpine Linux** image is pulled if not available locally.
- A **new container** named `alpine_with_bind_mount` is created.
- The `-v` flag **mounts the local directory** `C:/Users/bhave/docker_data` to `/data` inside the container.
- The container runs a shell (`sh`) in **detached mode**.

---

### 📄 Step 2: Creating a File Inside the Bind Mount
Run the following command to create a file inside the container:
```sh
docker exec -it alpine_with_bind_mount sh -c "echo 'Hello, Bhavesh!' > /data/testfile.txt"
```

#### 🔍 What Happens?
- A shell is executed inside the running container.
- A file `testfile.txt` is created inside `/data` and contains `Hello, Bhavesh!`.
- Since `/data` is a **bind-mounted** directory, the file is stored on the **host machine** at `C:/Users/bhave/docker_data/testfile.txt`.

---

### ✅ Step 3: Verifying the File Exists
To check if the file exists inside the container, run:
```sh
docker exec -it alpine_with_bind_mount sh -c "cat /data/testfile.txt"
```

#### 📌 Expected Output:
```
Hello, Bhavesh!
```
This confirms that the file was successfully created and is accessible inside the container. 🎉

---

### 🗑 Step 4: Removing the Container
Now, remove the container:
```sh
docker rm -f alpine_with_bind_mount
```

#### 🔍 What Happens?
- The container is **forcefully stopped and removed**.
- However, **testfile.txt remains** in the bind-mounted directory on the host machine.

---

### 🔄 Step 5: Creating a New Container with the Same Bind Mount
Start a **new container** and attach the same bind-mounted directory:
```sh
docker run -dit --name new_alpine -v C:/Users/bhave/docker_data:/data alpine sh
```

#### 🔍 What Happens?
- A **new container** named `new_alpine` is created.
- The same bind-mounted directory (`C:/Users/bhave/docker_data`) is mounted to `/data`.

---

### 🔎 Step 6: Verifying File Persistence
Check if `testfile.txt` still exists inside the new container:
```sh
docker exec -it new_alpine sh -c "cat /data/testfile.txt"
```

#### 📌 Expected Output:
```
Hello, Bhavesh!
```
This confirms that **bind mounts persist data even after a container is removed**. 🔥

---
---
![Screenshot 2025-03-14 072548](https://github.com/user-attachments/assets/f6cf5fa3-7a51-4ace-b637-68100f318f2f)
---

## 🎯 Conclusion
✅ Bind mounts **allow data persistence** across multiple container instances.  
✅ **Deleting a container** does not remove data stored in the bind-mounted directory.  
✅ Any **new container** with the same mount can **access previous container data**.  
✅ Useful for **sharing files** between containers and persisting data beyond a container’s lifecycle.  

---

## 🚀 Next Steps
🔹 Experiment with **named volumes** (`docker volume create`) for better storage management.  
🔹 Try using **bind mounts** with different container images.  
🔹 Explore how **permissions impact** bind-mounted files across host and container.  

This experiment showcases the **power of bind mounts** in Docker. Keep exploring, and happy coding! 🚀
