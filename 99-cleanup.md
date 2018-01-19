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
