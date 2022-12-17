
# both stages must use alpine
FROM node:16.19.0-alpine as build

WORKDIR /app

COPY package*.json ./
RUN npm ci --silent

COPY . ./

ENV NODE_ENV=production
RUN npm run build

EXPOSE 3000

#-----------------------

FROM nginx:1.23.3-alpine

WORKDIR /usr/share/nginx/html

RUN rm /etc/nginx/conf.d/*
RUN rm -rf /usr/share/nginx/html/*

COPY --from=build /app/build .
COPY --from=build /app/docker/default.conf /etc/nginx/conf.d/default.conf

EXPOSE 80
ENTRYPOINT ["nginx", "-g", "daemon off;"]
