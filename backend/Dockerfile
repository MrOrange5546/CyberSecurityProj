# Build Stage
FROM node:19-alpine as build

# Install build dependencies
RUN apk update && apk add --no-cache build-base gcc autoconf automake zlib-dev libpng-dev vips-dev > /dev/null 2>&1

# Set environment variables
ARG NODE_ENV=production
ENV NODE_ENV=${NODE_ENV}

# Set working directory
WORKDIR /opt/

# Copy package.json and yarn.lock and install production dependencies
COPY package.json yarn.lock ./
RUN npm install --only=production

# Set PATH to include node_modules binaries
ENV PATH /opt/node_modules/.bin:$PATH

# Set working directory for application code
WORKDIR /opt/app

# Copy application code
COPY . .

# Install global dependencies and build the application
RUN npm install -g --force && \
    npm run build

# Production Stage
FROM node:19-alpine

# Install additional dependencies
RUN apk add --no-cache vips-dev

# Set environment variables
ARG NODE_ENV=production
ENV NODE_ENV=${NODE_ENV}

# Set working directory
WORKDIR /opt/

# Copy node_modules directory from build stage
COPY --from=build /opt/node_modules ./node_modules

# Set working directory for application code
WORKDIR /opt/app

# Copy application code from build stage
COPY --from=build /opt/app ./

# Set PATH to include node_modules binaries
ENV PATH /opt/node_modules/.bin:$PATH

# Change ownership of the /opt/app directory
RUN chown -R node:node /opt/app

# Set user to node
USER node

# Expose port 1337
EXPOSE 1337

# Define command to start the application
CMD ["npm", "start"]
