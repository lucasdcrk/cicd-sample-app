# Lab Report: CI/CD with Jenkins

## Getting started

In order to start the assignement I created a infra-labs github repo using chamilo.

I then used the following commands to extract the dockerlab folder and push to my own repository.

- gcl git@github.com:HoGentTIN/infra-2223-lucasdcrk.git
- cp -r infra-2223-lucasdcrk/dockerlab/cicd-sample-app cicd-sample-app
- cd cicd-sample-app
- git init && git add . && git commit -m "initial commit"
- git branch -M main
- git remote add origin git@github.com:lucasdcrk/cicd-sample-app.git
- git push -u origin main

And I gave execution permission to the sample-app script and ran it

- chmod +x sample-app.sh
- ./sample-app.sh

And created the jenkins container

```
docker run -p 8080:8080 -u root \
  -v jenkins-data:/var/jenkins_home \
  -v $(which docker):/usr/bin/docker \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v "$HOME":/home \
  --name jenkins_server jenkins/jenkins:lts
```

So far `docker ps | grep jenkins_server` should return something like this:
```
➜  ~ docker ps | grep jenkins_server
43a13d1feb24   jenkins/jenkins:lts   "/usr/bin/tini -- /u…"   7 days ago           Up 32 minutes       0.0.0.0:8080->8080/tcp, 50000/tcp   jenkins_server
```

I created a BuildSampleApp pipeline with the following configuration:
```
chmod +x sample-app.sh
./sample-app.sh
```

And then TestSampleApp with the following configuration:
```
curl -v http://172.17.0.3:5050/ | grep "You are calling me from 172.17.0.2"
```

And finally the SampleAppPipeline with the following pipeline script:
```
node {
    stage('Preparation') {
        catchError(buildResult: 'SUCCESS') {
            sh 'docker stop samplerunning'
            sh 'docker rm samplerunning'
        }
    }
    stage('Build') {
        build 'BuildSampleApp'
    }
    stage('Results') {
        build 'TestSampleApp'
    }
}
```

## Issues

### Vagrant

The first issue I encountered was to run a vagrant VM on my computer, as the VirtualBox provider does not yet supports ARM-based MacOS computers.
I tried using another provider (namely VMWare) but has it is beta software I was not able to get it work.

Because we are using docker for this assignement I choose to use Docker on my machine, without a VM.

### Accessing the docker CLI from inside a docker container

Thouth linked /var/run/docker.sock to /var/run/docker.sock inside the jenkins container as specified in the lab instructions, using `docker` from inside the jenkins container resulted in a `docker: command not found`.
This prevented the Jenkins pipelines from completing, as they were unable to restart the sample-app's container.

I solved this issue by installing the Docker CLI inside the jenkins container.

### Tempdir already exists

The last issue I had was that the pipeline would be able to run only the first time, as the tempdir was kept between new deploys.

```
[BuildSampleApp] $ /bin/sh -xe /tmp/jenkins10522810446853865319.sh
+ chmod +x sample-app.sh
+ ./sample-app.sh
mkdir: cannot create directory ‘tempdir’: File exists
Build step 'Execute shell' marked build as failure

Finished: FAILURE
```

I was able to fix this issue by adding `rm -rf ./tempdir` in the `sample-app.sh`, before created the tempdir folders.

## Resources

List all sources of useful information that you encountered while completing this assignment: books, manuals, HOWTO's, blog posts, etc.

- https://forums.virtualbox.org/viewtopic.php?t=105481
- https://www.vmware.com/products/fusion.html
- https://stackoverflow.com/questions/55315528/docker-not-found-after-mounting-var-run-docker-sock
- https://docs.github.com/en/get-started/writing-on-github/working-with-advanced-formatting/creating-and-highlighting-code-blocks
- https://www.vagrantup.com/docs/cli/global-status