# CKAD preparation


Tips:

1. K8s commands

```

-- Set alias and autocomplete to new alias created
$ alias k=kubectl
$ complete -F __start_kubectl k

-- Create Pod 
k run busybox --image=busybox --restart=Never --dry-run -o yaml
$ k run nginx --image=nginx --restart=Never

-- Create Deployment
k run busybox --image=busybox --restart=Always --dry-run -o yaml

-- Create Job
k run busybox --image=busybox --restart=OnFailure --dry-run -o yaml

-- Create CronJob
k run busybox --image=busybox --restart=OnFailure --schedule="* * * * *" --dry-run -o yaml

-- Create service
--Cluster IP
$ k expose pod nginx --port=8080 --target-port=80 --dry-run -o yaml
--Node Port
$ k expose pod nginx --type=NodePort --port=8080 --target-port=80 --dry-run -o yaml
--To edit nodePort value export above yaml to svc.yaml and then below command
$ k create --edit -f svc.yaml

--Create config map
$ k create cm my-config --from-literal=AppName=employee-search
$ k create cm my-config-file --from-file=EMP=emp.properties

--Create Secret
$ k create secret generic my-secret --from-literal=pass=Pass


--Create Persistent Volume
Search k8s documentation -->  "Persistent Volume" in concepts
Copy below part into some file and edit as per requirement

--
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv0003
spec:
  capacity:
    storage: 5Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: slow
  mountOptions:
    - hard
    - nfsvers=4.1
  nfs:
    path: /tmp
    server: 172.17.0.2
--

--Create PersistentVolumeClaim
Search k8s documentation -->  "Persistent Volume" in concepts
Copy below part into some file and edit as per requirement

--
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: myclaim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 8Gi
  storageClassName: slow
  selector:
    matchLabels:
      release: "stable"
    matchExpressions:
      - {key: environment, operator: In, values: [dev]}
--

--Setting more flags while creation Pod
--add more environments and args using env (try to use autocompletion from before --env to make it work)
k run busybox7 --image=busybox --restart=Never --env=GREETING='How r u?' --env=HELLO='Hello World!!' -o yaml  -- sh -c'echo $(HELLO) " " $(GREETING)'

--Setting more flags while creating Deployment
k run busybox8 --image=busybox --restart=Always --serviceaccount=default --replicas=3 -o yaml --env=GREETING='How r u?' --env=HELLO='Hello World!!' -o yaml  -- sh -c 'echo $(HELLO) " " $(GREETING)'

--Setting more flags while creating Job

--Setting more flags while creating cronjob

--Describe the resources
$ k describe pod nginx
$ k describe svc nginx

--Get yaml from k8s resources
$ k get pod nginx -o yaml

--wide information from get verb
$ k get pod nginx -o wide

--using --edit flag to reduce few key strokes
$ k create --edit -f svc.yaml

--

--Delete all pods
k delete po --all

```

# References:

1.  https://www.linkedin.com/pulse/my-ckad-exam-experience-atharva-chauthaiwale/

2. https://medium.com/@harioverhere/ckad-certified-kubernetes-application-developer-my-journey-3afb0901014

3. https://github.com/lucassha/CKAD-resources

4. https://medium.com/techprimers/kubernetes-certification-my-take-on-ckad-and-learnings-you-can-benefit-38729b895d46

5. https://github.com/dgkanatsios/CKAD-exercises

6. https://github.com/TechPrimers/k8s-the-easy-way

