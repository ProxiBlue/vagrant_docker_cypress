FROM phusion/baseimage:master-amd64
LABEL maintainer="Lucas van Staden sales@proxiblue.com.au"

ENV DEBIAN_FRONTEND=noninteractive

RUN useradd -G www-data --create-home --shell /bin/bash vagrant && \
    echo root:vagrant | chpasswd && \
    echo vagrant:vagrant | chpasswd && \
    mkdir -p /home/vagrant/.composer && \
    mkdir -p /home/vagrant/.ssh
ADD https://raw.githubusercontent.com/mitchellh/vagrant/master/keys/vagrant.pub /home/vagrant/.ssh/authorized_keys
ADD https://getcomposer.org/installer /tmp/composer-setup.php
ADD common/.inputrc /home/vagrant/
ADD common/disable_manpages /etc/dpkg/dpkg.cfg.d/01_nodoc
ADD sudoers.d/01_vagrant /etc/sudoers.d/
ADD common/wrap_chrome_binary /tmp/
RUN chown -R vagrant:vagrant /home/vagrant && \
    chown -R vagrant:vagrant /home/vagrant/.* && \
    chmod 0600 /home/vagrant/.ssh/* && \
    chmod 0700 /home/vagrant/.ssh && \
    apt-get update && \
    apt-get -y install software-properties-common --no-install-recommends tzdata curl sudo wget && \
    DEBIAN_FRONTEND=noninteractive && \
    wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add - && \
        echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list && \
    add-apt-repository ppa:ondrej/php && \
    apt-get update && apt -y upgrade && sudo apt remove cmdtest && \
    apt-get -y --allow-unauthenticated --no-install-recommends install \
    ca-certificates \
    git \
    nginx \
    google-chrome-stable \
    pv \
    openssh-server \
    supervisor && \
    rm -rf /var/cache/apt/archives && \
    apt-get update && \
    apt-get -y --allow-unauthenticated --no-install-recommends install gettext-base && \
    chmod 0400 /etc/sudoers.d/01_vagrant&& \
    ssh-keygen -A && \
    apt -y autoremove && \
    rm -rf /var/cache/apt/archives && \
    rm -rf /usr/share/doc/ && \
    rm -rf /usr/share/man/ && \
    rm -rf /usr/share/locale && \
    mkdir -p /var/log/sshd/ && \
    mkdir -p /var/log/nginx/ && \
    mkdir -p /etc/nginx/snippets && \
    ssh-keyscan  github.com >> ~/.ssh/known_hosts && \
    echo "nameserver 1.1.1.1" >/etc/resolv.conf && \
    echo "nameserver 8.8.8.8" >>/etc/resolv.conf && \
    bash /tmp/wrap_chrome_binary

ADD common/self-signed.conf /etc/nginx/snippets/self-signed.conf
ADD common/ssl-params.conf /etc/nginx/snippets/ssl-params.conf
ADD common/supervisord-sshd.conf /etc/supervisor/conf.d/supervisord-sshd.conf
ADD common/supervisord-nginx.conf /etc/supervisor/conf.d/supervisord-nginx.conf
ADD ssl/certs/dhparam.pem /etc/ssl/certs/dhparam.pem
ADD ssl/certs/nginx-selfsigned.crt /etc/ssl/certs/nginx-selfsigned.crt
ADD ssl/private/nginx-selfsigned.key /etc/ssl/private/nginx-selfsigned.key

EXPOSE 22 80 443

CMD ["/usr/bin/supervisord", "-n"]
