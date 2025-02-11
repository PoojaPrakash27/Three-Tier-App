FROM node:14 AS builder

WORKDIR /app

COPY package* /app

RUN npm install 

# stage 2

FROM node:14-alpine

WORKDIR /app

COPY . /app

COPY --from=builder /app/node_modules /app/node_modules

EXPOSE 3000

CMD ["npm", "start"]