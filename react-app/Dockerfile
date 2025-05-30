# Stage 1: Build the React app
FROM node:18-alpine as build

WORKDIR /react-app

COPY package.json package-lock.json ./
RUN npm install

COPY . ./
RUN chmod +x ./node_modules/.bin/*
RUN npm run build

# Stage 2: Serve the React app with a lightweight web server
FROM nginx:alpine

COPY --from=build /react-app/build /usr/share/nginx/html

# Expose port 80
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]