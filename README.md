# 🌐 Cert-Manager: Securing Kubernetes with TLS  

## 🤔 What is Cert-Manager?  
Cert-Manager is a Kubernetes add-on that automates the management of TLS certificates. It supports issuers like Let’s Encrypt, HashiCorp Vault, and private PKI. With Cert-Manager, you can:

✅ Automatically request and renew certificates.  
✅ Secure your apps over HTTPS.  
✅ Integrate with multiple certificate providers.  

---

## 🛠️ Why Cert-Manager?  
- 🔒 **Automated Certificates**: No manual renewals or setup.  
- ⚡ **Enhances Security**: HTTPS for your apps with ease.  
- 🌍 **Flexible Issuers**: Works with Let’s Encrypt, Vault, and more.  

---

## 📚 Tutorial Overview  
In this guide, you will:  
1️⃣ **Start a Kubernetes cluster with Minikube.**  
2️⃣ Install Cert-Manager using Helm.  
3️⃣ Create a `ClusterIssuer` for Let’s Encrypt.  
4️⃣ Secure your app by updating Ingress with TLS certificates.  

---

### ✅ Prerequisites  
Before you begin, ensure you have:  
- 🖥️ A computer with Minikube installed ([Installation Guide](https://minikube.sigs.k8s.io/docs/start/)).  
- 🐳 **kubectl** installed and configured ([kubectl Installation](https://kubernetes.io/docs/tasks/tools/)).  
- 🎛️ Helm (version 3+) installed ([Helm Installation](https://helm.sh/docs/intro/install/)).  
- 📡 A domain name (if testing HTTPS with external access).  
- 💡 Basic understanding of Kubernetes concepts like Ingress and namespaces.

---

### 🚀 Step 1: Start Your Minikube Cluster  
Run the following command to start a local Kubernetes cluster with Minikube:  

```bash
minikube start --kubernetes-version v1.30 --memory 8192 --cpus 2 --driver=docker
```
This creates a Kubernetes environment suitable for testing Cert-Manager. Once it’s running, verify with:

```bash
kubectl get nodes
```
You should see a ready node listed! ✅

---

## 🚀 Installing Cert-Manager with Helm  

### 1️⃣ Add the Cert-Manager Helm Repository  
Helm is a Kubernetes package manager that simplifies deployments. Cert-Manager's Helm chart is hosted by Jetstack. Add it to your repositories:  

```bash
helm repo add cert-manager https://charts.jetstack.io
```

### 2️⃣ Update the Helm Repository Cache
Ensure you’re using the latest chart version by updating the repository cache:

```bash
helm repo update
```

### 3️⃣ Install Cert-Manager
Cert-Manager requires CustomResourceDefinitions (CRDs), which define additional Kubernetes resources like Certificate and Issuer. Install Cert-Manager in your cluster with:

```bash
helm install cert-manager cert-manager/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version 1.16.1 \
  --set installCRDs=true
```

📝 Key Points:

CRDs: These extend Kubernetes to recognize custom resources like certificates.
The --set installCRDs=true flag ensures these CRDs are installed automatically.
Cert-Manager will be deployed in the cert-manager namespace.

✅ Confirmation
After installation, you’ll see an output like this:

```bash
NAME: cert-manager  
NAMESPACE: cert-manager  
STATUS: deployed  
```

This confirms Cert-Manager is installed and ready to manage your TLS certificates! 🔐