# Install dependencies only when needed
FROM node:18-alpine AS deps

# install dependencies
WORKDIR /usr/src/app
COPY package.json pnpm-lock.yaml ./
RUN npx pnpm i

# Rebuild the source code only when needed
FROM node:18-alpine AS builder
WORKDIR /usr/src/app
COPY . .
COPY --from=deps /usr/src/app/node_modules ./node_modules
#Copy your ENV file
COPY ./.env.example ./.env
RUN npx pnpm run sync
RUN npx pnpm run build

# Production image, copy all the files and run next
FROM node:18-alpine AS runner
WORKDIR /usr/src/app

ENV NODE_ENV production

RUN adduser -S ory -D -u 10000 -s /bin/nologin

COPY --from=builder /usr/src/app/node_modules ./node_modules
COPY --from=builder /usr/src/app/package.json ./package.json

USER 10000

EXPOSE 3000

CMD ["node", "./build"]