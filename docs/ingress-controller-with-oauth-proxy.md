# Ingress Controller with OAuth Proxy

TODO: Figure out how the fuck to make auth-forwarding work with k3s/k3d

## Ingress Controller

**Option A:** Install Traefik ingress controller with Helm:

```
$ helm install --namespace kube-system --name traefik-ingress -f ./custom-values/traefik-ingress.yml stable/traefik
```

View the Traefik dashboard with port forwarding:

```
$ kubectl port-forward -n kube-system svc/traefik-ingress-dashboard 8080:80
```

Open URL: http://localhost:8080

**Option B:** Install Nginx ingress controller with Helm:

```
$ helm install --namespace kube-system --name nginx-ingress -f ./custom-values/nginx-ingress.yml stable/nginx-ingress
```

## Dummy App

Create a dummy app:

```
$ kubectl apply -f ./manifests/hello-first/deployment.yml
$ kubectl apply -f ./manifests/hello-first/service.yml
```

**For Traefik ingress controller:**

```
$ kubectl apply -f ./manifests/hello-first/ingress-traefik.yml
```

**For Nginx ingress controller:**

```
$ kubectl apply -f ./manifests/hello-first/ingress-nginx.yml
```

Add a host alias to `/etc/hosts`:

```
127.0.0.1 first.hello.com
```

### Verify the app is running and accessible:

**For Traefik ingress controller:**

```
$ kubectl port-forward -n kube-system svc/traefik-ingress 8080:80
```

**For Nginx ingress controller:**

```
$ kubectl port-forward -n kube-system svc/nginx-ingress-controller 8080:80
```

Open URL: http://first.hello.com:8080

## OAuth Provider/App

Create a GitHub OAuth app following instructions from: https://pusher.github.io/oauth2_proxy/auth-configuration#github-auth-provider

### Oauth Secrets

Store the "Client ID" and "Client Secret" from the OAuth app/provider in secrets:

```
$ kubectl create secret generic hello-first-oauth2-proxy-secrets --from-literal=clientID=[CLIENT ID] --from-literal=clientSecret=[CLIENT SECRET] --from-literal=cookieSecret=[COOKIE SECRET]
```

## OAuth Protected Dummy App

Create an Oauth2-Proxy app for the dummy app:

```
$ kubectl apply -f ./manifests/hello-first/oauth2-proxy/deployment.yml
$ kubectl apply -f ./manifests/hello-first/oauth2-proxy/service.yml
```

**For Traefik ingress controller:**

```
$ kubectl apply -f ./manifests/hello-first/oauth2-proxy/ingress-traefik.yml
```

**For Nginx ingress controller:**

```
$ kubectl apply -f ./manifests/hello-first/oauth2-proxy/ingress-nginx.yml
```

Add a host alias to `/etc/hosts`:

```
127.0.0.1 first.hello-with-auth.com
```

### Verify the protected app is running and accessible:

**For Traefik ingress controller:**

```
$ kubectl port-forward -n kube-system svc/traefik-ingress 8080:80
```

**For Nginx ingress controller:**

```
$ kubectl port-forward -n kube-system svc/nginx-ingress-controller 8080:80
```

Open URL: http://first.hello-with-auth.com:8080
