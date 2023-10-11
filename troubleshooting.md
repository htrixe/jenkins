Troubleshooting: Jenkins not coming up?


There's a chance that your jenkins container won't come up. If you can't see your container by using docker ps, this will help! Otherwise, you can just ignore it :D



Issue:

docker ps doesn't show the running container.

Debug:

docker ps -a shows the container with exit status.

docker logs jenkins shows a volume permission error.

Reason:

* Inside of the Jenkins container, there's a user named "jenkins" which has a Linux uid of 1000.

* You are mounting a docker volume to /var/jenkins_home which is the home directory of that user. If the directory doesn't have 1000 permissions, then the user won't be able to write/delete files, which causes the container to exit.

Resolution:

Apply 1000 permissions to your jenkins-data folder, and then restart the container.

sudo chown 1000:1000 -R ~/jenkins-data 
docker-compose up -d