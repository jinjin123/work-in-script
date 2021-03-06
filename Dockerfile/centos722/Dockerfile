FROM centos:7
RUN yum install kde-l10n-Chinese -y
RUN yum install glibc-common -y
RUN localedef -c -f UTF-8 -i zh_CN zh_CN.utf8
ENV LC_ALL zh_CN.UTF-8

ENV NGINX_VERSION 1.12.2
RUN yum install -y openssh-server
RUN yum install -y openssh-*
RUN yum install -y openssl
RUN yum install epel-release  -y
RUN yum install python2-pip -y
RUN yum install crontabs -y
RUN set -x && \
    yum install -y gcc \
    gcc-c++ \
    git \
    wget \
    autoconf \
    pcre-devel \
    openssl \
    openssl-devel \
    automake \
    libtool \
    make \
    cmake &&  yum clean all
    
#Download nginx
RUN set -x && \
    mkdir -p /home/nginx && cd $_ && \
    curl -Lk http://nginx.org/download/nginx-$NGINX_VERSION.tar.gz | gunzip | tar x -C /home/nginx

#Make install nginx
RUN set -x && \
    cd /home/nginx/nginx-$NGINX_VERSION && \
    ./configure --prefix=/usr/local/nginx \
    --user=www --group=www \
    --error-log-path=/var/log/nginx_error.log \
    --http-log-path=/var/log/nginx_access.log \
    --pid-path=/var/run/nginx.pid \
    --with-pcre \
    --with-http_ssl_module \
    --without-mail_pop3_module \
    --without-mail_imap_module \
    --with-http_gzip_static_module && \
    make && make install


#clean
RUN set -x && \
    yum remove -y gcc \
    gcc-c++ \
    autoconf \
    automake \
    libtool \
    make \
    cmake &&  yum clean all

#Update nginx config
ADD nginx.conf /usr/local/nginx/conf/

ADD requirements.txt /etc/
RUN pip install -r  /etc/requirements.txt

## insert conf file
ADD supervisord.conf /etc/
ADD gunicorn.conf /etc/

RUN mkdir -p /app/project/
RUN mkdir -p /var/{log/supervior,log/gunicorn,run/supervisord}

EXPOSE 8000 9001

#start
ADD startup.sh /startup.sh
RUN chmod +x /startup.sh

ENTRYPOINT ["/startup.sh"]
WORKDIR  /app/project/oldb

##code dir need to create logs
#ADD run.sh /run.sh
#ADD ssh_host_rsa_key /etc/ssh/ssh_host_rsa_key
#RUN chmod 600 /etc/ssh/ssh_host_rsa_key
#RUN mkdir -p /root/.ssh
#ADD authorized_keys /root/.ssh/authorized_keys
#RUN chmod 777 /run.sh
#RUN echo "root:111111" |chpasswd

#WORKDIR  /
#CMD ["./run.sh"]
