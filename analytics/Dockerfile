FROM python:3.8-slim-bullseye

WORKDIR /app
COPY . .

RUN apt update -y
RUN apt install build-essential libpq-dev -y
RUN pip install --upgrade pip setuptools wheel
RUN pip install -r requirements.txt

ENV DB_USERNAME myuser
ENV DB_PASSWORD mypassword
ENV DB_HOST 127.0.0.1
ENV DB_PORT 5433
ENV DB_NAME mydatabase

CMD ["python3", "./app.py"]