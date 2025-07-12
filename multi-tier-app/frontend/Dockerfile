# Stage 1: Build
FROM node:18 AS build
WORKDIR /opt/app
COPY package.json package-lock.json ./
RUN npm install
COPY . .
ENV VITE_API_URL=""
RUN npm run build

# Stage 2: Serve
FROM nginx:stable-alpine AS production
COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY --from=build /opt/app/dist /usr/share/nginx/html  
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]

