FROM node:20 AS dependencies

WORKDIR /app

COPY package*.json ./

RUN npm install

FROM node:20-alpine

WORKDIR /app

COPY --from=dependencies /app/node_modules ./node_modules

COPY . .

EXPOSE 3000

CMD ["npm", "start",  "--", "--host", "0.0.0.0"]
