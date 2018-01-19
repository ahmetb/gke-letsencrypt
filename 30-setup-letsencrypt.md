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
[ClusterIssuer]: https://github.com/jetstack/cert-manager/blob/master/docs/user-guides/cluster-issuers.md
[manifest]: yaml/letsencrypt-issuer.yaml

-----

**Next:** [Set up Let’s Encrypt &rarr;](40-deploy-an-app.md)

