FROM python:3.14.3-slim@sha256:fb83750094b46fd6b8adaa80f66e2302ecbe45d513f6cece637a841e1025b4ca

ENV HELM_EXPERIMENTAL_OCI=1
ENV REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt

COPY ./migrate_chart.py ./migrate_chart.sh /
ADD https://get.helm.sh/helm-v3.9.1-linux-amd64.tar.gz /

RUN tar zxvf /helm-v3.9.1-linux-amd64.tar.gz && \
    pip install click==7.1.2 && \
    pip install requests==2.24.0 && \
    pip install pyyaml && \
    chmod +x /migrate_chart.sh ./migrate_chart.py

ENTRYPOINT [ "/migrate_chart.py" ]