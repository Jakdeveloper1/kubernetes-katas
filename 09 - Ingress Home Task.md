## ****Ingress** in Kubernetes?** - Class/Home Task

When you deploy apps in Kubernetes (K8s), they usually run inside the cluster, hidden from the outside world.  
If you want people (users, browsers, mobile apps) to **access** your app from the internet, you need a way to **expose** it.

**Using a Service (Without Ingress)**
Kubernetes uses **Services** to expose applications to the outside world. There are different types of Services in Kubernetes, but the most common way to expose an app to the outside world is using a **LoadBalancer** or **NodePort** service.

### **How does this differ from Ingress?**

-   **NodePort/LoadBalancer** expose **whole services** to the outside world.
-   **Ingress** is more flexible because it allows **fine-grained routing** of requests based on **URL paths** and **hosts**. For example, you can route traffic to different services within the same cluster (e.g., `/frontend` -> `frontend-service`, `/api` -> `backend-service`).
    

**So without Ingress:**
-   Your service might be exposed, but you can’t have fine-grained routing based on URL paths or hostnames.
-   It’s simpler for small applications, but for complex routing or multiple services, you’d typically want Ingress.
-   You would need to expose each service separately (complicated).
- **Ingress** takes care of routing external traffic to the correct Service, based on rules you define (such as URL paths or hostnames).
    
**With Ingress:**
-   **One public IP**.
-   **One domain** (`yourshop.com`).
-   Ingress handles routing to the correct backend automatically.

### Example Scenario:
Let's assume you have the following setup:
-   **Deployment** for your web application (`nginx-deployment`)
-   A **Service** (`nginx-service`) to expose your nginx application inside the cluster.
-   **Ingress** that routes traffic to `nginx-service`.
    
### 1. **Create a Deployment**:
First, you create a Deployment for your application (nginx in this case).

    kubectl create deployment nginx-deployment --image=nginx

### 2. **Expose the Deployment with a Service**:
Next, you expose the Deployment using a **Service**. This service will be the one that receives traffic from Ingress.

    kubectl expose deployment nginx-deployment --type=ClusterIP --name=nginx-service --port=80

### 3. **Create an Ingress Resource**:
Now, you define an Ingress that will route traffic from the outside world to the `nginx-service`.

    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: nginx-ingress
    spec:
      rules:
        - host: yourdomain.com
          http:
            paths:
              - path: /
                pathType: Prefix
                backend:
                  service:
                    name: nginx-service
                    port:
                      number: 80


### What Happens Here:
-   **Ingress** defines that any traffic to `yourdomain.com` should be forwarded to the `nginx-service` on port 80.
-   The **Service** (`nginx-service`) forwards the traffic to the **Pods** (your nginx containers) created by the **Deployment**.
    

### **Ingress Controller**:
For the Ingress to actually work, you need an **Ingress Controller** (e.g., NGINX Ingress Controller) running in your cluster. The controller watches for changes in Ingress resources and handles the traffic routing based on the rules you've defined.
