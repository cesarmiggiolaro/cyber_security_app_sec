
FROM node:18 as installer

RUN set -xe \
    && apt-get update \
    && apt-get install -y --force-yes wget \
    && mkdir -p /home/juice \
    && wget https://github.com/juice-shop/juice-shop/archive/refs/tags/v14.5.1.tar.gz -O /tmp/juice-shop.tar.gz \
    && tar -xvf /tmp/juice-shop.tar.gz --strip-components=1 -C /home/juice \
    && rm -rf /tmp/juice-shop.tar.gz \
    && apt-get autoremove -y --force-yes \
    && apt-get clean

#RUN apk update && apk add gzip
#ADD https://github.com/juice-shop/juice-shop/archive/refs/tags/$JUICE_SHOP_VERSION.tar.gz /tmp/juice-shop.tar.gz
#RUN tar -zxvf /tmp/juice-shop.tar.gz -C /juice-shop
#RUN wget https://github.com/juice-shop/juice-shop/archive/refs/tags/v14.5.1.tar.gz -O juice-shop.tar.gz tar xfz juice-shop.tar.gz --strip-components=1 -C .

#RUN echo "output: /tmp"
#RUN ls -alh /tmp/
RUN echo "output: /"
RUN ls -alh /
RUN echo "output: /home/juice"
RUN ls -alh /home/juice

WORKDIR /home/juice
RUN npm i -g typescript ts-node
RUN npm install --legacy-peer-deps --save sqlite3
#RUN cd frontend && npm install -g npm@9.5.1 --legacy-peer-deps --save sqlite3 && cd .. && npm run build:frontend && (npm run --silent build:server || cd .)
RUN npm dedupe
RUN rm -rf frontend/node_modules
RUN rm -rf frontend/.angular
RUN rm -rf frontend/src/assets
RUN mkdir logs
RUN chown -R 65532 logs
RUN chgrp -R 0 ftp/ logs/ data/ i18n/
RUN chmod -R g=u ftp/ logs/ data/ i18n/
RUN rm data/chatbot/botDefaultTrainingData.json || true
RUN rm ftp/legal.md || true
RUN rm i18n/*.json || true


FROM node:18-alpine
ARG BUILD_DATE
ARG VCS_REF
LABEL maintainer="Bjoern Kimminich <bjoern.kimminich@owasp.org>" \
    org.opencontainers.image.title="OWASP Juice Shop" \
    org.opencontainers.image.description="Probably the most modern and sophisticated insecure web application" \
    org.opencontainers.image.authors="Bjoern Kimminich <bjoern.kimminich@owasp.org>" \
    org.opencontainers.image.vendor="Open Web Application Security Project" \
    org.opencontainers.image.documentation="https://help.owasp-juice.shop" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.version="11.0.1" \
    org.opencontainers.image.url="https://owasp-juice.shop" \
    org.opencontainers.image.source="https://github.com/bkimminich/juice-shop" \
    org.opencontainers.image.revision=$VCS_REF \
    org.opencontainers.image.created=$BUILD_DATE

RUN addgroup juicer && \
    adduser -D -G juicer juicer

#WORKDIR /juice-shop

COPY --from=installer --chown=juicer:0 /home/juice /home/juicer

RUN echo "output: /home/juicer"
RUN ls -alh /home/juicer

RUN echo "output: ."
RUN ls -alh . 

WORKDIR /home/juicer

USER juicer
#USER 65532
EXPOSE 3000
#cd frontend && npm install && cd .. && npm run build (wd=/home/juicer)
#RUN npm run build (wd=/home/juicer)
RUN npm start

#CMD ["app.ts"]

#CMD ["npm", "start"]

#CMD ["/home/juicer/build/app.js"]

#docker-compose build --progress=plain --no-cache
