# jenkins-testing-code

FROM centos
RUN yum -y install openssh-server
RUN useradd remote_user && \
    echo remote_user:1234 | chpasswd && \
    mkdir /home/remote_user/.ssh && \
    chmod 700 /home/remote_user/.ssh
COPY remote-key.pub /home/remote_user/.ssh/authorized_keys
RUN chown remote_user:remote_user -R /home/remote_user/.ssh && \
    chmod 600 /home/remote_user/.ssh/authorized_keys
RUN /usr/bin/ssh-keygen -A
EXPOSE 22
RUN rm -rf /run/nologin
CMD /usr/sbin/sshd -D

# Chown all the files to the app user.
RUN chown -R appuser:xyzgroup /usr/app

# Switch to 'appuser'
USER appuser

cd "/home/jenkins" && java  -jar remoting.jar -workDir /home/jenkins -jar-cache /home/jenkins/remoting/jarCache


 sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-*
 sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-*
 dnf distro-sync
