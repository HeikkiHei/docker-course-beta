# For exercise 1.5

FROM node:latest
EXPOSE 8000

WORKDIR /homie
COPY / .

RUN npm install
RUN touch /homie/logs.txt

CMD npm start