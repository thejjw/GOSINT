#
# GOSINT Dockerfile
#
# https://github.com/ciscocsirt/GOSINT
#
# Original Written by: Jason Soto @jsitech
# Updated by @thejjw
# 
# Usage:
#
#   sudo docker build -t gosint .
#   sudo docker run -it -p 443:443 gosint


FROM golang:1.16-stretch

#Install Dependencies

RUN echo "deb http://archive.debian.org/debian-archive/debian/ stretch contrib main non-free" > /etc/apt/sources.list

RUN apt-get update \
    && apt-get -y install wget nginx mongodb php7.0-fpm nginx git

# Create SSL Certs for Nginx
RUN mkdir /etc/nginx/ssl \
    && openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/nginx/ssl/nginx.key -out /etc/nginx/ssl/nginx.crt -subj "/C=US/ST=NY/L=NY/O=IT/OU=IT/CN=ssl.gosint"

#Add config file for nginx
ADD default.conf /etc/nginx/sites-available/default

RUN go get github.com/tools/godep \ 
    && go install github.com/tools/godep@latest

WORKDIR /go/src/

#Clone GOSINT Repository

RUN git clone https://github.com/ciscocsirt/GOSINT.git

WORKDIR /go/src/GOSINT/

RUN git checkout 28730c6506366f243edc757a03152d1c7af1a0ca

COPY gosint.sh gosint.sh
RUN chmod 655 gosint.sh

RUN go mod init

RUN go mod tidy

RUN go mod vendor

RUN go build -o gosint \
    && chmod +x gosint

RUN mkdir /var/www/gosint \
    && cp -r website/* /var/www/gosint/ \
    && chown -R www-data:www-data /var/www/gosint/

#start gosint

CMD ["./gosint.sh"]
