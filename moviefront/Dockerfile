# Stage 1, Build Angular frontend
#FROM node:latest
#COPY ./moviefront .
#RUN npm install -g npm@7.17.0 @angular/cli@latest
#RUN ng build --prod

# Stage 2, Build Nginx backend
FROM nginx:latest
COPY ./movie-app /usr/share/nginx/html
COPY movieapp.conf /etc/nginx/conf.d/movieapp.conf
