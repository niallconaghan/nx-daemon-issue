# Stage 1: Base Stage
FROM node:20.10.0-alpine3.18 as base
WORKDIR /usr/src/app

# Install build dependencies
RUN apk add --no-cache g++ make py3-pip

# Copy package.json and package-lock.json to leverage Docker layer caching
COPY package.json package-lock.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Stage 2: Development Stage
FROM base as development

# Install nx globally
RUN npm install -g nx

# Command for development
CMD ["npx", "nx", "serve", "nx-daemon-issue"]

# Stage 3: Build Stage
FROM base as build
WORKDIR /usr/src/app

# Build the application
RUN npx nx run nx-daemon-issue:build

# Stage 4: Production Stage
FROM node:20.10.0-alpine3.18 as production
WORKDIR /usr/src/app

COPY package.json package-lock.json ./

RUN npm ci --omit=dev

COPY --from=development /usr/src/app/dist/apps/nx-daemon-issue ./dist/apps/nx-daemon-issue

CMD ["node", "dist/apps/nx-daemon-issue/main"]
