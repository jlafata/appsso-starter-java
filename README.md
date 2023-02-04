# AppSSO Starter Java

This repository provides an example application used to set up an authentication mechanism with AppSSO.

## Usage

In order to successfully deploy this sample, you need to perform the following steps:

- [Provision an AuthServer](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/app-sso-getting-started-provision-auth-server.html)
- [Provision a ClientRegistration](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/app-sso-getting-started-client-registration.html)
- [Deploying the sample application as a Workload](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/app-sso-app-operators-tutorials-securing-first-workload.html)

### create working directory
```
mkdir generated
```

### creat an authServer instance
```
ytt -f appSSOInstance.yaml -v workloadNamespace=<workloadNamespace> -v devDefaultAccountUsername=<devDefaultAccountUsername> -v devDefaultAccountPassword=<devDefaultAccountPassword> > generated/appsso-instance.yaml
```

In my environment
```
ytt -f appSSOInstance.yaml -v workloadNamespace=workloads -v devDefaultAccountUsername=test_user -v devDefaultAccountPassword=Passw0rd123 > generated/appsso-instance.yaml
```
```
kubectl apply -f generated/appsso-instance.yaml 
```
### clientRegistrationResourceClaim
ytt -f clientRegistrationResourceClaim.yaml -v workloadNamespace=workloads -v appSSORedirectURI=http://appsso-starter-java-1.workloads.tap.synable.io/login/oauth2/code/sso > generated/appsso-workload.yaml

kubectl apply -f generated/appsso-workload.yaml  



### Generating a ClientRegistration

Using `ytt` CLI, to generate a `ClientRegistration` resource for this project, run:

```shell
ytt \
    --file client.yaml \
    --data-value namespace=workloads \
    --data-value workload_name=appsso-starter-java \
    --data-value domain=127.0.0.1.nip.io \
    --data-value authserver_label.my-sso=true \
    --data-value authserver_label.env=dev |
      kubectl apply -f -
```


```shell
ytt \
    --file client.yaml \
    --data-value namespace=workloads \
    --data-value workload_name=appsso-starter-java \
    --data-value domain=tap.synable.io \
    --data-value authserver_label=appsso-starter-java  > generated/client_registration.yaml

```

where:

- `namespace` - the namespace in which your workloads will run
- `workload_name` - name of the workload
- `domain` - base domain name of your TAP cluster
- `authserver_label.{matching-label}` - a uniquely identifying label(s) for your authorization server. In this example,
  we assume that the `AuthServer` resource has labels `my-sso: "true"` and `env: dev`. You may add as many identifying
  labels as needed.

For more information, check
out [Deploying the sample application as a Workload](https://docs.vmware.com/en/VMware-Tanzu-Application-Platform/1.4/tap/app-sso-app-operators-tutorials-securing-first-workload.html).