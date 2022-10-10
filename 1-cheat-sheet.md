# Cheat sheets and checklists

- Student name: Lucas Decrock
- GitHub repo: https://github.com/lucasdcrk/cicd-sample-app

## Basic commands

| Task                | Command |
| :---                | :---    |
| Start the Vagrant VM | `vagrant up`  |
| Stop the Vagrant VM | `vagrant down` |
| Connect via SSH to the Vagrant VM | `vagrant ssh`  |
| Show running docker containers | `docker ps`  |
| Show all docker containers | `vagrant ps -aux`  |

## Git workflow

Simple workflow for a personal project without other contributors:

| Task                                         | Command                   |
| :---                                         | :---                      |
| Current project status                       | `git status`              |
| Select files to be committed                 | `git add FILE...`         |
| Commit changes to local repository           | `git commit -m 'MESSAGE'` |
| Push local changes to remote repository      | `git push`                |
| Pull changes from remote repository to local | `git pull`                |

## Checklist network configuration

1. Is the Vagrant VM running? `vagrant global status`
2. Is the Jenkins docker container running? `docker ps | grep jenkins_server`
3. Is a DNS-server available? `cat /etc/resolv.conf`
4. Is the Jenkins web interface available? `curl http://192.168.56.20:8080/`