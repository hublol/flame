FROM node:16 as builder

WORKDIR /app

COPY package*.json ./

RUN npm install --production

COPY . .

RUN mkdir -p ./public ./data \
    && cd ./client \
    && npm install --production \
    && npm run build \
    && cd .. \
    && mv ./client/build/* ./public \
    && rm -rf ./client

FROM node:16-alpine

# TODO: modify image to specify UID/GID at runtime
ARG UID=1000
ARG GID=1000

COPY --from=builder /app /app

WORKDIR /app

# Not working for Alpine:
# RUN groupmod -g "${GID}" node && usermod -u "${UID}" -g "${GID}" node

# Busybox syntax (for the base Alpine image)
RUN deluser --remove-home node \
  && addgroup -g "${GID}" -S node \
  && adduser -u "${UID}" -G node -S node

RUN chown -R node:node .

EXPOSE 5005

USER node

ENV NODE_ENV=production
ENV PASSWORD=flame_password

CMD ["node", "server.js"]
