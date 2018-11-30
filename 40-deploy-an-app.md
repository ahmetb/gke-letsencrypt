<!--
Copyright 2018 Google Inc.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    https://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
-->

# Deploy a sample web app on a domain name

A brief copy of the [GKE static IP tutorial][tut] is below.

First, deploy the [sample web application manifest](yaml/sample-app.yaml) which
contains the Deployment and a NodePort Service (required by Ingress):

    kubectl apply -f https://raw.githubusercontent.com/ahmetb/gke-letsencrypt/master/yaml/sample-app.yaml

Create a static IP called to (will be used in the Ingress):

    gcloud compute addresses create helloweb-ip --global

**Update your domain name records** (at your domain registrar or DNS
provider) with this IP address.

    gcloud compute addresses describe helloweb-ip --global --format 'value(address)'

Save [sample-ingress-tls](yaml/sample-ingress-tls) manifest, which will request
a certificate for a domain name from the `letsencrypt-prod` issuer:

    curl -O https://raw.githubusercontent.com/ahmetb/gke-letsencrypt/master/yaml/sample-ingress-tls.yaml

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: helloweb
  annotations:
    kubernetes.io/ingress.global-static-ip-name: helloweb-ip
    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
    certmanager.k8s.io/acme-http01-edit-in-place: "true"
  labels:
    app: hello
spec:
  backend:
    serviceName: helloweb-backend
    servicePort: 8080
  tls:
  - secretName: www-dogs-com-tls
    hosts:
    - www.dogs.com
```


Modify a few things before deploying this manifest:

- Replace `www.dogs.com` with your domain name
- Replace `dogs-com-tls` (will be used to create the TLS Secret) with a name
  that is suitable
- Replace `helloweb` with the Ingress name that your website is running on

Then apply this manifest:

    kubectl apply -f sample-ingress-tls.yaml

Go get a cup of coffee. :coffee:

Now, while you're waiting, let's take a look at what is cert-manager doing
behind the scenes:

0. cert-manager creates `Certificate` CRD which will trigger an issuing progress. You can check this out `kubectl get Certificate`.
0. cert-manager updates your Ingress to handle `GET /.well-known/acme-challenge/*` requests with a temporary Service it created in your cluster. This will be used to prove that you own the domain name.
0. You can run `kubectl get ingress -o=yaml helloweb` to see how it is modified.
0. Since Ingress is updated, Google Cloud Load Balancer is being updated too!
0. It will take about 10-15 minutes for the changes to take effect.
0. After Ingress changes take effect, cert-manager will notice that the `/.well-known/*` URL starts working.
0. At that time you will be able to visit your non-https website http://www.dogs.com
0. cert-manager will ask Let's Encrypt to provide certificates.
0. Let's Encrypt will come and visit `/.well-known/*` URL to see the proof that you own the domain name.
0. Let's Encrypt will provide you certificates.
0. cert-manager will save the TLS certificates to the specified `spec.tls[0].secretName`. You can check this out `kubectl get secret www-dogs-com-tls`.
0. Google Cloud Load Balancer is being updated with this new TLS.
0. Voila! You can visit https://www.dogs.com on your browser.


:warning: The Google HTTP(s) Load Balancer can take 10-15 minutes; during that time you'll see 404s, and other
server errors. Do not panic, be patient!

cert-manager will continue to run in the background and renew your certificates
every 60 days (or so), because all Let's Encrypt certificates expire every 90
days! So, don't uninstall cert-manager if you start using this certificate to
serve traffic.


**Did this work for you?** If so, please take time to ✩Star the repository on
GitHub.


[tut]: https://cloud.google.com/kubernetes-engine/docs/tutorials/configuring-domain-name-static-ip#step_2b_using_an_ingress
[manifest]: yaml/sample-app.yaml

-----

**Next:** [Cleanup &rarr;](99-cleanup.md)


![Google Analytics](https://ga-beacon.appspot.com/UA-2609286-16/40-deploy-an-app?pixel)
