FROM node:20-alpine AS build
# Build stage
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build -- --configuration production

# Nginx stage
FROM nginx:alpine
COPY --from=build /app/dist/angular-15-crud /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

WORKDIR /usr/src/app

# 1) Copy only package files
COPY package*.json ./

# 2) Install prod dependencies
RUN npm install --only=production

# 3) Copy the rest of the source code
COPY . .
