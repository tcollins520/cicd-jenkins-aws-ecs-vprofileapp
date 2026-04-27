FROM nginx
LABEL "Project"="Vprofile"
LABEL "Author"="Tina"

# remvove default nginx config file
RUN rm -rf /etc/nginx/conf.d/default.conf

# copy our config file to config file directory
# our image will be build using this config file
COPY nginvproapp.conf /etc/nginx/conf.d/nginvproapp.conf 