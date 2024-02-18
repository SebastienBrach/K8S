FROM node:18.16.0-alpine

WORKDIR /usr/src/app

COPY . ./

RUN npm install

EXPOSE 3000

CMD [ "node", "server.js" ]