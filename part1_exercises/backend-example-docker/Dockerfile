# For exercises 1.5 and 1.6

FROM node:latest
EXPOSE 8000

WORKDIR /homie
COPY / .

RUN npm install nodemon@latest -D
RUN touch /homie/logs.txt

CMD FRONT_URL=http://localhost:5000 npm start