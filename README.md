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


### Analogy 🍎🍏:

1. **You**: You have an apple store (your website). First, you buy apples and put them in your store (set up your website so people can see it).

2. **Cert-manager (Your helper)**: "Hello, Let’s Encrypt, I want a special gift (certificate) for my apple store, please."

3. **Let’s Encrypt (The certificate owner)**: "Sure! But first, I need to check that you really own the store. Put a secret label on one of the apples and make sure people can see it at this address: `http://yourstore.com/.well-known/acme-challenge/<CODE>`."

4. **Cert-manager**: "Done! Now the label is on the apple. Can you see it?"

5. **Let’s Encrypt**: "Yes, I see it! Now, here’s your gift (certificate) so your store is safe."

Now, your store is protected, and people can buy apples with confidence. 🍏🔐🎉

## Solvers: Types and Their Roles

- **HTTP01 Solver**:
  - Proves domain ownership by creating an HTTP resource on the application.
  - Let's Encrypt verifies by making a request to `http://<your-domain>/.well-known/acme-challenge/<token>`.
  - Typically used with Ingress controllers like NGINX or Traefik.

- **DNS01 Solver**:
  - Proves domain ownership by adding a DNS TXT record to the domain.
  - Useful for securing wildcard domains (e.g., `*.example.com`).

---