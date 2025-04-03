# syntax=docker/dockerfile:1.4

FROM node:22 AS development

# Instalar dependencias globales
RUN npm install -g nodemon

# Variables de entorno
ENV CI=true \
    PORT=3000 \
    NODE_ENV=development

# Configurar directorio de trabajo
WORKDIR /code

# Copiar archivos de dependencias
COPY package*.json ./

# Instalar dependencias
RUN npm ci

# Copiar el resto del código
COPY . .

# Comando para desarrollo
CMD ["npm", "run", "dev"]

# Etapa de construcción
FROM development AS builder
RUN npm run build

# Etapa de desarrollo con herramientas adicionales
FROM development AS dev-envs
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    && rm -rf /var/lib/apt/lists/*

# Configurar usuario para desarrollo
RUN useradd -s /bin/bash -m vscode && \
    groupadd docker && \
    usermod -aG docker vscode

# Instalar herramientas de Docker
COPY --from=gloursdocker/docker / /

# Etapa de producción
FROM nginx:1.13-alpine AS production
COPY --from=builder /code/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
