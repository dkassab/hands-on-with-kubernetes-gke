# Resource Quota Demo Workflow

Kubernetes can restrict the amount of CPU and Memory that Pods or Containers can consume. This is especially useful when a Kubernetes cluster is not set to auto-scale (physically) and has contrained resources being shared by multiple teams. 

The revelvant Kubernetes documentation is found [here](https://kubernetes.io/docs/concepts/policy/resource-quotas/)

These steps are to be executed in the Google Cloud Shell.

## 1. Navigate to the Workshop Directory (if necessary)  

```
cd hands-on-with-kubernetes-gke
```

## 2. Execute the application service and deployment

```
kubectl apply -f examples/resource-quotas/service.yaml
kubectl apply -f examples/resource-quotas/deployment.yaml
```

## 3. List the Pods

```
kubectl get pods -o wide
```

You should now see:

```
NAME                                   READY     STATUS    RESTARTS   AGE       IP               NODE
resource-quota-demo-4067652524-01jh5   1/1       Running   0          41s       172.16.235.216   worker1
resource-quota-demo-4067652524-72xxh   1/1       Running   0          41s       172.16.235.217   worker1
resource-quota-demo-4067652524-wnhgn   1/1       Running   0          41s       172.16.235.218   worker1
```

## 4. List the Deployment

```
kubectl get deployments
```

```
NAME                  DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
resource-quota-demo   3         3         3            3           1m
```

## 5. Scale the Deployment to Something Silly

Create 100 replicas of this crappy app!

```
kubectl scale deployment resource-quota-demo --replicas=100
```

## 6. Check the Number of Pods in the Kubernetes Dashboard

If you closed the Kuberentes Dashboard, follow the instructions to open Dashboard [here](https://github.com/apprenda/hands-on-with-kubernetes-gke/blob/master/docs/3-build-cluster.md)

Now click on `Pods` from the left hand navigation menu.

You should see healthy pods (with green ticks next to them).

However you should see many pods displaying the following:

```
No nodes are available that match all of the following predicates:: Insufficient cpu (3).
```

## 7. Check the Status of the Deployment

We can also see the information by describing the deployment using

```
kubectl describe deployment resource-quota-demo
```

You will see the section: 

```
Type         Status    Reason
----         ------    ------
Available    false     MinimumReplicasUnavailable 
```

## 8. Scale Down the Replicas

```
kubectl scale deployment resource-quota-demo --replicas=3
```

## 9. Delete the Demo

Finally execute the following command to tidy away the demo

```
kubectl delete -f examples/resource-quotas/service.yaml
kubectl delete -f examples/resource-quotas/deployment.yaml
```
