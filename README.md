# CKAD preparation


Tips:

1. K8s commands

```

-- Set alias and autocomplete to new alias created
$ alias k=kubectl
$ complete -F __start_kubectl k

-- Create Pod 
k run busybox --image=busybox --restart=Never --dry-run -o yaml

-- Create Deployment
k run busybox --image=busybox --restart=Always --dry-run -o yaml

-- Create Job
k run busybox --image=busybox --restart=OnFailure --dry-run -o yaml

-- Create CronJob
k run busybox --image=busybox --restart=OnFailure --schedule="* * * * *" --dry-run -o yaml


-- Create service


--Setting more flags while creation Pod
--add more environments and args using env (try to use autocompletion from before --env to make it work)
k run busybox7 --image=busybox --restart=Never --env=GREETING='How r u?' --env=HELLO='Hello World!!' -o yaml  -- sh -c'echo $(HELLO) " " $(GREETING)'




--Setting more flags while creating Deployment

k run busybox8 --image=busybox --restart=Always --serviceaccount=default --replicas=3 -o yaml --env=GREETING='How r u?' --env=HELLO='Hello World!!' -o yaml  -- sh -c 'echo $(HELLO) " " $(GREETING)'

--Setting more flags while creating Job

--Setting more flags while creating cronjob

--Describe the resources

--Get yaml from k8s resources

--wide information from get verb

--using --edit flag to reduce few key strokes

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

