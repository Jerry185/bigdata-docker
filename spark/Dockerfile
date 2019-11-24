FROM ubuntu:18.04

MAINTAINER saraiva.ufc@gmail.com

WORKDIR /opt/

RUN apt-get -qq update -y

RUN apt-get -qqy install axel openssh-server openssh-client sudo

RUN mkdir /var/run/sshd
RUN echo 'root:password' | chpasswd
RUN sed -i 's/PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config

# SSH login fix. Otherwise user is kicked off after login
RUN sed 's@session\s*required\s*pam_loginuid.so@session optional pam_loginuid.so@g' -i /etc/pam.d/sshd
ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile
RUN echo '%wheel ALL=(ALL) ALL' >> /etc/sudoers

# To download Spark
RUN axel https://archive.apache.org/dist/spark/spark-2.4.0/spark-2.4.0-bin-without-hadoop.tgz
RUN tar -xvzf spark-2.4.0-bin-without-hadoop.tgz
RUN mv spark-2.4.0-bin-without-hadoop ./spark

# To download PySpark
RUN apt-get -qqy install python3-dev python3-pip
RUN pip3 install pyspark==2.4.4

# To download Java
RUN axel http://monalisa.cern.ch/MONALISA/download/java/jdk-8u40-linux-x64.tar.gz
RUN mkdir -p /usr/lib/jvm/
RUN tar -xzf jdk-8u40-linux-x64.tar.gz -C /usr/lib/jvm/

# BASH FILES
COPY ./bash_files/* /root/

# SSH Keys
RUN mkdir -p /root/.ssh/
COPY ./ssh_keys/* /root/.ssh/
RUN cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
RUN chmod 0600 /root/.ssh/id_rsa
RUN /usr/bin/ssh-keygen -A

# ENTRYPOINT 
COPY ./docker-entrypoint.sh docker-entrypoint.sh
RUN chmod +x docker-entrypoint.sh
ENTRYPOINT ["./docker-entrypoint.sh"]