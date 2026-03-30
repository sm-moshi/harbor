FROM node:16.10.0@sha256:543c034446e58fb3991842e22db7a404f5feb1050cf6126cb551e03cae0e7ac1 as builder

WORKDIR /build_dir

COPY src/portal /build_dir
COPY api/v2.0/swagger.yaml /build_dir/swagger.yaml


RUN apt-get update \
    && apt-get install -y --no-install-recommends python-yaml
RUN npm install --unsafe-perm
RUN npm run postinstall
RUN npm run generate-build-timestamp
RUN node --max_old_space_size=2048 'node_modules/@angular/cli/bin/ng' build --configuration production
RUN python -c 'import sys, yaml, json; y=yaml.load(sys.stdin.read()); print json.dumps(y)' < swagger.yaml > dist/swagger.json
RUN cd app-swagger-ui && npm install --unsafe-perm
RUN cd app-swagger-ui && npm run build

FROM nginx:1.29@sha256:7150b3a39203cb5bee612ff4a9d18774f8c7caf6399d6e8985e97e28eb751c18



COPY --from=builder /build_dir/dist /usr/share/nginx/html
COPY --from=builder /build_dir/app-swagger-ui/dist /usr/share/nginx/html
COPY src/portal/docker-build/nginx.conf /etc/nginx/nginx.conf

EXPOSE 8080
VOLUME /var/cache/nginx /var/log/nginx /run

STOPSIGNAL SIGQUIT

HEALTHCHECK CMD curl --fail -s http://127.0.0.1:8080 || exit 1
USER nginx
CMD ["nginx", "-g", "daemon off;"]
