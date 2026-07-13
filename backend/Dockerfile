FROM python:3.13-alpine

WORKDIR /app

RUN adduser -D appuser

RUN pip install flask

COPY . .

EXPOSE 5000

USER appuser

CMD ["python", "app.py"]
