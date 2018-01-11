# jobs

Some sample job definitions and templates to use with OpenShift.

## pi

A sample Perl job from the OpenShift documentation that prints the value of pi. To create the job:

    $ oc create -f pi/job.yml

Once the pod has been created, you can get the logs of the job using:

    $ oc logs job/pi

When the job has finished succesfully, the status should show as Completed:

    $ oc get pods
    NAME       READY     STATUS      RESTARTS   AGE
    pi-ipb1e   0/1       Completed   0          1m

## envdemo

A Job that uses environment variable populated from a ConfigMap.

    $ oc create -f envdemo/configmap.yml
    $ oc create -f envdemo/job.yml

Check that the value of `GREETING_MESSAGE` is output in the logs:

    $ oc logs -f jobs/envdemo
    GREETING_MESSAGE  =  Bonjour
    HOME  =  /
    HOSTNAME  =  envdemo-fyva8
    ...
    
## templatedjob

A Job defined in an OpenShift template. To create the template:

    $ oc create -f templatedjob/configmap.yml
    $ oc create -f templatedjob/template.yml

Then to create the job: 

    $ oc process templatedjob | oc create -f -
    job "templatedjob-0bgrbatx" created
    
Kubernetes will apply the label `job-name=xxx` to pods. So, to see the pod(s) created to execute the job:

    $ oc get pods -l job-name=templatedjob-0bgrbatx
    
Check the output logs:

    $ oc logs job/templatedjob-0bgrbatx
    GREETING_MESSAGE  =  Howdy
    HOME  =  /
    HOSTNAME  =  templatedjob-0bgrbatx-6n6mi
    JOB_PARAM  =  joe.smith

Limitation: the same job can't be created more than once.

--

## Kubernetes API

Using the Kubernetes API to get information about Jobs.

Using OpenShift, log in as a user with permissions to view jobs, then:

    $ OCTOKEN=$(oc whoami -t)
    $ OCMASTER=127.0.0.1:8443
    $ OCPROJECT=jobs
    $ OCJOBNAME=templatedjob-0bgrbatx
    $ curl -k -H "Authorization: Bearer $OCTOKEN" https://$OCMASTER/apis/batch/v1/namespaces/$OCPROJECT/jobs/$OCJOBNAME

A **running** job will have a `status` section in the response like this:

```json
"status": {
  "startTime": "2018-01-11T13:27:38Z",
  "active": 1
}
```

A **completed** job will have a `status` section in the response like this:

```json
"status": {
  "conditions": [
    {
      "type": "Complete",
      "status": "True",
      "lastProbeTime": "2018-01-11T13:28:45Z",
      "lastTransitionTime": "2018-01-11T13:28:45Z"
    }
  ],
  "startTime": "2018-01-11T13:27:38Z",
  "completionTime": "2018-01-11T13:28:45Z",
  "succeeded": 1
}
```
