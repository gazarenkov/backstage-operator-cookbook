# How to add environment variables to Backstage Container

There are 3 equal methods for adding environment variables to the Container with Backstage Opereator:

* Direct key=value declaration in the Backstage CR with **spec.application.extraEnvs.envs**
* Injection from predefined ConfigMap using **spec.application.extraEnvs.configMaps**
* Injection from predefined Secret using **spec.application.extraEnvs.secrets**

This [Example](bs-env.yaml) contains all of them.
Here you can see declaration of 3 environment variables (MY_ORG, LOG_LEVEL and MY_SECRET) using 3 methods listed above.
Note, you can have as many variables declared inside one ConfigMap or Secret as you need. It is recommended to use Secret for sensitive information (like tokens, passwords etc) and ConfigMap or direct declaration for everything else.

Apply the script on Kubernets/Openshift cluster with installed Backstage Operator:

````
kubectl apply -f ./envvar/rhdh-env.yaml 
````

Make sure the Backstage Pod is up and running:

````
kubectl get pod                             
NAME                               READY   STATUS    RESTARTS   AGE
backstage-my-rhdh-7595b5f6-tcrmv   1/1     Running   0          14m
...
````

Check if environment variables declared in the script are injected to the Backstage Container.
Note: the Pod name prefix is always like "**backstage-<your-CR-name>-**", the generated Pod name suffix varying (will be different in your case, not "-7595b5f6-tcrmv")

````
kubectl exec --stdin --tty backstage-my-rhdh-7595b5f6-tcrmv -c backstage-backend -- env      
...
MY_ORG=my-org
...
LOG_LEVEL=debug
...
MY_SECRET=secret
...
````

Also, you can make sure LOG_LEVEL env variable impacted on Backstage server log level (you will see DEBUG messages in the Backstage container output):

````
 kubectl attach backstage-my-rhdh-7595b5f6-tcrmv -c backstage-backend -i -t 
...
{"level":"debug","message":"Processing location:default/generated-28cca00eef20648da516bda20fe0e71b7b7364ba","plugin":"catalog","service":"backstage","timestamp":"2024-10-09 07:09:40"}
...

````