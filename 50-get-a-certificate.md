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

After deploying the Ingress resource with the annotations:

    certmanager.k8s.io/cluster-issuer: letsencrypt-prod
    certmanager.k8s.io/acme-http01-edit-in-place: "true"

`cert-manager` now should have started a Certificate request process. This whole
process may take up to 10-20 minutes, because uploading load balancer of the
Ingress is expected to take quite some time.

You can view the `Certificate` resource automatically created:

```
kg certificate
NAME                CREATED AT
www-dogs-com-tls    1m
```

After about 10-15 minutes, `kubectl describe certificate`  should show success:

```
Events:
  Type    Reason        Age    From          Message
  ----    ------        ----   ----          -------
  Normal  CreateOrder   3m34s  cert-manager  Created new ACME order, attempting validation...
  Normal  IssueCert     3m34s  cert-manager  Issuing certificate...
  Normal  CertObtained  3m33s  cert-manager  Obtained certificate from ACME server
  Normal  CertIssued    3m33s  cert-manager  Certificate issued successfully
```

and after the load balancer configuration is complete, you should be able to
visit `https://` address of your domain name! 🎉

:warning: The Google HTTP(s) Load Balancer updates can take 10-15 minutes;
during that time you may see 404 or other errors. Do not panic, be patient!
See Troubleshooting section below if errors persist.

**Certificate Renewals:** Let's Encrypt certificates expire every 90 days! But
[cert-manager] will keep running and it will renew your certificates before they
expire. So, don't uninstall cert-manager if you are using this certificate to
serve traffic.

-----

**Did this work for you?** If so, please take time to **✩Star** the repository on
GitHub. This would show your support!

**Next:** [Cleanup &rarr;](99-cleanup.md)

-----

### Behind the scenes

Let's take a look at what is [cert-manager] doing behind the scenes after you
create the Ingress:

1. cert-manager's
   [ingress-shim](https://cert-manager.readthedocs.io/en/latest/reference/ingress-shim.html)
   detects that you request a TLS certificate for the host specified under
   `tls:` section.
2. cert-manager creates `Certificate` resource, which will start the certificate
   request process. (You can find the Certificate at `kubectl get certificate`.)
3. cert-manager updates your Ingress to handle `GET
   /.well-known/acme-challenge/*` requests with a temporary Service it created
   in your cluster. This will be used to prove that you own the domain name.
4. You can run `kubectl get ingress -o=yaml helloweb` to see how it is modified.
5. Since Ingress is updated, Google Cloud Load Balancer is being updated too!
6. It will take about 10-15 minutes for the changes to take effect.
7. After Ingress changes take effect, cert-manager will notice that the
   `/.well-known/*` URL starts working.
8. At that time you will be able to visit your non-https website http://www.dogs.com
9. cert-manager will ask Let's Encrypt to provide certificates.
10. Let's Encrypt will come and visit `/.well-known/*` URL to see the proof that
   you own the domain name.
11. Let's Encrypt will provide you certificates.
12. cert-manager will save the TLS certificates to the specified
    `spec.tls[0].secretName`. You can check this out `kubectl get secret
    www-dogs-com-tls`.
13. Google Cloud Load Balancer is being updated with this new TLS (will take some time)
14. Voila! You can visit https://www.dogs.com on your browser.


cert-manager will continue to run in the background and renew your certificates
every 60 days (or so), because all Let's Encrypt certificates expire every 90
days! So, don't uninstall cert-manager if you start using this certificate to
serve traffic.


### Troubleshooting

To see error logs or events about this process, run the following commands:

- `kubectl describe ingress/helloweb`: Shows load balancer status. This may
  contain errors about load balancer creation/update status,
- `kubectl describe certificate`: This should show the Certificate events.

[cert-manager]: https://github.com/jetstack/cert-manager

![Google Analytics](https://ga-beacon.appspot.com/UA-2609286-16/50-get-a-certificate?pixel)
