# Syadmin / Devops

The purpose of this project is to provide a sample work test related to a typical sysadmin / devops job. This assumes
that the applicant is well-versed and familiar with a variety of technologies including:

- various Linux OS (Debian, Ubuntu, Fedora, Red Hat, etc)
- server administration, management, networking, security
- cloud computing using docker and kubernetes on various platforms including on-premises, AWS, GCP, etc
- kubernetes management and deployment tools like helm, terraform
- managing git and ci/cd via jenkins, gitlab pipelines/runners, github actions, etc

## Instructions

This test is broken up into different sections. For each question, please put answers, related files and code snippets
inside each section's folder or in `answers.md`. Answers to questions should be specific and include code where possible
as well as include any reasoning or logic behind it. If any question is unclear to you, then attempt to answer to the
best of your understanding. The test should take only a few hours to complete. For most code questions you don't need to
provide full working examples as long as you are able to provide as much relevant code as possible and clearly indicate
the intention and logic in your answer.

For example:

*You have been asked to retrieve a copy of the mysql database. How would you accomplish this?*

**BAD**

- I would run the mysqldump command on the server

**GOOD**

- I would run the following command:
  `mysqldump -u USER -p DBNAME --ignore-table=DBNAME.TABLE --single-transaction --quick --lock-tables=false`
  using those options because ... .

### The Test

#### Servers and OS knowledge

1. The company wants to provision a brand new server from a third party provider for hosting a kubernetes cluster. By
   default, this server is provisioned with a Linux OS of your choice and ssh root access using your ssh key. Explain in
   detail the various security issues that will need to be addressed and how you would address them. What would be
   needed to ensure this server is hardened from various threats? What kind of threats would we need to be wary of?
   Include the OS, any specific tools, commands, caveats and reasoning behind your answer.

2. After securing the server, we need to be able to give certain users access. What mechanisms or methods would you use
   to secure access to the server and also to grant only specific users access to the server? What methods are available
   and what would be most secure?

### Configuration Management (CM)

1. For managing the server above using your proposed configuration/settings, we don't want to do this manually. Assume
   we need to configure 10+ servers in pretty much the same way, all of which will be running kubernetes, either in the
   same cluster or different clusters. Using the tool(s) of your choice (ansible, puppet, chef, etc), provide an example
   of how you would achieve securing the servers and applying configuration using CM. Include any sample scripts or code
   snippets (any yaml's, templates, manifests, etc), as well as details on how you would manage any secrets (ie. if you
   require any root passwords in order to run your CM scripts). Try to provide as much relevant code as possible while
   being detailed in your answer. You do not need to write a fully working example; we are mostly interested in your
   thought process and how you would structure and manage the configuration of servers as well as evaluate your
   proficiency in using the tool(s) of your choice.

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

2. We would like to deploy the application above onto the kubernetes cluster we just provisioned in the first section.
   We will use nginx to serve the application. Using the provisioning tool of your choice (helm, terraform, etc), create
   an example of how you would accomplish this. Provide a complete yet minimal code sample/snippet (ie. a helm chart
   with deployments, ingresses, configmaps, etc, or terraform config files, templates, etc). Be sure to include how you
   would manage secrets and values as well as any setup instructions and notes.

3. Suppose we wanted to deploy this onto a cloud provider instead. Using the provider of your choice (AWS, GCP, etc),
   what would you need to change from the previous question in order to get this deployed? Provide a detailed answer on
   the necessary changes as well as any structural changes to your code (if needed) to make this simple and easy to
   configure.
