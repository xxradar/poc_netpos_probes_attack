```
kubectl create ns prod-nginx
```
```
kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  namespace: prod-nginx
  labels:
    app: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
        env: prod
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 30  # Delay before the first probe
          periodSeconds: 10        # How often to perform the probe
          timeoutSeconds: 5        # When the probe times out
          successThreshold: 1      # Minimum consecutive successes for the probe to be considered successful after having failed
          failureThreshold: 3      # When to restart the container
        readinessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 15  # Delay before the first probe
          periodSeconds: 10        # How often to perform the probe
          timeoutSeconds: 5        # When the probe times out
          successThreshold: 1      # Minimum consecutive successes for the probe to be considered successful
          failureThreshold: 3      # How many times to retry upon failure before giving up
EOF
```
```
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: my-nginx-clusterip
  namespace: prod-nginx
spec:
  ports:
  - port: 80
    protocol: TCP
  selector:
    app: nginx
EOF
```
```
kubectl apply -n prod-nginx -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: default-deny
spec:
  podSelector:
    matchLabels: {}
  policyTypes:
   - Ingress
   - Egress
EOF
```
```
kubectl apply -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment-2
  namespace: prod-nginx
  labels:
    app: nginx-deployment-2
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
        env: prod
    spec:
      containers:
      - name: nginx
        image: nginx
        ports:
        - containerPort: 80
        livenessProbe:
          httpGet:
            path: /
            port: 80
          initialDelaySeconds: 30  # Delay before the first probe
          periodSeconds: 10        # How often to perform the probe
          timeoutSeconds: 5        # When the probe times out
          successThreshold: 1      # Minimum consecutive successes for the probe to be considered successful after having failed
          failureThreshold: 3      # When to restart the container
        readinessProbe:
          httpGet:
            host: www.google.com
            path: /
            port: 80
          initialDelaySeconds: 15  # Delay before the first probe
          periodSeconds: 10        # How often to perform the probe
          timeoutSeconds: 5        # When the probe times out
          successThreshold: 1      # Minimum consecutive successes for the probe to be considered successful
          failureThreshold: 3      # How many times to retry upon failure before giving up
EOF
```
