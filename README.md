# Self-hosted Supabase with Kubernetes

> [!NOTE]  
> This is a Draft. Not fully tested yet.

Create namespace for Supabase and set it as default

```sh
kubectl create ns supabase
kubectl config set-context --current --namespace=supabase
```

## Postgres

Edit PVC in `postgres/postgres.yaml`

```sh
kubectl create secret generic supabase-db  --from-literal=username='postgres'  --from-literal=password='YOUR-DB-PASS'
```

> [!IMPORTANT]
> Don't use provided keys in production environments.
>
> Generate JWT keys here https://supabase.com/docs/guides/self-hosting/docker#generate-api-keys

```sh
kubectl create secret generic supabase-jwt \
    --from-literal=secret='x2zhph7hixlm99chy3v1ad2cfemvym6pu25pr0wf' \
    --from-literal=anonKey='eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.ewogICJyb2xlIjogImFub24iLAogICJpc3MiOiAic3VwYWJhc2UiLAogICJpYXQiOiAxNzEyMTc4MDAwLAogICJleHAiOiAxODY5OTQ0NDAwCn0.GcZn--EiwXXV88DHlOjD3m9NNCjVP2McMAO9WwvityI' \
    --from-literal=serviceKey='eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.ewogICJyb2xlIjogInNlcnZpY2Vfcm9sZSIsCiAgImlzcyI6ICJzdXBhYmFzZSIsCiAgImlhdCI6IDE3MTIxNzgwMDAsCiAgImV4cCI6IDE4Njk5NDQ0MDAKfQ.FBB5oOrnKsQYrD6NzNr34Y3zf_MBUJ7dPSgm2Rk4I30'
```

```sh
kubectl apply -f postgres/postgres-conf.yaml
kubectl apply -f postgres/postgres.yaml
```

## Meta (RESTful API for managing PostgreSQL)

```sh
kubectl apply -f meta.yaml
```

## PostgRest (RESTful API for PostgreSQL)

```sh
kubectl apply -f rest.yaml
```

## Kong (Routing and REST API authorization)

This service is an entry point to access all Supabase services. Don't expose other services if you don't know what are you doing.

Setup NodePort/LoadBalancer for Service in `kong/service.yaml` or use `kong/ingress.yaml`

Create secret for dashboard basic authentication

```sh
kubectl create secret generic supabase-kong  --from-literal=username='supabase'  --from-literal=password='dashboard-password'
```

```sh
kubectl apply -f kong
```

## Realtime (Broadcast messages about Postgres changes)

```sh
kubectl apply -f realtime.yaml
```

## Storage (S3 compatible file storage)

Edit PVC in `storage.yaml`

```sh
kubectl apply -f storage.yaml
```

## Imgproxy (REST API for cropping, resizing and rotating images)

```sh
kubectl apply -f imgproxy.yaml
```

## Auth (User registration and authentication)

Set `API_EXTERNAL_URL` and smtp variables in `auth.yaml`

```sh
kubectl apply -f auth.yaml
```

## Edge functions

```sh
kubectl apply -f functions
```

## Analytics (Logflare)

```sh
openssl rand -hex 20
e32e83530e9981ee07b9e0757d7bbbccef9c2ef9
```

```sh
kubectl create secret generic supabase-logflare --from-literal=apiKey='e32e83530e9981ee07b9e0757d7bbbccef9c2ef9'
```

```sh
kubectl apply -f analytics.yaml
```

## Studio (Supabase Dashboard)

Set `SUPABASE_URL` in `studio.yaml`

```sh
kubectl apply -f studio.yaml
```

## Vector (optional) (Collect, transform, and route logs)

Uses hostPath volumes to access pods logs

```sh
kubectl apply -f vector
```
