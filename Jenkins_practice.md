# Some hands-on experience with Jenkins

the whole thing is **optional**, feel free to skip any step here if you feel confident about it. The key moment is that it should work. There is not really such a thing as the best implementation. 

Feel free to ping a person who shared the document with you in case of any issue or a need for guidance, but fistly google it up =)




## Installation and expoloring Jenkins

- Create instance/VM for Jenkins.
- Install and configure latest version of Jenkins
    - during the installation, at the "Customize Jenkins" stage choose "Select plugins to install" >> None
- Create three simple jobs for understanding Jenkins variables and how to use them:
     1. Create a simple job  that lists all environment variables from the host machine
     2. Create a simple job using Shell that outputs system uptime, OS version, Kernel version etc
     3. Create a simple job that triggers after previous job runs successfully. And prints build number variable

## Plugins, folders, users, bulding, testing, pushing

- Install the following plugins:
    - [Folders]()
    - [Workspace cleanup](https://plugins.jenkins.io/ws-cleanup)
    - [Build timeout](https://plugins.jenkins.io/build-timeout)
    - [Git](https://plugins.jenkins.io/git/)
    - [Maven integration](https://plugins.jenkins.io/maven-plugin/)
    - [Artifactory](https://plugins.jenkins.io/artifactory/)
    - [Credentials Binding](https://plugins.jenkins.io/credentials-binding/)

Just to practise a little bit more, you can install some of them manually. There are 3 possible ways:
- via Jenkins cli command
- manually via folder
- throught the UI option

Here are some readings that might come in handy:
- https://www.jenkins.io/doc/book/managing/plugins/
- https://linuxtechlab.com/3-methods-to-install-plugins-on-jenkins-server/


The next jobs require some Maven knowledge on how to build a java app. In case of difficulties with it, feel free to choose any other project that uses any other language from the Internet. The idea here is to make some flow where the code will be copied, tested, packaged, and pushed somewhere remotely.
- https://github.com/spring-projects/spring-petclinic
- __(Job A)__ Create Jenkins job that clones PetClinic and runs unit tests.
- __(Job B)__ Create Jenkins job that clones PetClinic and packages it and pushes to some remote location (it can be anyting for now, even S3)
- <details><summary>Optional task</summary><br>Create a  "training_2" folder and move the job B inside <br>Create a user "builder" and allow them to Read, Build, Cancel jobs inside the folder<br> Required plugins are <a href="https://plugins.jenkins.io/cloudbees-folder">Folders</a> and <a href="https://plugins.jenkins.io/matrix-auth/">Matrix Authorization Strategy</a> </details>

## A little bit of parameterization

- Clone Petclinic to your _master_ branch 
- In your repo create three branches: qa, stage, dev. 
- In each created branch update artifactid in pom.xml with branch name
- Create parameterized __Job E__ that builds Petclinic project from branches: _qa, stage, dev_ that should be used as parameters.
    - <details><summary>Optional task</summary><br>Do the same with the <a href="https://plugins.jenkins.io/git-parameter/" >Git Parameter </a> plugin <br><br> </details>
- Create Jenkins agent and update jobs A, B created previously to run on this Jenkins agent.
    - <details><summary>Optional task</summary><br>Try another option to connect an agent to the master (either SSH or JNLP) <br><br> <details><summary>Very optional task</summary><br><details><summary>We're serious, it's really very optional</summary><br> Create a dynamic agent (either for AWS, GCP, or Vagrant+VirtualBox)  <br><br> </details> </details> </details>



## Pipelines

- Create __Job F__ as declarative pipeline that contains the steps from __Job A__ and __Job B__. Use Petclinic source from _master_ branch of your repo.
- Create _Jenkinsfile_ from __Job F__ pipeline and push it to _master_ branch in your repo.
- Update __Job F__ to use Jenkinsfile from your repo.
- Update __Job F__ to poll SCM every 2-3 minutes to run job if changes appers. Make test commit to _master_ branch and check if __Job F__ started automatically.
- <details><summary>Optional task</summary><br>Substitute the SCM pooling with a webhook<br><br> <details><summary>Very optional task</summary><br><details><summary>We're serious, it's really very optional</summary><br> A few option here, make the pipeline trigger only if a specific branch was updated. The next level is to make the pipeline trigger only if a specific branch and specific files were updated. Keep in mind, that initially you can do it with a simple job, and then later codify it in a pipeline. Perhaps the snippet generator can help as well  <br><br> </details> </details> </details>
- Inside of any **stage**, find the email or name of the committer, set it as a Jenkins variable. And later, just echo inside the **post** block. Later the idea might be developed into sth more, but now just to practice it.
- Create __Job G__ as multi-branch pipeline based on __Job F__.
  - https://www.jenkins.io/doc/book/pipeline/multibranch/
  - https://plugins.jenkins.io/workflow-multibranch/
- Create pipeline __Job H__ with a few parameters. The pipeline should print the values to the std out.
- Create pipeline __Job I__ that will trigger __Job H__ with some specific parameters.





