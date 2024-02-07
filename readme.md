```
kubectl create ns prod-nginx
```
```
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
```

