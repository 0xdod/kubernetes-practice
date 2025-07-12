FROM python:3.10-slim
WORKDIR /opt/dojo
RUN apt-get update && apt-get install -y curl
RUN curl -sSL https://install.python-poetry.org | POETRY_HOME="/opt/poetry" POETRY_VERSION=1.8.3 python3 -
ENV PATH="/opt/poetry/bin:${PATH}"
COPY pyproject.toml poetry.lock ./
RUN poetry install
ENV PYTHONPATH="/opt/dojo"
COPY . .
RUN curl -o wait-for-it.sh https://raw.githubusercontent.com/vishnubob/wait-for-it/master/wait-for-it.sh && chmod +x wait-for-it.sh
EXPOSE 8000
CMD ["sh", "-c", "./wait-for-it.sh -s demo-postgres:5432 -- poetry run bash ./prestart.sh && poetry run uvicorn --host 0.0.0.0 --port 8000 app.main:app --reload"]
# CMD ["sh", "-c", "poetry run bash ./prestart.sh && poetry run uvicorn --host 0.0.0.0 --port 8000 app.main:app --reload"]
