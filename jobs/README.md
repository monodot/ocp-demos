# jobs

Some sample job definitions and templates to use with OpenShift.

## pi-job.yml

A sample Perl job from the OpenShift documentation that prints the value of pi. To create the job:

    $ oc create -f pi-job.yml

Once the pod has been created, you can get the logs of the job using:

    $ oc logs job/pi

When the job has finished succesfully, the status should show as Completed:

    $ oc get pods
    NAME       READY     STATUS      RESTARTS   AGE
    pi-ipb1e   0/1       Completed   0          1m

## envdemo-job.yml

A Job that uses environment variable populated from a ConfigMap.

    $ oc create -f envdemo-configmap.yml
    $ oc create -f envdemo-job.yml

Check that the value of `GREETING_MESSAGE` is output in the logs:

    $ oc logs -f jobs/envdemo
    GREETING_MESSAGE  =  Bonjour
    HOME  =  /
    HOSTNAME  =  envdemo-fyva8

