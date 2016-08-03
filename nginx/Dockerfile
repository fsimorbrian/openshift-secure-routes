FROM registry-nginxinc.rhcloud.com/nginx/rhel7-nginx:1.9.2

COPY default.conf /etc/nginx/conf.d/
RUN mkdir /etc/nginx/ca
RUN mkdir /etc/nginx/pki
RUN openssl genrsa -out /etc/nginx/ca/ca.key 1024
RUN openssl req -x509 -new -key /etc/nginx/ca/ca.key -out /etc/nginx/ca/ca.cer -days 365 -subj "/C=US/ST=California/L=San Diego/O=morbrian/CN=ca.morbrian.com"
RUN openssl genrsa -out /etc/nginx/pki/server.key 1024
RUN openssl req -new -out /etc/nginx/pki/server.req -key /etc/nginx/pki/server.key -subj "/C=US/ST=California/L=San Diego/O=morbrian/CN=nginx.morbrian.com"
RUN openssl x509 -req -in /etc/nginx/pki/server.req -out /etc/nginx/pki/server.cer -CAkey /etc/nginx/ca/ca.key -CA /etc/nginx/ca/ca.cer -days 365 -CAcreateserial -CAserial serial
#RUN openssl pkcs12 -export -out /etc/nginx/pki/server.p12 -inkey /etc/nginx/pki/server.key -in /etc/nginx/pki/server.cer -certfile /etc/nginx/ca/ca.cer

# this chmod line is required on atomic but not on CDK - researching why
RUN chmod -R 777 /var/log/nginx /var/cache/nginx/ \
	&& chmod -R 777 /var/run \
	&& chmod -R 777 /etc/nginx/*
