
# 🧠 Code Pilot — Cloud-based Online IDE

This is a scalable, containerized Replit-like online IDE built with Node.js, Docker, Kubernetes, and WebSocket terminals. Users can write, run, and preview code — even long-running web servers — in isolated cloud environments.

---

## 🌐 Architecture Overview

### Key Services:

| Service         | Description |
|----------------|-------------|
| **Orchestrator** | Creates and manages user environments (Kubernetes pods, services, ingresses) |
| **Runner**       | Runs inside each pod; exposes terminal access, file management, and code execution |
| **Frontend**     | Connects to runner over WebSocket, renders terminal and file editor |

Each user’s code runs inside an isolated Docker container on Kubernetes. WebSocket is used to power terminal interaction.

---

## 📁 Folder Structure

```
code-pilot/
├── frontend/                # Frontend UI for the IDE (terminal, file editor, preview)
├── init-service             # Service that initializes backend environments
├── k8s/                     # Kubernetes manifests and cluster configuration
├── orchestrator-simple/     # Orchestrator service to manage REPL pods, services, ingresses
└── runner/                  # Runner container that executes code, handles terminal + file I/O
```

---

## 🚀 Features

✅ Interactive browser terminal (via `xterm.js` & `node-pty`)  
✅ Real-time file editing and syncing  
✅ Long-running server support (`npm run dev`, etc.)  
✅ Preview user web apps at unique URLs  
✅ Fully isolated per-user environments (Kubernetes pods)  
✅ Secure and scalable

---

## ⚙️ Technologies Used

- 🐳 Docker
- ☸️ Kubernetes
- 🔌 WebSockets (`socket.io`)
- 🐚 Pseudo Terminals (`node-pty`)
- 🧠 Node.js + TypeScript
- 📦 Amazon S3 (for file sync)
- ✨ NGINX Ingress Controller

---

## 🧪 Running the Project (Dev)

> You must have `kubectl`, `docker`, and access to a Kubernetes cluster.

### 1. Start the Orchestrator Service
```bash
cd orchestrator-simple
npm install
npm run dev
```

### 2. Runner Image (used inside pods)
```bash
cd runner
docker build -t ./runner
docker push ./runner
```

Ensure this image matches what's referenced in `service.yaml`.

### 3. Deploy REPL Pod Manually (for testing)
```bash
kubectl apply -f service.yaml
```

Set the correct environment variables in your cluster for:
- `AWS_ACCESS_KEY_ID`
- `AWS_SECRET_ACCESS_KEY`
- `S3_BUCKET`
- `S3_ENDPOINT`

---

## 🔁 How It Works

1. User requests a new REPL → hits **Orchestrator**
2. Orchestrator generates & deploys pod/service/ingress
3. Init container in pod fetches starter code from S3
4. Runner container exposes:
   - Port `3001`: WebSocket (terminal)
   - Port `3000`: User app (e.g., React, Flask)
5. Browser connects via `wss://repl-id.domain.com` for terminal
6. Terminal commands (like `npm run dev`) launch server
7. Ingress exposes `http://repl-id.domain.com` to preview

---

