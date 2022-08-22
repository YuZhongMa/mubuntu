FROM ubuntu:14.04
MAINTAINER Mlj

RUN DEBIAN_FRONTEND=noninteractive

# Update apt-get local index
# RUN apt-get -qq update

# Install
RUN apt-get -y --force-yes install wget curl git unzip supervisor g++ make nginx mysql-server mysql-client redis-server

RUN bash -c "wget http://getcomposer.org/composer.phar && mv composer.phar /usr/local/bin/composer && chmod +x /usr/local/bin/composer"


# Node
RUN mkdir -p /tmp/node
WORKDIR /tmp/node
ENV NODE_VERSION 12.14.0
ENV NPM_VERSION 6.13.4
RUN curl -SLO "http://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.gz" \
    && tar -xzf "node-v$NODE_VERSION-linux-x64.tar.gz" -C /usr/local --strip-components=1 \
    && rm "node-v$NODE_VERSION-linux-x64.tar.gz" \
    && npm install -g npm@"$NPM_VERSION" \
    && npm cache clear \
    && rm -rf /tmp/*

# MySQL conf
RUN sed -i -e"s/^bind-address\s*=\s*127.0.0.1/bind-address = 0.0.0.0/" /etc/mysql/my.cnf

# nginx conf
RUN echo "\ndaemon off;" >> /etc/nginx/nginx.conf

# ADD files/default /etc/nginx/sites-available/default
# ADD files/supervisord.conf /etc/supervisor/supervisord.conf
ADD files/start.sh /usr/local/bin/start.sh

RUN mkdir -p /tmp/node
WORKDIR /var/multrix
RUN chown www-data:www-data /var/multrix
RUN service mysql start && service redis-server start && mysql -e "create database tickets"

# Expose ports
EXPOSE 80
EXPOSE 443

# Default command for container, start supervisor
CMD ["service", "mysql", "start"]
CMD ["service", "redis-server", "start"]
CMD ["/usr/local/bin/start.sh"]