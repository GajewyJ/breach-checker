FROM nginx:alpine
LABEL maintainer="90028969+GajewyJ@users.noreply.github.com"
LABEL autor="Jakub Gajewy" data="2026-11-05"
RUN apk add --no-cache nodejs npm && npm install -g htmlhint
COPY . /usr/share/nginx/html
RUN date > /usr/share/nginx/html/build.txt
RUN htmlhint /usr/share/nginx/html/index.html
EXPOSE 80