FROM python:3.11.4-slim-bullseye as python-base
RUN mkdir {{cookiecutter.project_name}}
WORKDIR  /{{cookiecutter.project_name}}
COPY /pyproject.toml /{{cookiecutter.project_name}}
RUN pip3 install poetry
RUN poetry config virtualenvs.create false
RUN poetry install
COPY . .

HEALTHCHECK --interval=1m --timeout=10s --retries=3 --start-period=30s CMD python tools/scripts/healthcheck.py

CMD ["gunicorn", "-w", "4", "-k", "uvicorn.workers.UvicornWorker", "{{cookiecutter.project_name}}.app:app", "--bind", "0.0.0.0:8000"]