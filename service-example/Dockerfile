FROM node:10-alpine

WORKDIR /app

COPY package.json .
COPY server.js .

RUN npm i

EXPOSE 3001

CMD ["npm", "start"]
