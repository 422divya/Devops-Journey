# Jenkins CI/CD Project

**1-Jenkins Job to fetch code from github**

=> Created freestyle job on jenkins to download the code from github.

Dashboard ==> New item ==> Created free style Project ==> Provided github repository url under SCM(Source Code Management) ==> As this is public git repository so not provided credentials of git ==> Saved

=> While running job got failed with below error:

`ERROR: Couldn't find any revision to build. Verify the repository and branch configuration for this job.`

=> Saw that the repository was not in the master branch and in job master branch was specified under "Branch Specifier (blank for 'any')". So It was failing. Cahnges it to main and job got executed successfully.

**2- Creating webhook**

=> When the github to jenkins webhook is configured on the github repository, then if any changes are commited on that repository it will trigger the jenkins job in which the webhook option is selected. 

==> Github plugin is required to use the webhook. This plugin runs service in the background which monitors the github webhook url. If any trigger is done on that url the in whichever jenkins job the github hook option is selected and has the same repository it is executed.

**3-**While pushing the image in the Docker hub it was failing with below error while substituting the variable with the username and password of Docker hub.

`/var/lib/jenkins/workspace/job-pipeline@tmp/durable-33029181/script.sh: line 1: ${env.dockerusername}: bad substitution`

Below is the correct syntax. When trying to give single quote it was taking the ${env.dockerusernam} as the value and not as variable. After giving double quotes the error got resolved:

============
`stage ('Push the Docker image') {`
          `steps {`
                `withCredentials([usernamePassword(credentialsId: 'dockerhub1', usernameVariable: 'dockerusername', passwordVariable: 'hubpassword')]) {`
                    
            
                sh "docker login -u ${env.dockerusername} -p ${env.hubpassword}"
                sh "docker push divya422/reach-jango"
                }`
                
============
