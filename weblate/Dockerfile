FROM debian:jessie
MAINTAINER Michal Čihař <michal@cihar.com>

# Add user early to get a consistent userid
RUN useradd --shell /bin/sh --user-group weblate

RUN apt-get update
RUN env DEBIAN_FRONTEND=noninteractive apt-get install --no-install-recommends -y ssh curl python-virtualenv python-lxml python-pillow python-psycopg2 python-mysqldb git mercurial gettext && apt-get upgrade && apt-get clean

WORKDIR /app
RUN python -m virtualenv --system-site-packages .
ADD requirements.txt /tmp/requirements.txt
RUN bin/pip install -r /tmp/requirements.txt
RUN rm /tmp/requirements.txt

ADD settings.py /app/etc/
ADD wait-mysql /app/bin/
RUN chmod +x /app/bin/wait-mysql
RUN curl --location --silent --show-error --fail \
        https://github.com/Barzahlen/waitforservices/releases/download/v0.1/waitforservices \
        > /usr/local/bin/waitforservices && \
    chmod +x /usr/local/bin/waitforservices

WORKDIR /tmp

RUN install -d -o weblate -g weblate -m 755 /app/data
RUN ln -s /app/etc/settings.py /app/local/lib/python2.7/site-packages/weblate/settings.py
ENV DJANGO_SETTINGS_MODULE weblate.settings
ENV START_CMD "/app/bin/django-admin"

VOLUME ["/app/etc", "/app/data"]
USER weblate

EXPOSE 8000
ENTRYPOINT ["/app/bin/wait-mysql"]
CMD ["runserver", "0.0.0.0:8000"]
