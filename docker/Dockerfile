FROM ubuntu

RUN apt-get update

RUN apt-get install -y apache2 && apt-get clean

ADD index.html /var/www/html/index.html

EXPOSE 80

CMD ["apachectl", "-D", "FOREGROUND"]