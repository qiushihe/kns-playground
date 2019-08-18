# Setup Ingress Controller with OAuth Proxy

TODO:

* Install ingress controller with `--namespace kube-system`
* Figure out how the fuck to make auth-forwarding work with k3s/k3d

## OAuth Provider/App

Create a GitHub OAuth app following instructions from: https://pusher.github.io/oauth2_proxy/auth-configuration#github-auth-provider

## Oauth Secrets

Store the "Client ID" and "Client Secret" from the OAuth app/provider in secrets:

```
$ kubectl create secret generic hello-first-oauth-secrets --from-literal=clientID=[CLIENT ID] --from-literal=clientSecret=[CLIENT SECRET] --from-literal=cookieSecret=[COOKIE SECRET]
```

## Nginx Ingress Controller

Install Nginx Ingress Controller with Helm:

```
$ helm install --name nginx-ingress -f ./custom-values/nginx-ingress.yml stable/nginx-ingress
```

## Dummy App

Create a dummy app:

```
$ kubectl apply -f ./manifests/hello-first.yml
```

... then add a host alias to `/etc/hosts`:

```
127.0.0.1 first.hello.com
```

Verify the app is running and accessible:

```
$ kubectl port-forward svc/nginx-ingress-controller 8080:80
```

Open URL: http://first.hello.com:8080

## OAuth Protected Dummy App

Create an Oauth2-Proxy app for the dummy app:

```
$ kubectl apply -f ./manifests/hello-first-oauth2-proxy.yml
```

... then add a host alias to `/etc/hosts`:

```
127.0.0.1 first.hello-with-auth.com
```

Verify the protected app is running and accessible:

```
$ kubectl port-forward svc/nginx-ingress-controller 8080:80
```

Open URL: http://first.hello-with-auth.com:8080
