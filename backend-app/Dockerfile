FROM node:22.13.1 AS builder

WORKDIR /app

COPY package* /app

RUN npm install 

# stage 2

FROM node:22-alpine

WORKDIR /app

COPY . /app

COPY --from=builder /app/node_modules /app/node_modules

EXPOSE 3500

CMD ["node", "index.js"]