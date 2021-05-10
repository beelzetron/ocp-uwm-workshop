# OpenShift 4.6+ User Workload Monitoring Workshop

## Introduction

This is a runbook for the OpenShift 4.6+ User Workload Monitoring Workshop.

It demonstrates how to enable User Workload Monitoring and how to monitor applications.

It is divided into 3 main sections, please follow them starting from the first one.

## Prereqs

You will need:

- a minimum understanding of linux shell
- an OCP 4.6+ cluster

### Optional - Configure an external auth provider

Configure an authentication provider for your new cluster _only if you do not have one in place_.

We will use [HTPasswd](https://docs.openshift.com/container-platform/4.7/authentication/identity_providers/configuring-htpasswd-identity-provider.html) provider.

1. Create the htpasswd password file:

    ```bash
    htpasswd -c -B -b users.htpasswd developer redhat123
    ```

2. Create the HTPasswd Secret:

    ```bash
    oc create secret generic htpass-secret --from-file=htpasswd=users.htpasswd -n openshift-config
    ```

3. Define the OAuth Custom Resource for htpasswd identity provider:

    ```yaml
    apiVersion: config.openshift.io/v1
    kind: OAuth
    metadata:
      name: cluster
    spec:
      identityProviders:
      - name: htpasswd 
        mappingMethod: claim 
        type: HTPasswd
        htpasswd:
          fileData:
            name: htpass-secret
    ```

4. Apply the Custom Resource by pasting it onto the web console or by saving it to a file and applying it with `oc apply -f`.

## Enable User Workload Monitoring

[Enable User Workload Monitoring](0-Enable-User-Workload-Monitoring/README.md)

## Monitor a simple application

[Monitor a simple application](1-Monitor-simple-application/README.md)

## Monitor a Jboss application

[Monitor a Jboss application](2-Monitor-Jboss-application/README.md)
