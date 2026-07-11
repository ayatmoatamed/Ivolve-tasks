FROM python:3.15.0b3-alpine3.24

WORKDIR /app

RUN pip install flask

ENV APP_MODE=production

ENV APP_REGION=canada-west

COPY app.py .

CMD ["python", "app.py"]

EXPOSE 8080
