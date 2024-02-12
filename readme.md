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
```
$ sudo lsof -i -n -r 1 | grep "http "
kubelet      887            root   25u  IPv4 106685092      0t0  TCP 10.1.2.223:35994->216.58.214.68:http (ESTABLISHED)
kubelet      887            root   26u  IPv4 106684186      0t0  TCP 10.1.2.223:35990->216.58.214.68:http (ESTABLISHED)
kubelet      887            root   27u  IPv4 106686732      0t0  TCP 10.1.2.223:35978->216.58.214.68:http (ESTABLISHED)
kubelet      887            root   28u  IPv4 106685858      0t0  TCP 10.1.2.223:36004->216.58.214.68:http (ESTABLISHED)
kubelet      887            root   25u  IPv4 106743014      0t0  TCP 10.1.2.223:47020->216.58.214.164:http (ESTABLISHED)
kubelet      887            root   27u  IPv4 106743928      0t0  TCP 10.1.2.223:47046->216.58.214.164:http (ESTABLISHED)
kubelet      887            root   28u  IPv4 106742339      0t0  TCP 10.1.2.223:47060->216.58.214.164:http (ESTABLISHED)
```
