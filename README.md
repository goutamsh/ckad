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

--If some fields you don't remember then use kubectl explai command
$ kubectl explain deploy.spec.strategy

--You can use -h command in case of help for some command
$ kubectl rollout -h


```

## Few kubernetes resource fields to remember for configuration

Command:

$ k run busybox --image=busybox --restart=Never --labels='app=busybox' --limits='cpu=1,memory=1000Mi' --requests='cpu=200m,memory=512Mi' --dry-run -o yaml --env MESSAGE='HelloGlasgow' -- sh -c '(while true; do date ; sleep 100; done) > /opt/log/date.log' > pod.yaml

---
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    app: busybox
  name: busybox
spec:
  volumes:
    - name: vol
      emptyDir: {}
  containers:
  - args:
    - sh
    - -c
    - (while true; do date ; sleep 100; done) > /opt/log/date.log
    env:
    - name: MESSAGE
      value: Hello Glasgow
    image: busybox
    name: busybox
    resources:
      limits:
        cpu: "1"
        memory: 1000Mi
      requests:
        cpu: 200m
        memory: 512Mi
    volumeMounts:
      - name: vol
        mountPath: /opt/log
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}


---


$ k taint node minikube testKey=testValue:NoSchedule

----
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    app: busybox
  name: busybox
spec:
  tolerations:
    - key: testKey
      value: testValue
      effect: NoSchedule
    - key: testKey1
      operator: Exists
      effect: NoSchedule
  nodeSelector:
    kubernetes.io/hostname: minikube
  volumes:
    - name: vol
      emptyDir: {}
  containers:
  - args:
    - sh
    - -c
    - (while true; do date ; sleep 100; done) > /opt/log/date.log
    env:
    - name: MESSAGE
      value: Hello Glasgow
    image: busybox
    name: busybox
    resources:
      limits:
        cpu: "1"
        memory: 1000Mi
      requests:
        cpu: 200m
        memory: 512Mi
    volumeMounts:
      - name: vol
        mountPath: /opt/log
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
----


$ k create cm config-map --from-literal=APP_NAME='My app'

k create secret generic my-secret --from-literal=PASSWORD=pass

---
my-pv.yaml

---

apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-pv
spec:
  capacity:
    storage: 1Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Recycle
  hostPath:
    path: /tmp/my-pv

---

my-pvc.yaml

----
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-claim
spec:
  accessModes:
    - ReadWriteOnce
  volumeMode: Filesystem
  resources:
    requests:
      storage: 500Mi
  storageClassName: ""

--------
---

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    app: busybox
  name: busybox
spec:
  tolerations:
    - key: testKey
      value: testValue
      effect: NoSchedule
    - key: testKey1
      operator: Exists
      effect: NoSchedule
  nodeSelector:
    kubernetes.io/hostname: minikube
  volumes:
    - name: vol
      emptyDir: {}
    - name: hostpath-vol
      hostPath:
        path: /temp/busybox
    - name: cm-vol
      configMap:
              name: config-map
    - name: secret-vol
      secret:
              secretName: my-secret
    - name: pv-vol
      persistentVolumeClaim:
              claimName: my-claim
  containers:
  - args:
    - sh
    - -c
    - (while true; do date ; sleep 100; done) > /opt/log/date.log
    envFrom:
      - configMapRef:
          name: config-map
    env:
    - name: MESSAGE
      value: Hello Glasgow
    - name: APP_NAME_FROM_CONFIG
      valueFrom:
        configMapKeyRef:
          name: config-map
          key: APP_NAME
    - name: PASS_FROM_SECRET
      valueFrom:
        secretKeyRef:
          name: my-secret
          key: PASSWORD
    image: busybox
    name: busybox
    resources:
      limits:
        cpu: "1"
        memory: 1000Mi
      requests:
        cpu: 200m
        memory: 512Mi
    volumeMounts:
      - name: vol
        mountPath: /opt/log
      - name: hostpath-vol
        mountPath: /opt/busybox/hostPath
      - name: cm-vol
        mountPath: /opt/busybox/cm
      - name: secret-vol
        mountPath: /opt/busybox/secret
      - name: pv-vol
        mountPath: /opt/busybox/my-pv
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
---


TO DO:

- node affinity
- readynessprobe and livenessprobe
- etc

# References:

1.  https://www.linkedin.com/pulse/my-ckad-exam-experience-atharva-chauthaiwale/

2. https://medium.com/@harioverhere/ckad-certified-kubernetes-application-developer-my-journey-3afb0901014

3. https://github.com/lucassha/CKAD-resources

4. https://medium.com/techprimers/kubernetes-certification-my-take-on-ckad-and-learnings-you-can-benefit-38729b895d46

5. https://github.com/dgkanatsios/CKAD-exercises

6. https://github.com/TechPrimers/k8s-the-easy-way

