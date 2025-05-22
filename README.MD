# FTP Server Helm Chart (Passive Mode - Single Port)

## ⚙️ Prerequisites

- [Minikube](https://minikube.sigs.k8s.io/)
- [Helm](https://helm.sh/)
- [lftp](https://lftp.yar.ru/) installed locally
- Docker Driver for Minikube
---

## 🚀 Installation And Testing

### 1. Deploy the FTP server

```bash
helm install ftp ./ftp-server-chart
```

### 2. Port-forward required ports

```bash
kubectl port-forward svc/ftp-ftp-server-chart 2121:21
kubectl port-forward svc/ftp-ftp-server-chart 21100:21100
```

### 3. Connect using `lftp`

```bash
lftp -u testuser,testpass localhost -p 2121
```

Inside `lftp`:

```bash
ls
mkdir test
cd test
put file.txt
get file.txt
```

---

## 📡 Port Forwarding Requirements (FTP Passive Mode Explained)

The FTP protocol uses two channels:

The FTP server uses port 21 for control connections (handling login and commands like ls, put) and port 21100 for passive data transfers (used for uploading/downloading files and listing directories).

You must forward **both ports** because:
- `port-forward` exposes only explicitly specified ports
- FTP clients follow the PASV response to open a new connection to a separate port (21100 in this case)

If you only forward port 21:
- ✅ Login will work
- ❌ `ls`/`put`/`get` will fail due to refused data connection

---

🛠️ Room for Improvements

Reduce Required Port Forwards

Implement SOAPS (Secrets of Application Password Storage)
Avoid hardcoding FTP credentials in environment variables or Helm values.
