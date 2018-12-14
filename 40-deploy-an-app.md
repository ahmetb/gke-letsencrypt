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

Save [sample-ingress-tls](yaml/sample-ingress-tls.yaml) manifest, which will request
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

**Next:** [Get a certificate for your domain name &rarr;](50-get-a-certificate.md)


[tut]: https://cloud.google.com/kubernetes-engine/docs/tutorials/configuring-domain-name-static-ip#step_2b_using_an_ingress
[manifest]: yaml/sample-app.yaml

-----


![Google Analytics](https://ga-beacon.appspot.com/UA-2609286-16/40-deploy-an-app?pixel)
