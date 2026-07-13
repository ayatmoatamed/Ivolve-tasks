FROM python:3.13-alpine

WORKDIR /

RUN adduser  -D appuser 

COPY . .

RUN pip install -r requirements.txt

EXPOSE 5000

USER appuser

CMD ["python", "app.py"]
