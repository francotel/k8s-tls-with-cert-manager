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

```bash
minikube addons enable ingress
minikube addons enable metallb

minikube addons list

$ service=$(minikube service my-app --url)
$ while sleep 0.1; do curl "$service"; done

```

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
  --set crds.enabled=true
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

```bash
❯ kubectl get pods -n cert-manager

NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-7df78d6dfb-dgnc8              1/1     Running   0          79s
cert-manager-cainjector-7895f6ff5c-nhcxp   1/1     Running   0          79s
cert-manager-webhook-5d7fc67f7b-nsv2m      1/1     Running   0          79s
```
This confirms Cert-Manager is installed and ready to manage your TLS certificates! 🔐

---

## 🔐 Let's Encrypt and ClusterIssuer in Kubernetes

### What is Let's Encrypt?
[Let's Encrypt](https://letsencrypt.org/) is a free, automated, and open certificate authority (CA) that provides SSL/TLS certificates. These certificates are used to secure communication over the internet by encrypting data between clients and servers. With Let's Encrypt, you can automate certificate management in Kubernetes for your applications.

### Why use Let's Encrypt in Kubernetes?
- **Automated Certificate Management**: Simplifies the generation, renewal, and revocation of certificates.
- **Security**: Encrypts traffic to and from your Kubernetes applications.
- **Scalability**: Works seamlessly with Kubernetes ingress controllers.
- **Cost-Effective**: Certificates are free!

### Workflow: Kubernetes and Let's Encrypt

1. **ClusterIssuer or Issuer Configuration**:
   - These are Kubernetes resources that define how Cert-Manager communicates with Let's Encrypt to request certificates.
   - `ClusterIssuer` is cluster-wide and can issue certificates for any namespace.
   - `Issuer` is namespace-scoped and works only within its namespace.

2. **Certificate Request**:
   - Applications create an `Ingress` resource that references the `ClusterIssuer` or `Issuer`.
   - Cert-Manager uses this reference to request certificates from Let's Encrypt.

3. **Challenge Solving**:
   - Let's Encrypt validates that the domain requesting the certificate is owned by the client.
   - This is done using **solvers**, such as `HTTP01` or `DNS01`.

4. **Certificate Issuance**:
   - Upon successful validation, Let's Encrypt issues a certificate, which Cert-Manager stores in a Kubernetes `Secret`.

## Solvers: Types and Their Roles

- **HTTP01 Solver**:
  - Proves domain ownership by creating an HTTP resource on the application.
  - Let's Encrypt verifies by making a request to `http://<your-domain>/.well-known/acme-challenge/<token>`.
  - Typically used with Ingress controllers like NGINX or Traefik.

- **DNS01 Solver**:
  - Proves domain ownership by adding a DNS TXT record to the domain.
  - Useful for securing wildcard domains (e.g., `*.example.com`).

---

## 🌐 Easy Steps: Self-Signed TLS in Kubernetes

Here is how to use self-signed certificates for your services.

---

### 🛠️ Step 1: Create an Issuer

Issuer tells Kubernetes how to make the certificates.

### YAML File: `issuer.yaml`
```yaml
apiVersion: cert-manager.io/v1
kind: Issuer
metadata:
  name: selfsigned
  namespace: app
spec:
  selfSigned: {}
```

#### Command to Apply:

```bash
kubectl apply -f issuer.yaml

kubectl get issuers -n app
```

####  Test with Browser:
Go to https://francotel.com. You will see a warning for self-signed certificates.

Test with curl:

```bash
curl -k https://francotel.com
```

Check Logs in NGINX:

```bash
kubectl logs -n ingress-nginx -l app.kubernetes.io/name=ingress-nginx
```