FROM node:25-alpine
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm install
RUN npm list
COPY . .
# Antes: ENV VITE_API_URL=http://heroes-nestjs-backend:3000
# Ahora: Apunta a la ruta que Nginx interceptará.
ENV VITE_API_URL=/api
RUN npm run build

FROM nginx:alpine
COPY --from=0 /app/dist /usr/share/nginx/html
RUN rm /etc/nginx/conf.d/default.conf
COPY nginx/nginx.conf /etc/nginx/conf.d
CMD ["nginx", "-g", "daemon off;"]