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

# Install cert-manager

[cert-manager] is "pre-stable" software,so use it at your own risk. Clone its repository and deploy the Helm chart:

    git clone https://github.com/jetstack/cert-manager.git

    cd cert-manager

    helm install --name cert-manager \
        --namespace kube-system contrib/charts/cert-manager


[cert-manager]: https://github.com/jetstack/cert-manager/

-----

**Next:** [Set up Let’s Encrypt &rarr;](30-setup-letsencrypt.md)
