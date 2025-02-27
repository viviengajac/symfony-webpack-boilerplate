# Symfony Boilerplate avec Webpack Encore - TypeScript - SASS
Ce guide explique comment configurer un nouveau projet Symfony avec Webpack Encore pour gérer les assets. Le projet intègre FileLoader, TypeScript, et SASS.

## Prérequis
Doivent être installés sur votre machine :
 - Node.js
 - npm
 - Composer
 - Symfony CLI

## À noter
La version de Symfony peut varier avec le temps. Adaptez l'étape **2. Création du projet Symfony** pour correspondre à la version souhaitée.  
Retrouvez [la dernière version de Symfony ici](https://symfony.com/doc/current/setup.html)

## Étapes
### Initialisation du projet
1. Ouvrez un terminal dans le répertoire où le projet sera créé (ex: ```C:/projets```)
2. Création du projet Symfony :  
```bash
symfony new mon-projet --version="7.2.x" --webapp
```
3. Dans le terminal, allez à la racine du projet :
```bash
cd mon-projet
```
4. Désinstallation de **AssetMapper** (**Ux-turbo** et **Stimulus** sont configurés pour asset-mapper et doivent être désinstallés) :  
```bash
composer remove symfony/ux-turbo symfony/asset-mapper symfony/stimulus-bundle
```
5. Installation de **Webpack Encore** (**Ux-turbo** et **Stimulus** sont réinstallés et configurés pour Webpack) :  
```bash
composer require symfony/webpack-encore-bundle symfony/ux-turbo symfony/stimulus-bundle
```
6. Installation des dépendances :
```bash
npm install
```

### Configuration de Webpack Encore
1. Ouvrez le projet avec votre IDE (ex: VSCode)
2. Configuration de **FileLoader**, qui permet de copier les assets dans ```public/build``` :
 - Créer un dossier ```assets/images```
 - Dans le fichier ```webpack.config.js``` à la racine du projet, ajouter les lignes suivantes (12 à 15) :  
```javascript
Encore
	// directory where compiled assets will be stored
	.setOutputPath('public/build/')
	.copyFiles({
	    from: './assets/images',
	    to: 'images/[path][name].[ext]',
	})
	// public path used by the web server to access the output path
	.setPublicPath('/build')
```
3. Toujours dans ```webpack.config.js```, décommenter les lignes 64 et 67 pour activer **TypeScript** et **SASS** : 
```javascript
// enables Sass/SCSS support
.enableSassLoader()
// uncomment if you use TypeScript
.enableTypeScriptLoader()
```
4. Dans ```/assets```, créer un dossier "ts" (soit un dossier ```/assets/ts```)
5. Dans le dossier ```/assets/ts```, créer un fichier ```tsconfig.json``` avec comme contenu :
```typescript
{}
```
6. Dans ```/assets/styles```, renommer le fichier ```app.css``` en ```app.scss```
7. Dans ```/assets```, créer un dossier "js" (soit un dossier ```/assets/js```)
8. Déplacer le fichier ```/assets/app.js``` dans ```assets/js``` (soit ```/assets/js/app.js```)
9. Déplacer le fichier ```/assets/bootstrap.js``` dans ```assets/js``` (soit ```/assets/js/bootstrap.js```)
10. Dans le fichier ```/assets/js/app.js```, modifier le lien ```app.css``` en ```app.scss``` en remontant d'un niveau à la ligne 10 :  
```javascript
// any CSS you import will output into a single css file (app.css in this case)
import '../styles/app.scss';
```
11. Dans le fichier ```/assets/js/bootstrap.js```, modifier la ligne 5 pour remonter d'un niveau :  
```javascript
'@symfony/stimulus-bridge/lazy-controller-loader!../controllers',
```
12. Dans le fichier ```webpack.config.js``` à la racine du project, modifier le lien ```assets/app.js``` en ```assets/js/app.js``` à la ligne 27 :   
```javascript
/*
	* ENTRY CONFIG
 *
 * Each entry will result in one JavaScript file (e.g. app.js)
 * and one CSS file (e.g. app.css) if your JavaScript imports CSS.
 */
.addEntry('app', './assets/js/app.js')
```

### Lancement de Webpack et installation des dernières dépendances
Il s'agit ici d'installer les bonnes versions de **FileLoader**, **TypeScript** et **SASS**.  
En lançant Webpack, on devrait avoir des messages d'erreurs. Pas de panique, ces messages nous indiquent ce qu'il faut faire pour installer les dernières versions des dépendances manquantes.  
1. Ouvrez un terminal à la racine du projet (ex: ```C:/projets/mon-projet```)
2. Démarrer Webpack :
```bash
npm run watch
```
3. Copier la commande du message d'erreur et installer la dépendance  
**Exemples de commandes | Attention à la version !**
```bash
npm install file-loader@^6.0.0 --save-dev
```
```bash
npm install sass-loader@^16.0.1 sass --save-dev
```
```bash
npm install typescript ts-loader@^9.0.0 --save-dev
```
4. Répéter les étapes 2 et 3 jusqu'au lancement de Webpack sans erreur
5. Dans un second terminal toujours à la racine du projet, démarrer le serveur de développement de Symfony :
```bash
symfony server:start
```
6. Dans un navigateur, aller à l'url [localhost:8000](http://localhost:8000)

## Utilisation de Webpack Encore
### Compilation automatique des assets
Pour compiler les assets automatiquement lorsque ceux-ci sont modifiés, laisser tourner le terminal qui exécute ```npm run watch```

### Utilisation des assets
Pour utiliser les assets dans Twig, faire appel à la fonction ```asset()``` :  
**Exemple avec une image**  
```html
{{ asset('build/images/mon-image.png') }}
```
**À noter**  
Ici on utilise le dossier ```/assets/images```, mais on peut tout aussi bien créer d'autres dossiers pour d'autres types de contenu, par exemple ```/assets/pdf```.  
Il suffit de créer le dossier et de le déclarer dans ```webpack.config.js``` comme nous l'avons fait pour le dossier ```/assets/images``` en ajoutant une fonction ```.copyFiles()``` :  
```javascript
.copyFiles({
	    from: './assets/pdf',
	    to: 'pdf/[path][name].[ext]',
	})
```

### Utilisation des scripts JavaScript et TypeScript
Les scripts sont placés dans des dossiers spécifiques :  
 - Fichier JavaScript  : ```/assets/js```
 - Fichier TypeScript : ```/assets/ts```

Pour utiliser un script, il suffit de le déclarer dans ```webpack.config.js``` en lui donnant un nom unique et le chemin vers le fichier :  
**Fichier JavaScript**
```javascript
.addEntry('exempleUn', './assets/js/exempleUn.js')
```
**Fichier TypeScript**
```javascript
.addEntry('exempleDeux', './assets/ts/exempleDeux.js')
```
Enfin, pour insérer un script dans une page Twig :  
```html
{% block javascripts %}
    {{ encore_entry_script_tags('exempleUn') }}
    {{ encore_entry_script_tags('exempleDeux') }}
{% endblock %}
```

## Liens utiles
[Documentation de Symfony](https://symfony.com/doc/current/index.html)  
[Documentation de Webpack Encore](https://symfony.com/doc/current/frontend/encore/index.html)  
[Documentation de Twig](https://twig.symfony.com/doc/)