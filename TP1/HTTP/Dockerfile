FROM httpd:alpine

COPY web/index.html /usr/local/apache2/htdocs/index.html

COPY proxy.conf /usr/local/apache2/conf/extra/proxy.conf

RUN echo "ServerName localhost" >> /usr/local/apache2/conf/httpd.conf \
 && echo "LoadModule proxy_module modules/mod_proxy.so" >> /usr/local/apache2/conf/httpd.conf \
 && echo "LoadModule proxy_http_module modules/mod_proxy_http.so" >> /usr/local/apache2/conf/httpd.conf \
 && echo "Include conf/extra/proxy.conf" >> /usr/local/apache2/conf/httpd.conf

EXPOSE 80
