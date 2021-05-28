# Jobs and Cronjobs

## Jobs 
Jobs are meant to performa a task and finish. Jobs are disposable pods, that instead of running indefinitely will execute a task and finish, entering ```Completed``` status.

``` kubectl get jobs ```

```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: date
    spec:
      completion: 3
      parallelism: 3
      template:
        spec:
          containers:
          - name: date
            image: alpine
            command: ['sh', '-c', 'echo $DATE']
          restartPolicy: Never
      backoffLimit: 4
```
```backoffLimit``` specifies how many times job should be retried before considering as failed

```completion``` allows to specify how many pods should a successfully run a job before it's considered completed

```parallelism``` allows to specify how many pods are created at once (in parallel)

```
    kubectl create -f jobs_and_cronjobs/job.yaml 
    job.batch/hello created
    
    kubectl get jobs
    NAME    COMPLETIONS   DURATION   AGE
    hello   1/1           3s         6s
    
    kubectl describe job hello
    Name:           hello
    Namespace:      default
    Selector:       controller-uid=a7b82cd0-1f45-4b02-9440-980d5efa53d2
    Labels:         controller-uid=a7b82cd0-1f45-4b02-9440-980d5efa53d2
                    job-name=hello
    Annotations:    <none>
    Parallelism:    1
    Completions:    1
    Start Time:     Mon, 24 May 2021 14:38:21 +0000
    Completed At:   Mon, 24 May 2021 14:38:24 +0000
    Duration:       3s
    Pods Statuses:  0 Running / 1 Succeeded / 0 Failed
    Pod Template:
    Labels:  controller-uid=a7b82cd0-1f45-4b02-9440-980d5efa53d2
            job-name=hello
    Containers:
    hello:
        Image:      alpine
        Port:       <none>
        Host Port:  <none>
        Command:
        sh
        -c
        echo Hello World
        Environment:  <none>
        Mounts:       <none>
    Volumes:        <none>
    Events:
    Type    Reason            Age   From            Message
    ----    ------            ----  ----            -------
    Normal  SuccessfulCreate  16s   job-controller  Created pod: hello-jz59l
    Normal  Completed         13s   job-controller  Job completed
    
    kubectl logs hello-jz59l
    Hello World
```

## Cronjobs

Cronjobs are jobs that execute on schedule. 
```
    spec:
      schedule: "*/1 * * * *"
      jobTemplate:
        spec:
          template:
            spec:
              containers:
              - name: cronjob
                image: busybox
```