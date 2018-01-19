# Install cert-manager

[cert-manager] is "pre-stable" software,so use it at your own risk. Clone its repository and deploy the Helm chart:

    git clone https://github.com/jetstack/cert-manager.git

    cd cert-manager

    helm install --name cert-manager \
        --namespace kube-system contrib/charts/cert-manager


[cert-manager]: https://github.com/jetstack/cert-manager/

-----

**Next:** [Set up Let’s Encrypt &rarr;](30-setup-letsencrypt.md)
