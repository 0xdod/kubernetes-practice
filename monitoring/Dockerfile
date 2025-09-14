FROM node:20-alpine

WORKDIR /app

COPY package.json yarn.lock ./

RUN yarn install --frozen-lock-file --production

COPY app.js .

CMD ["sh", "-c", "node app.js"]
