# 📦 FTP Server Helm Chart (Passive Mode - Single Port)

## ⚙️ Prerequisites

- [Minikube](https://minikube.sigs.k8s.io/)
- [Helm](https://helm.sh/)
- [`lftp`](https://lftp.yar.ru/) installed locally
- Docker driver enabled in Minikube

---

## 🚀 Installation & Usage

### 1. Enable Ingress in Minikube

```bash
minikube addons enable ingress
```

### 2. Install NGINX Ingress Controller

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update

helm install ingress-nginx ingress-nginx/ingress-nginx \
  --namespace ingress-nginx --create-namespace \
  --set controller.publishService.enabled=true \
  --set controller.extraArgs.update-status=false
```

### 3. Deploy FTP server with Helm

```bash
helm install ftp ./ftp-server-chart
```

### 4. Port-forward FTP ports for testing

```bash
kubectl port-forward svc/ftp-ftp-server-chart 2121:21
kubectl port-forward svc/ftp-ftp-server-chart 21100:21100
```

> ⚠️ Both ports are required due to FTP passive mode data transfer.

### 5. Update `/etc/hosts` for Ingress

Add the following line to your local `/etc/hosts` file:
```
127.0.0.1 ftp.local
```

### 6. Access files over HTTP

Visit:
```
http://ftp.local/
```

You should see an auto-index listing of files uploaded via FTP.

### 7. Connect using `lftp`

```bash
lftp -u testuser,testpass -p 2121 localhost
```

Then:
```lftp
mkdir test
cd test
put file.txt
ls
get file.txt
```

---

## 📡 Why You Need Both Ports

- **Port 21**: Control channel (login, commands)
- **Port 21100**: Passive data channel (file transfers, `ls`, `put`, `get`)

Without both, FTP will connect but fail during any data action.

---

## 🛠️ Improvements You Can Make

- **Remove plaintext credentials** from `values.yaml` by using Helm secrets or external Secret references.
- **Enable persistent storage with PVCs** (already supported via Helm).
- **Add HTTPS with cert-manager** for `ftp.local`.