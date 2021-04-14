{% if cookiecutter.advanced_docs == "yes" -%}
FROM curlimages/curl:latest as static

RUN mkdir /home/curl_user/static && cd /home/curl_user/static && curl \
    -O "https://cdn.jsdelivr.net/npm/swagger-ui-dist@3/swagger-ui-bundle.js" \
    -O "https://cdn.jsdelivr.net/npm/swagger-ui-dist@3/swagger-ui.css" \
    -O "https://raw.githubusercontent.com/go-swagger/go-swagger/master/docs/favicon.ico" \
    -O "https://cdn.jsdelivr.net/npm/redoc@next/bundles/redoc.standalone.js"
{% endif %}

FROM python:3.11.4-slim-bullseye AS builder

ARG USER=user
ARG UID=1000

# Extend PATH to use pip-installed tools from shell (like alembic)
ENV PATH="${PATH}:/home/$USER/.local/bin"

# Add non-root user
RUN adduser --uid $UID --disabled-password --gecos '' $USER
USER $USER
WORKDIR /home/$USER

{% if cookiecutter.advanced_docs == "yes" -%}
# Copy static files
ENV API_DOCS_STATIC_PATH="/home/$USER/static"
RUN mkdir static
COPY --from=static --chown=$USER /home/curl_user/static/* ./static/
{% endif %}

ENV PYTHONUNBUFFERED 1

WORKDIR /app

COPY poetry.lock pyproject.toml ./
RUN pip install --upgrade pip && \
    pip install poetry && \
    poetry config virtualenvs.create false

ARG DEV=false
RUN if [ "$DEV" = "true" ] ; then poetry install --with dev ; else poetry install --only main ; fi

COPY ./app/ ./
# Copy project files
WORKDIR /home/$USER/app

#EXPOSE 8080
#CMD uvicorn main:app --host 0.0.0.0 --port 8080

HEALTHCHECK --interval=1m --timeout=10s --retries=3 --start-period=30s CMD python tools/scripts/healthcheck.py
CMD ["python", "-u", "."]