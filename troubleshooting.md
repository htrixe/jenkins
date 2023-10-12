# Troubleshooting: Jenkins not coming up?


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

---

# Troubleshooting: remote-host image not building correctly?


There's a chance that your remote-host image won't sucessfully build.

Why?

Well, in the course we are using centos:latest, which at that point was centos:7. In September/2019, centos 8 was released, and it's downloaded instead of the 7 version when you use "latest"

Resolution:

1 - Change the from instruction and point to centos 7

FROM centos:7

2- Keep using centos 8 and modify these lines:

Change this

RUN useradd remote_user && \
    echo "1234" | passwd remote_user  --stdin && \ # Passwd command is deprecated on centos:8
    mkdir /home/remote_user/.ssh && \
    chmod 700 /home/remote_user/.ssh
to this:

RUN useradd remote_user && \
    echo "remote_user:1234" | chpasswd && \
    mkdir /home/remote_user/.ssh && \
    chmod 700 /home/remote_user/.ssh


You could also see errors related to sshd-keygen. If so, just change this line from :

RUN /usr/sbin/sshd-keygen

to

RUN ssh-keygen -A

------