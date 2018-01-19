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

## Cleanup

Delete the Ingress, Service and Deployment:

    kubectl delete ingress,deployment helloweb
    kubectl delete service helloweb-backend

Delete the reserved static IP address (unused static IPs are not free!):

    gcloud compute addresses delete helloweb-ip --global -q

If you'd like to uninstall cert-manager deployment:

    helm del --purge cert-manager

If you'd like to uninstall Helm:

    helm reset

Do not forget to delete the TLS certificates with:

    kubectl delete secret <NAME>

-----

[Go Home](README.md)
