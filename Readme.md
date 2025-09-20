
# How files are organized (suggested)

* `app-v1/index.html` — Version 1 (colorful, welcome note, simple calculator)
* `app-v2/index.html` — Version 2 (same as V1 + calendar)
* `Dockerfile` — single Dockerfile you can build per-version (use `--build-arg APP=app-v1` or copy correct folder)
* `deployment.yaml` — Kubernetes Deployment (set image)
* `service-nodeport.yaml` — Kubernetes NodePort service

---
# Build & run commands (local)

Assuming you are in the parent directory containing `Dockerfile`, `app-v1/`, `app-v2/`.

1. Build V1 image locally:

```bash
docker build --no-cache -t ocotoops-app:v1 --build-arg APP_DIR=app-v1 .
```

2. Run V1 container (map port 8080 on host):

```bash
docker run --rm -p 8080:80 --name ocotoops-v1 ocotoops-app:v1
# Open http://localhost:8080
```

3. Build V2:

```bash
docker build --no-cache -t ocotoops-app:v2 --build-arg APP_DIR=app-v2 .
```

4. Run V2:

```bash
docker run --rm -p 8081:80 --name ocotoops-v2 ocotoops-app:v2
# Open http://localhost:8081
```

5. To push images to a registry (example Docker Hub):

```bash
docker tag ocotoops-app:v1 yourdockerhubusername/ocotoops-app:v1
docker push yourdockerhubusername/ocotoops-app:v1
# same for v2
```

---

# Kubernetes manifests

Update the `image:` fields to your registry image (e.g., `yourdockerhubusername/ocotoops-app:v1`).

## deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ocotoops-app
  labels:
    app: ocotoops
spec:
  replicas: 2
  selector:
    matchLabels:
      app: ocotoops
  template:
    metadata:
      labels:
        app: ocotoops
    spec:
      containers:
      - name: ocotoops-container
        # Replace with your image (v1 or v2)
        image: yourdockerhubusername/ocotoops-app:v1
        imagePullPolicy: IfNotPresent
        ports:
        - containerPort: 80
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 5
          periodSeconds: 5
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 10
          periodSeconds: 10
```

> To switch to V2, change `image:` to `.../ocotoops-app:v2` and `kubectl apply -f deployment.yaml`.

## service-nodeport.yaml

This creates a NodePort service exposing the app on node port `30080`. You can change `nodePort` as needed (range depends on cluster, typical is 30000–32767).

```yaml
apiVersion: v1
kind: Service
metadata:
  name: ocotoops-nodeport
spec:
  type: NodePort
  selector:
    app: ocotoops
  ports:
  - port: 80
    targetPort: 80
    protocol: TCP
    nodePort: 30080
```

---

# Kubernetes deploy commands

1. Apply deployment and service:

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service-nodeport.yaml
```

2. Verify pods:

```bash
kubectl get pods -l app=ocotoops -w
```

3. Access:

* If running on a local minikube cluster:

  ```bash
  minikube service ocotoops-nodeport --url
  # or open http://<minikube-ip>:30080
  ```
* If on any k8s node, access `http://<node-ip>:30080`.

4. To update to v2:

```bash
# Update image in deployment.yaml or use kubectl set image
kubectl set image deployment/ocotoops-app ocotoops-container=yourdockerhubusername/ocotoops-app:v2 --record
# wait for rollout
kubectl rollout status deployment/ocotoops-app
```


