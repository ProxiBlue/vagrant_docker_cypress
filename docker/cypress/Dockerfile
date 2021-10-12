FROM cypress/browsers:chrome69

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y openssh-server libgbm-dev supervisor sudo && \
userdel node && \
useradd -u 1000 -G www-data --create-home --shell /bin/bash vagrant && \
    echo vagrant:vagrant | chpasswd && \
    mkdir -p /home/vagrant/.ssh && \
    mkdir /var/log/sshd
ADD https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant.pub /home/vagrant/.ssh/authorized_keys
ADD supervisord-sshd.conf /etc/supervisor/conf.d/supervisord-sshd.conf
ADD sudoers.d/01_vagrant /etc/sudoers.d/
ADD wrap_chrome_binary /tmp/
RUN /etc/init.d/ssh start && \
    chown -R vagrant:vagrant /home/vagrant && \
    chown -R vagrant:vagrant /home/vagrant/.* && \
    chmod 0600 /home/vagrant/.ssh/* && \
    chmod 0700 /home/vagrant/.ssh && \
    bash /tmp/wrap_chrome_binary
USER vagrant
ENV HOME /home/vagrant
WORKDIR /home/vagrant
RUN npm install cypress@8.6.0 && echo "export DISPLAY=:0" >>/home/vagrant/.bashrc
USER root
#RUN $(npm bin)/cypress run --browser chrome
CMD ["/usr/bin/supervisord", "-n"]
