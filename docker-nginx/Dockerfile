FROM nginx:stable-alpine

# Remove default.conf
RUN rm -f /etc/nginx/conf.d/default.conf

# Copy reverse-proxy.conf
COPY conf/reverse-proxy.conf /etc/nginx/conf.d/reverse-proxy.conf

EXPOSE 8888/tcp
