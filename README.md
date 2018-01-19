# Let’s Encrypt on GKE

[GKE] (Google Kubernetes Engine) does not offer an out-of-the-box HTTPS solution
or TLS/SSL certificates for your websites today.

[Let's Encrypt][le] is a non-profit Certificate Authority that provides free
TLS/SSL certificates that can be used to secure websites with HTTPS.

In this tutorial, you will install a third-party Kubernetes controller named
[cert-manager] to your cluster. This add-on automates obtaining and renewing
TLS/SSL certificates from Let's Encrypt.

- Make sure to have a domain name and a GKE cluster ready to go!
- Estimated time: 30 minutes.

## Steps

0. [Install Helm](10-install-helm.md)
0. [Install cert-manager](20-install-cert-manager.md)
0. [Set up Let's Encrypt](30-setup-letsencrypt.md)
0. [Deploy a web app on a domain name](40-deploy-an-app.md)
0. [Get a certificate for your domain](50-get-a-certificate.md)
0. [Start serving HTTPS with the certificate](60-start-serving-https.md)
0. [Cleanup](99-cleanup.md)

-----

This is not an official Google product or documentation.


[GKE]: https://cloud.google.com/kubernetes-engine
[le]: https://letsencrypt.org/
[cert-manager]: https://github.com/jetstack/cert-manager/
[Ingress]: https://cloud.google.com/kubernetes-engine/docs/tutorials/http-balancer
