FROM ubuntu:18.04

LABEL maintainer="Kevin kevin.test@test.com"

RUN apt-get update
RUN apt-get install -y nginx

EXPOSE 80

# ADD static-website-example/ /var/www/html/
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y git
RUN rm -R /var/www/html/*
RUN git clone https://github.com/diranetafen/static-website-example.git /var/www/html/

ENTRYPOINT [ "/usr/sbin/nginx", "-g", "daemon off;" ]
