FROM node:12.16.2-alpine3.11 AS node
ENV NODE_OPTIONS --unhandled-rejections=strict
WORKDIR /app



#
# Stage: Production NPM install
#
FROM node AS npm-prod

COPY jest.config.js \
  package.json \
  package-lock.json \
  ./

RUN npm install --production



#
# Stage: Development NPM install
#
FROM npm-prod AS npm-dev

RUN npm install



#
# Stage: Development environment
#
FROM node AS dev
ENV NODE_ENV=development
ENV DEBUG=http:*

COPY .eslintignore \
 .eslintrc.js \
 tsconfig.json \
 tsconfig.lint.json \
 ./
COPY --from=npm-dev /app/ .
COPY test/ test/
COPY src/ src/
COPY static/ static/

CMD ["npm", "run", "start:dev"]



#
# Stage: Production build
#
FROM dev AS build-prod
ENV NODE_ENV=production

RUN npm run build



#
# Stage: Production environment
#
FROM node AS prod
ENV NODE_ENV=production
ENV DEBUG=http:*

COPY --from=npm-prod /app/ .
COPY --from=build-prod /app/build/ build/
COPY static/ static/

HEALTHCHECK --interval=5s --timeout=1s \
  CMD wget --quiet --tries=1 --spider http://localhost:80/ping || exit 1

CMD ["npm", "run", "start"]
