FROM nginx:latest
COPY nginx.conf /etc/nginx/
COPY entrypoint /etc/nginx/
COPY build/ /usr/share/nginx/html/
ENTRYPOINT ["/etc/nginx/entrypoint"]
