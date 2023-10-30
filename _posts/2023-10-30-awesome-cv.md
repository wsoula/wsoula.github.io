Automatic Resume/CV From LaTex Template
---
After I got my last job I came across a reddit post mentioning https://github.com/posquit0/Awesome-CV
and I decided to try and get it setup.  I had it all working great and then promptly forgot about
it while getting my deep into my new job.  Periodically, we have to update our resumes for regulatory
reasons and I got an email from HR that my resume was out of date and needed updating.  I remembered
about my Awesome-CV repo and decided to keep that up to date as well.  It was then that I discovered
that all the repos I was using were out of date and did not work anymore.  I had to update the
docker-texlive-thin docker image to use ubuntu 23.10 instead of 22.10, which required publishing
my own docker-texlive-thin image to dockerhub.  Then I had to update the awesome-cv-action repo to
use this new docker image, which required forking that repo and publishing my own awesome-cv-action
github action.  Then I was able to update my Awesome-CV repo to use my new action and I finally
got a new resume uploaded as an artifact.  So feel free to use my new docker image and github action
and open issues on it if it gets out of date again.
