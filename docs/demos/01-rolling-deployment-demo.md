# Rolling Deployment Demo Workflow

We will perform a standard rolling deployment by upgrading from an older version of an application to an updated one. 

We will see two ways of creating a service so that you become familiar with kubectl and YAML. 

Relevant Kubernetes documentation is found [here](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/). Instructors will cover "Writing a Deployment Spec." 

You will perform these steps in the Google Cloud Shell.

## 1. Navigate to the Workshop Directory (if necessary)  

```
cd hands-on-with-kubernetes-gke
```

## 2. Deploy an Initial Version 1.0 of the App

```
kubectl apply -f examples/rolling-deployment/deployment-v1.0.yaml
```

## 3. Display Workloads In The Terminal

```
kubectl get deployments
```

Describe the Deployment

```
kubectl describe deployments kdemo-dep
```

See all the pods running on the cluster in the `default` namespace

```
kubectl get pods -o wide
```

## 4. Create a Service With an External Endpoint

Create a Service definition for the new deployment

```
kubectl expose deployment kdemo-dep \
--type=LoadBalancer \
--name=kdemo-svc \
--port=80 \
--target-port=8080
```

Alternatively, you can use the deployment spec

```
kubectl create -f examples/rolling-deployment/service.yaml
```

Find the external IP

```
kubectl get services kdemo-svc
```

Initially you will see something like this while the Google Load Balancer allocates an external IP address

```
root@bootstrap-node:~/hands-on-with-kubernetes-workshop# kubectl get services
NAME                CLUSTER-IP       EXTERNAL-IP            PORT(S)        AGE
kdemo-svc           172.17.149.128   <pending>              80:32233/TCP   13s
```

After a short while, running the command again should give you something like this

```
root@bootstrap-node:~/hands-on-with-kubernetes-workshop# kubectl get services
NAME                CLUSTER-IP       EXTERNAL-IP            PORT(S)        AGE
kdemo-svc           172.17.149.128   104.196.252.72         80:32233/TCP   13s
```

## 5. Browse Version 1.0 of the App

Navigate to the `EXTERNAL-IP` address (104.196.252.72 in this example) from a browser tab/window to hit one of the Pods. You should see a simple HTML page displaying the Pod ID in a blue background.

## 6. Deploy New Version 1.1 of the App

Execute the following command

```
kubectl apply -f examples/rolling-deployment/deployment-v1.1.yaml
```

Check the pods that are running, either in the Dashboard or with this command

```
kubectl get pods -o wide
```

We should see v1.1 of the application Pods coming online

```
NAME                                 READY     STATUS              RESTARTS   AGE
kdemo-dep-1412125313-c9clv   1/1       Running             0          7m
kdemo-dep-1412125313-dlhsh   1/1       Terminating         0          7m
kdemo-dep-1412125313-lxtx3   1/1       Running             0          7m
kdemo-dep-1641828995-6z6xm   0/1       ContainerCreating   0          3s
kdemo-dep-1641828995-jjgtc   0/1       ContainerCreating   0          3s
```

## 7. Browse New Version 1.1 of the App

Refresh the site in the browser. You should see version 1.1, with a green background.

## 8. Roll Back the Deployment

Execute the following comand to roll back the deployment we just made

```
kubectl rollout undo deployment kdemo-dep
```

You can check the status of the rollout by executing this

```
kubectl rollout status deployment kdemo-dep
```

## 9. Confirm Rollback of the App

Refresh the site in the browser. You should see version 1.0 again, with a blue background.

## 10. Delete the Demo Artifacts

Delete the Service

```
kubectl delete services kdemo-svc
```

Finally delete the deployment

```
kubectl delete -f examples/rolling-deployment/deployment-v1.1.yaml
```
