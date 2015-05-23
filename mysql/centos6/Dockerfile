# "ported" by Adam Miller <maxamillion@fedoraproject.org> from
#   https://github.com/fedora-cloud/Fedora-Dockerfiles
#
# Originally written for Fedora-Dockerfiles by
#   scollier <scollier@redhat.com>

FROM centos:centos6
MAINTAINER The CentOS Project <cloud-ops@centos.org>


RUN yum -y update; yum clean all
RUN yum -y install epel-release; yum clean all
RUN yum -y install mysql-server mysql pwgen supervisor bash-completion psmisc net-tools; yum clean all

ADD ./start.sh /start.sh
ADD ./config_mysql.sh /config_mysql.sh
ADD ./supervisord.conf /etc/supervisord.conf

# RUN echo %sudo	ALL=NOPASSWD: ALL >> /etc/sudoers

RUN chmod 755 /start.sh
RUN chmod 755 /config_mysql.sh
RUN /config_mysql.sh

RUN yum -y install wget
RUN yum -y install tar
RUN yum install -y openssh-server

RUN useradd worker
RUN echo 'worker:worker' |chpasswd
RUN echo 'root:root' |chpasswd

RUN /sbin/service sshd start
RUN /sbin/service sshd stop

RUN cd /home 
RUN wget https://projects.tigase.org/attachments/download/1406/tigase-server-5.2.1-b3461-dist-max.zip
RUN tar zvxf tigase-server-5.2.1-b3461-dist-max.zip
RUN mv tigase-server-5.2.1-b3461-dist-max.zip tigase
RUN cd tigase
RUN chmod u+x ./scripts/tigase.sh 
RUN ./scripts/db-create-mysql.sh tigase tigase tigasedb root mysqlPassword localhost
RUN ./scripts/tigase.sh start etc/tigase.conf

EXPOSE 5222
EXPOSE 22
EXPOSE 3306
CMD ["/usr/sbin/sshd", "-D"]
CMD ["/bin/bash", "/start.sh"]
