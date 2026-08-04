FROM alpine:3.18

RUN apk add --no-cache nginx

COPY index.html /usr/share/nginx/html/index.html

COPY nginx.conf /etc/nginx/http.d/default.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
