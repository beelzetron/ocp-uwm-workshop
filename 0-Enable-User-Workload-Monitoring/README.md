# Enable User Workload Monitoring

In this section we enable User Workload Monitoring feature and we set up two projects that we will use later on.

## Create or modify cluster-monitoring-config ConfigMap

First, let's check if the `cluster-monitoring-config` ConfigMap exists in the `openshift-monitoring` namespace:

```bash
oc get configmap cluster-monitoring-config -n openshift-monitoring
```

If the previous command returns output similar to this:

```bash
NAME                        DATA   AGE
cluster-monitoring-config   1      5d23h
```

The ConfigMap is present, just add the line

```yaml
    enableUserWorkload: true
```

to the existing ConfigMap by editing it, for instance, this ConfigMap contains the configuration required to persist prometheus data and user workload monitoring parameter:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: cluster-monitoring-config
  namespace: openshift-monitoring
data:
  config.yaml: |
    enableUserWorkload: true
    prometheusK8s:
      volumeClaimTemplate:
        spec:
          storageClassName: local-storage
          resources:
            requests:
              storage: 40Gi
```

If the `cluster-monitoring-config` ConfigMap does not exists create it like this:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: cluster-monitoring-config
  namespace: openshift-monitoring
data:
  config.yaml: |
    enableUserWorkload: true
```

Save the yaml snippet to a file and apply it to your cluster:

```bash
oc apply -f cluster-monitoring-config.yaml
```

Wait until all the pods in the namespace `openshift-user-workload-monitoring` are in state `Running`.

```bash
oc get pods -n openshift-user-workload-monitoring 
NAME                                   READY   STATUS    RESTARTS   AGE
prometheus-operator-54b64ffcdb-bmgw6   2/2     Running   0          1m
prometheus-user-workload-0             5/5     Running   1          1m
prometheus-user-workload-1             5/5     Running   1          1m
thanos-ruler-user-workload-0           3/3     Running   0          1m
thanos-ruler-user-workload-1           3/3     Running   0          1m
```

## Create the projects and grant required privileges

Now that the user workload monitoring stack is running, we create two projects that we will use to deploy our applications.

Create the first project:

```bash
oc new-project simple-app
```

Grant the required roles to the `developer` user:

```bash
oc policy add-role-to-user monitoring-edit developer
oc policy add-role-to-user admin developer
```

Create the second project:

```bash
oc new-project jboss-app
```

Grant the required roles to the `developer` user:

```bash
oc policy add-role-to-user monitoring-edit developer
oc policy add-role-to-user admin developer
```
