FROM --platform=linux/amd64 python:3.10.8
LABEL maintainer="Jose"

ENV PYTHONUNBUFFERED 1
COPY /requirements.txt /tmp/requirements.txt
COPY /api /api
WORKDIR /api
EXPOSE 9000

RUN python -m venv /env && \
    /env/bin/pip install --upgrade pip && \
    /env/bin/pip install -r /tmp/requirements.txt && \
    rm -rf /tmp && \
    adduser --disabled-password --no-create-home django-user

ENV PATH="/scripts:/env/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:/:${PATH}"
RUN chown django-user:django-user -R /api/


USER root
COPY ./terraform/gunicorn/gunicorn-entrypoint.sh /gunicorn-entrypoint.sh
RUN chmod +x /gunicorn-entrypoint.sh
RUN mkdir -p /volume/static
RUN chown -R django-user:django-user /volume/static
RUN chmod -R 755 /volume/static
RUN /env/bin/python manage.py collectstatic --no-input
RUN mkdir -p /var/log/gunicorn && chown -R django-user:django-user /var/log/gunicorn && chmod -R 755 /var/log/gunicorn

USER django-user
VOLUME /volume/static

CMD ["/gunicorn-entrypoint.sh"]