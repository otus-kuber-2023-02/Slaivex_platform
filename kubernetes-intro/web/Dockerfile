FROM nginx:1.23.1-alpine

## Set application build path
ARG APP_DIR='/app'

# Set permissions so that the container runs without root access
ARG USER_NAME=www
ARG UID=1001
ARG GID=1001

# Create service user
RUN adduser -D -u ${UID} ${USER_NAME}

# Configure nginx server
COPY ./conf/nginx/nginx.conf /etc/nginx/nginx.conf
COPY ./conf/nginx/default.conf /etc/nginx/conf.d/default.conf

## Set working directory and add application
WORKDIR ${APP_DIR}
COPY --chown=${UID}:${GID} ./app ${APP_DIR}

# Network properties
EXPOSE 8000

# Setting entrypoint for containers
CMD ["nginx", "-g", "daemon off;"]