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

Then, deploy the Ingress using [this manifest](yaml/sample-ingress.yaml):

    kubectl apply -f https://raw.githubusercontent.com/ahmetb/gke-letsencrypt/master/yaml/sample-ingress.yaml

Run `kubectl get ingress` until you see the static IP address assigned to the
load balancer.

Visit this IP address on your browser.

:warning: **Wait until visiting the IP address works:** The Google HTTP
Load Balancer can take 5-10 minutes; during that time you'll see 404s, and other
server errors. Do not panic, be patient!

After it works, **update your domain name records** (at your domain registrar or DNS
provider) with this IP address.

**Verify the domain name works** in your browser before proceeding &mdash;use
`http://`, as we don't have `https://` yet.


[tut]: https://cloud.google.com/kubernetes-engine/docs/tutorials/configuring-domain-name-static-ip#step_2b_using_an_ingress
[manifest]: yaml/sample-app.yaml

-----

**Next:** [Get a certificate for your domain name &rarr;](50-get-a-certificate.md)
