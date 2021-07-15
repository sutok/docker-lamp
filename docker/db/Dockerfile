FROM mysql/mysql-server

ADD my.cnf /etc/
RUN  yum update -y \
&& yum install -y vim git \
&& echo 'alias ll="ls -la"' > /root/.bashrc \
&& cp /usr/share/zoneinfo/Asia/Tokyo /etc/localtime 
