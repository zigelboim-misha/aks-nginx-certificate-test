# Nginx ingress with Certificates

## Deploying everything

Deploying the `nginx`:

```bash
helm upgrade --install nginx-ingress ingress-nginx/ingress-nginx \
  --namespace ingress-nginx --create-namespace \
  --set controller.ingressClassResource.name=external-nginx \
  --set controller.service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-health-probe-request-path"=/healthz \
  --set controller.watchIngressWithoutClass=true \
  --set controller.extraArgs.ingress-class=external-nginx
```

Deploying the `cert-manager` to do the heavy lifting for me:

```bash
helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.13.1 --set installCRDs=true
```

Create a new namespace:

```bash
kubectl create namespace test
```

Deploy the templates using `kubectl apply -f ./templates` in said namespace.

## Debugging whats going on

Now there is a need to debug the new created ingress:

- `kubectl describe certificate`
- `kubectl describe certificaterequest`
- `kubectl describe order`
- `kubectl describe challenge`

There you will see that the certificate is having problems.
We need to create a new `A Record` in our 3rd party domain host (like `Namecheap`).
It shall contain the sub-domain we want to use along side the ingress `ip`.

You can use the [DNS Checker](https://www.whatsmydns.net/#A/google.com) to check if the `A Record` works.
