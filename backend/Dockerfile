# --- ETAPA 1: Construcción (Build Stage) ---
# Usa una imagen Node.js completa para la compilación
FROM node:25-alpine AS build

# Establece el directorio de trabajo dentro del contenedor
WORKDIR /usr/src/app

# Copia los archivos de manifiesto del proyecto (package.json y package-lock.json o yarn.lock)
# Esto permite que Docker cachee esta capa si las dependencias no cambian
COPY package*.json ./

# Instala todas las dependencias (incluyendo las de desarrollo)
# 'npm ci' es preferible a 'npm install' en entornos CI/CD/Docker
RUN npm ci

# Copia el resto del código fuente
COPY . .

# Ejecuta la compilación de NestJS (genera los archivos JavaScript en la carpeta 'dist')
RUN npm run build

# --- ETAPA 2: Producción (Production Stage) ---
# Usa una imagen base más ligera (solo para ejecución)
FROM node:25-alpine AS production

# Establece la variable de entorno a producción
# ENV NODE_ENV production

# Establece el directorio de trabajo
WORKDIR /usr/src/app

# Copia solo los archivos de manifiesto de la 'build stage'
COPY --from=build /usr/src/app/package*.json ./

# Instala *solo* las dependencias de producción
RUN npm ci --omit=dev

# Copia el código compilado (la carpeta 'dist') de la 'build stage'
# Esto es lo crucial: solo se mueve el resultado de la compilación
COPY --from=build /usr/src/app/dist ./dist
COPY /public ./public 

#Expone el puerto por defecto de NestJS (o el que uses)
EXPOSE 3000

# Comando para iniciar la aplicación
# NestJS usa 'dist/main.js' como punto de entrada por defecto
CMD [ "node", "dist/main.js" ]