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

# Get a certificate for your domain name

Save [certificate.yaml](yaml/certificate.yaml) manifest, which will request
a certificate for a domain name from the `letsencrypt-prod` issuer:

    curl -O https://raw.githubusercontent.com/ahmetb/gke-letsencrypt/master/yaml/certificate.yaml

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: dogs-com-tls
  namespace: default
spec:
  secretName: dogs-com-tls
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
  commonName: www.dogs.com
  dnsNames:
  - www.dogs.com
  acme:
    config:
    - http01:
        ingress: helloweb
      domains:
      - www.dogs.com
```

Modify a few things before deploying this manifest:

- Replace `www.dogs.com` with your domain name
- Replace `dogs-com-tls` (will be used to create the TLS Secret) with a name
  that is suitable
- Replace `helloweb` with the Ingress name that your website is running on

Then apply this manifest:

    kubectl apply -f certificate.yaml

Go get a cup of coffee. :coffee:

Now, while you're waiting, this is what cert-manager doing behind the scenes:

0. cert-manager updates your Ingress to handle `GET /.well-known/acme-challenge/*` requests with a temporary Service it created in your cluster. This will be used to prove that you own the domain name.
0. You can run `kubectl get ingress -o=yaml helloweb` to see how it is modified.
0. Since Ingress is updated, Google Cloud Load Balancer is being updated too!
0. It will take about 5-10 minutes for the changes to take effect.
0. After Ingress changes take effect, cert-manager will notice that the `/.well-known/*` URL starts working.
0. cert-manager will ask Let's Encrypt to provide certificates.
0. Let's Encrypt will come and visit `/.well-known/*` URL to see the proof that you own the domain name.
0. Let's Encrypt will provide you certificates.
0. cert-manager will save the TLS certificates to the specified secretName.

When it is complete, you will see the specified `secretName` in the Secrets list:

    $ kubectl get secrets

    NAME               TYPE                DATA      AGE
    www-dogs-com-tls   kubernetes.io/tls   2         4m

You can also look at the status of the Certificate resource you just created:

    $ kubectl describe -f certificate.yaml

    ...
    Type     Reason                Message
    ----     ------                -------
    Warning  ErrorCheckCertificate Error checking existing TLS certificate: secret "www-dogs-com-tls" not found
    Normal   PrepareCertificate    Preparing certificate with issuer
    Normal   PresentChallenge      Presenting http-01 challenge for domain foo.kubernetes.tips
    Normal   SelfCheck             Performing self-check for domain www.dogs.com
    Normal   ObtainAuthorization   Obtained authorization for domain www.dogs.com
    Normal   IssueCertificate      Issuing certificate...
    Normal   CeritifcateIssued     Certificated issued successfully
    Normal   RenewalScheduled      Certificate scheduled for renewal in 1438 hours

If you see the "CeritificateIssued" event, it means it has worked!

**Congratulations, you now have a TLS certificate for your domain!**

-----

**Next:** [Start serving HTTPS traffic &rarr;](60-start-serving-https.md)

