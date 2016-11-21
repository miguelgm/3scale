# 3scale
3scale test

Tools Needed

oc - To interact with OpenShift Online. Available in opt/ or at openshift.com

Instructions:

- Configuring the accounts:
    - Fork the https://github.com/miguelgm/3scale repo to your github.com account
    - Register an account in openshift.com
    - Obtain an OpenShift API token by visiting https://api.preview.openshift.com/oauth/token/request. You will need to log to your github.com account
    - Execute "oc login --token=YOUR_TOKEN_HERE --server=https://api.preview.openshift.com" to configure the credentials for your oc installation

- Creating the project and apps in openshift.com:
    - Create a new project with "oc new-project 3scale"

    - Deploying redis:
        - Launch a redis pod by executing "oc new-app redis:latest"
        - Add readiness probe "oc set probe dc/redis --readiness --open-tcp=6379 --initial-delay-seconds=10"

    - Deploying helloworld:
        - Build the image containing the hello-world app "oc new-build centos/ruby-23-centos7~https://github.com/YOUR_GITHUB_USER/YOUR_3SCALE_FORK_APP.git --strategy='source' --name=helloworld --to=helloworld"
        - Get "DOCKER REPO" for ImageStream using "oc get is/helloworld"
        - Edit "dc_helloworld.yaml" and change line 41 with the value you got in the last step
        - Create deploy config for app helloworld "oc create -f dc_helloworld.yaml"
        - Add readiness probe "oc set probe dc/helloworld --readiness --get-url=http://:8080/ --initial-delay-seconds=10"
        - Add liveness probe "oc set probe dc/helloworld --liveness --get-url=http://:8080/ --initial-delay-seconds=20"

    - Creating helloworld service and route:
        - Create helloworld service "oc create -f svc_helloworld.yaml"
        - Create route for helloworld service "oc create -f routes_helloworld.yaml"

- Test it with "curl
    - Get the URL with "oc get routes/helloworld"
    - curl URL


Since friday, there is a bug that prevents the access to any pod using a route. I got it working on thursday.
https://bugzilla.redhat.com/show_bug.cgi?id=1396798
