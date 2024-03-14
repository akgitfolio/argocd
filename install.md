```bash
k3d cluster create my-cluster --api-port 6443 -p 8080:80@loadbalancer --agents 2
```

## Step 2: Install ArgoCD

1. **Create a Namespace for ArgoCD**:

   ```bash
   kubectl create namespace argocd
   ```

2. **Install ArgoCD**:

   ```bash
   kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
   ```

3. **Wait for ArgoCD Pods to be Ready**:

   ```bash
   kubectl get pods -n argocd
   ```

   Ensure all pods are in the `Running` state.

## Step 3: Access ArgoCD UI

1. **Expose ArgoCD Server**:

   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```

2. **Get the Initial Admin Password**:

   ```bash
   kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 --decode
   ```

3. **Login to ArgoCD UI**:
   Open a browser and go to `https://localhost:8080`. Use `admin` as the username and the password retrieved in the previous step.

## Step 4: Deploy an Application

1. **Create a Git Repository**:
   Ensure you have a Git repository with your Kubernetes manifests. For example, a repository with a simple Nginx deployment.

2. **Create an ArgoCD Application**:

   ```bash
   argocd app create nginx-app \
     --repo https://github.com/your-repo/nginx-deployment.git \
     --path . \
     --dest-server https://kubernetes.default.svc \
     --dest-namespace default
   ```

3. **Sync the Application**:

   ```bash
   argocd app sync nginx-app
   ```

4. **Verify the Deployment**:

   ```bash
   kubectl get pods -n default
   ```

   Ensure the Nginx pod is running.
