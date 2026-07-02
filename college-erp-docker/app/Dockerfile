# --------------------------------------------------
# Sanskar College ERP - Web Frontend
# Simple static site served by Nginx inside Docker
# --------------------------------------------------
FROM nginx:alpine

# Remove default nginx welcome page
RUN rm -rf /usr/share/nginx/html/*

# Copy our ERP website into nginx's web root
COPY . /usr/share/nginx/html

# Simple healthcheck so 'docker ps' shows container health
HEALTHCHECK --interval=30s --timeout=3s CMD wget -qO- http://localhost/ || exit 1

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
