FROM node:8.16.2-alpine

WORKDIR /app

COPY . .

RUN npm install && \
    npm audit fix -f

EXPOSE 4000

CMD npm start

