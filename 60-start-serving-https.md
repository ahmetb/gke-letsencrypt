# Start serving HTTPS traffic

Now that you have a certificate, it’s time to use it!

You need to update your Ingress to add a `tls` section under the `spec` with the
`secretName` that stores the TLS certificate and the domain name.

If you've used [the sample mnanifest](yaml/sample-app.yaml) earlier, save the
[updated Ingress manifest](yaml/ingress-tls.yaml) to your computer, and name it
`ingress-tls.yaml`.

Change the `secretName` and the `hosts` fields with the values you used earlier:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: helloweb
  annotations:
    kubernetes.io/ingress.global-static-ip-name: helloweb-ip
  labels:
    app: hello
spec:
  tls:
  - secretName: www-dogs-com-tls
    hosts:
    - www.dogs.com
  backend:
    serviceName: helloweb-backend
    servicePort: 8080
```


Then apply it to the cluster to modify the existing `helloweb` Ingress:

    kubectl apply -f ingress-tls.yaml

Now, the TLS certificate is being added to your load balancer: this can take
5-10 minutes, too.

Now visit your domain name with `https://`. Again, wait until it works.

Once it works, it means you now have a website serving HTTPS with Let's Encrypt
certificates!

cert-manager will continue to run in the background and renew your certificates
every 60 days (or so), because all Let's Encrypt certificates expire every 90
days! So, don't uninstall cert-manager if you start using this certificate to
serve traffic.

----

**Next:** [Cleanup &rarr;](99-cleanup.md)
