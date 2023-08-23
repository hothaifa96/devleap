FROM python:3.11.4-slim-bullseye
WORKDIR /app
COPY requirements.txt .
RUN  pip3 install -r requirements.txt
COPY main.py .
EXPOSE 5000
CMD [ "python","main.py" ]