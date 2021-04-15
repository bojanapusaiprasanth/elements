FROM nginx:latest

LABEL Maintainer=cloudelements

COPY ./nginx /var/opt/nginx

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]