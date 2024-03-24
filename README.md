# Self-hosted Supabase with Kubernetes

> [!NOTE]  
> This is a Draft. Not fully tested yet.

### Create secrets

```sh
kubectl create secret generic supabase-db  --from-literal=username='postgres'  --from-literal=password='DB_PASSWORD'
```

Generate API keys here https://supabase.com/docs/guides/self-hosting/docker#generate-api-keys

```sh
kubectl create secret generic supabase-jwt \
    --from-literal=secret='' \
    --from-literal=anonKey='' \
    --from-literal=serviceKey=''
```

```sh
kubectl create secret generic supabase-logflare  --from-literal=apiKey='your-super-secret-and-long-logflare-key'
```

### Persistent Volumes

Edit PVC in postgres/postgres.yaml and storage.yaml

### Web access

Configure kong/service for ClusterIP/NodePort/LoadBalancer or kong/ingress.yaml to setup web access for Supabase
