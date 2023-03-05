# Jenkins CI/CD Project

**1-Jenkins Job to fetch code from github**

=> Created freestyle job on jenkins to download the code from github.

Dashboard ==> New item ==> Created free style Project ==> Provided github repository url under SCM(Source Code Management) ==> As this is public git repository so not provided credentials of git ==> Saved

=> While running job got failed with below error:

`ERROR: Couldn't find any revision to build. Verify the repository and branch configuration for this job.`

=> Saw that the repository was not in the master branch and in job master branch was specified under "Branch Specifier (blank for 'any')". So It was failing. Cahnges it to main and job got executed successfully.

**2- Creating webhook**

=> When the github to jenkins webhook is configured on the github repository, then if any changes are commited on that repository it will trigger the jenkins job in which the webhook option is selected. 



