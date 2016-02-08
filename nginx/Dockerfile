FROM debian:jessie

MAINTAINER Vincent Composieux <vincent.composieux@gmail.com>

ENV NGINX_VERSION 1.9.10-1~jessie

RUN apt-key adv --keyserver hkp://pgp.mit.edu:80 --recv-keys 573BFD6B3D8FBC641079A6ABABF5BD827BD9BF62 \
	&& echo "deb http://nginx.org/packages/mainline/debian/ jessie nginx" >> /etc/apt/sources.list \
	&& apt-get update \
	&& apt-get install -y ca-certificates nginx=${NGINX_VERSION} gettext-base \
	&& rm -rf /var/lib/apt/lists/*

RUN apt-get install openssl

RUN ls -l /etc/nginx/conf.d/
COPY nginx.conf /etc/nginx/
COPY symfony.conf /etc/nginx/conf.d/

RUN rm /etc/nginx/conf.d/default.conf

RUN echo "upstream php-upstream { server php:9000; }" > /etc/nginx/conf.d/upstream.conf

RUN usermod -u 1000 www-data

COPY ssl/nginx* /etc/ssl/certs/
COPY ssl/dhparam.pem /etc/ssl/

#This ADD needs to be uncommented if not using a self-signed cert.
#CA_CERT_BUNDLE
#COPY ssl/ca_cert_bundle.crt /etc/ssl/certs

CMD ["nginx"]

EXPOSE 80
EXPOSE 443
