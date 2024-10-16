FROM node:18 AS build

RUN npm install -g @angular/cli

# Définir le répertoire de travail
WORKDIR /app

# Copier le package.json et le package-lock.json depuis le dossier frontend dans /app
COPY frontend/package.json frontend/package-lock.json ./

# Installer les dépendances
RUN npm install

# Désactiver l'analytique

# Copier tout le contenu du répertoire frontend dans le conteneur
COPY frontend .

# Donner les permissions d'exécution à Angular CLI
RUN chmod +x node_modules/.bin/ng

# Construire le projet pour la production
RUN npm run build-prod


# Utiliser une image nginx pour servir l'application Angular
#FROM nginx:alpine
#COPY --from=build /app/dist/mon-projet-angular /usr/share/nginx/html

# Exposer le port 8080
#EXPOSE 8080


EXPOSE 4200

## Démarrer npm
CMD ["npm", "run", "start"]