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

## Set up Let‘s Encrypt

cert-manager supports many issuing methods: Let's Encrypt is only one of them.
To have Let’s Encrypt provide your certs, you need to create an Issuer
(namespace-scoped) or a [ClusterIssuer] (cluster-wide) resource on Kubernetes.


First, set your email address in a variable (this is to get expiry notifications
from Let's Encrypt, and is required by their Terms of Service):

    export EMAIL=ahmet@example.com

Then run this to deploy the Issuer manifests (the command below will populate
your email address [in the manifest file][manifest]):

    curl -sSL https://rawgit.com/ahmetb/gke-letsencrypt/master/yaml/letsencrypt-issuer.yaml | \
        sed -e "s/email: ''/email: $EMAIL/g" | \
        kubectl apply -f-

You will see output:

    clusterissuer "letsencrypt-staging" created
    clusterissuer "letsencrypt-prod" created


Let's Encrypt has both staging and production endpoints. You should use the
staging environment to test the automation out. Once you get things working, you
can switch to the production environment.

For this tutorial, we'll dive straight into using the `letsencrypt-prod` issuer.


[cert-manager]: https://github.com/jetstack/cert-manager/
[ClusterIssuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[manifest]: yaml/letsencrypt-issuer.yaml

-----

**Next:** [Deploy a sample web app on a domain name &rarr;](40-deploy-an-app.md)



![Google Analytics](https://ga-beacon.appspot.com/UA-2609286-16/30-setup-letsencrypt?pixel)
