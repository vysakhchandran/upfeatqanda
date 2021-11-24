### Cloud Computing

1. Assume our developers have created a PHP 8 application using the Laravel framework. This application requires
   installing composer packages from public git repositories as well as from a private gitlab repository. The
   application also requires a cron to be run *optionally* which looks something like:
   ```
   * * * * * cd /path-to-your-project && php artisan schedule:run >> /dev/null 2>&1
   ```
   There should be a set of common php modules installed as well as xdebug. You may choose whichever base image of your
   choice in order to fulfill the above requirements.

   Create a Dockerfile that will be able to run this application. Ideally we want only one docker image created for use
   in both production and development. This means that xdebug should only be enabled in development environments (
   explain how something like this could be accomplished). Remember to include the steps required in installing packages
   from a private repository as well as using all best practices in creating the final docker image. Also include any
   issues (if any) that you think should be taken into consideration in your answer.

### Answer

Note: As am going for a vacation and i was looped over with other important tasks,i failed to manage my time. Being that said, i would be writing a high level answers and making use of contents available on internet. if needed, am happy to share a working model of code after i return from vacation which is next week. thansk in advance. 

For installing packages from a private repo you can configure compose to use mutiple repo or perhaps use satis. You may need to configure a git token to setup the authentication.
```
composer config --global --auth gitlab-token.gitlab.com PAT_TOKEN

add this in config
----------
"repositories": [
    {
        "type": "vcs",
        "url": "git@gitlab.com:namespace/repo-name.git"
    }
]

to use 
-------
composer require vendor-name/package-name

```

Its not advisable to setup a cron inside a container. Ideally i would seperate my scheduled/housekeeping tasks from my main workload container deployments. However if we want to push the cron its doable and we can copy the cron job locally to containers

```
FROM ubuntu:latest
MAINTAINER docker@ekito.fr

RUN apt-get update && apt-get -y install cron

# Copy hello-cron file to the cron.d directory
COPY hello-cron /etc/cron.d/hello-cron
 
# Give execution rights on the cron job
RUN chmod 0644 /etc/cron.d/hello-cron

# Apply cron job
RUN crontab /etc/cron.d/hello-cron
 
# Create the log file to be able to run tail
RUN touch /var/log/cron.log
 
# Run the command on container startup
CMD cron && tail -f /var/log/cron.log
```

And for using one docker file for production and dev. Its not a best practice because maintaining seperate docker/deployment files per environment is relatively easy than writing logic inside docker files, it introduces complexity and reduces reliability. However if still want to do this, we can write a script with logic checking the environemnt and executing the code based on environment. And in the docker file, copy the script file and execute it. 

```
example from internet 
-------------
Create a shell script, called install.sh that does something like:

if [ ${ENV} = "DEV" ]; then 
    composer install
else
    npm install
fi
In your Dockerfile add this script and then execute it when building

...
COPY install.sh install.sh
RUN chmod u+x install.sh && ./install.sh
...
```
Best practices 
1. Always use a .dockerignore file to limit the build context of your image.
2. Keep your image size as little as possible by avoiding installing unnecessary packages, which will also potentially make your containers more secure due to fewer dependencies.
3. Use trusted, official images from Docker Hub as base images.
4. Make sure you are running a single process per container.
5. Group commands into a single instruction (image layer) whenever possible.
6. Place any instruction that is more likely to change at the bottom of your file for better usage of image layer caches.

-----------------------

2. We would like to deploy the application above onto the kubernetes cluster we just provisioned in the first section.
   We will use nginx to serve the application. Using the provisioning tool of your choice (helm, terraform, etc), create
   an example of how you would accomplish this. Provide a complete yet minimal code sample/snippet (ie. a helm chart
   with deployments, ingresses, configmaps, etc, or terraform config files, templates, etc). Be sure to include how you
   would manage secrets and values as well as any setup instructions and notes.

Laravel k8 helm deployment are well described in [here](https://www.digitalocean.com/community/tutorials/how-to-deploy-laravel-7-and-mysql-on-kubernetes-using-helm)  Apologies for taking the shortcut. 


3. Suppose we wanted to deploy this onto a cloud provider instead. Using the provider of your choice (AWS, GCP, etc),
   what would you need to change from the previous question in order to get this deployed? Provide a detailed answer on
   the necessary changes as well as any structural changes to your code (if needed) to make this simple and easy to
   configure.
If we are deploying this on a cloud. I would take advantage of the eco system. Which means i would be able to seamlessly setup CI automations. right form the code repository, i'll be adapting cloud native tools. Like in aws codecommit for code repo,codebuild for build and testing, codedeployment for automated deployments, also use the container registry and secret management tools. Specifically for this workload, i would make use of EKS on fargate ( no tension ) 

However if we want to enjoy most of this benefits on prem, i would probably think of setting up an Openshift and deploy the laravel/or any apps on top of it. 
