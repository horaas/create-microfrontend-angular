
# Pasos para crear un mircrofrontend en Angluar
## Crear las aplicaciones



Iniciar con npm

```bash
  ng new mfe-host --create-application="false"
  ng new app-mfe-1 && ng new app-mfe-1
```

A cada proyecto una agregar las siguientes configuraciones

```bash
  ng add @angular-architects/module-federation --project mfe-host --port 4200
  ng add @angular-architects/module-federation --project app-mfe-1 --port 4201
  ng add @angular-architects/module-federation --project app-mfe-2 --port 4202
``` 
## En el archivo webpack.config de la app host agregar

```typescript
const { shareAll, withModuleFederationPlugin } = require('@angular-architects/module-federation/webpack');

   
  module.exports = withModuleFederationPlugin({
    name: "host"

    shared: shareAll({ singleton: true, strictVersion: true, requiredVersion: 'auto' })

    remotes: {
       appmfe1: 'http://localhost:4201/remoteEntry.js',
       appmfe2: 'http://localhost:4202/remoteEntry.js'
     },
  });

```
### En la carpeta app agregar el archivo decl.d.ts para declarar los archivos a usar en el proyecto

```typescript
declare module 'appmfe1/routes'
declare module 'appmfe2/routes'
```
- appmfe1:  corresponde al nombre definido en el archivo webpack del host
- routes:  corresponde al nombre definido en la exportación del archivo webpack del app de  microfrontend, en este caso vamos a esportar las rutas para hacer uso de ellas la app host, el cual tambien puede ser el modulo si la version de Angular tiene la estructura de modulos

## Configuracion host routes

### En el archivo app.routes o si tiene el app.routing.module agregar las siguiente linea en las rutas

```typescript
{
  path: "app-mfe-1", loadChildren: () => import('appmfe1/routes').then((module) => module.routes)
},
{
  path: "app-mfe-2", loadChildren: () => import('appmfe2/routes').then((module) => module.routes)
}
```
Si usa module
```typescript
{
  path: "app-mfe-1", loadChildren: () => import('appmfe1/modulename').then((module) => module.modulename)
},
{
  path: "app-mfe-2", loadChildren: () => import('appmfe2/modulename').then((module) => module.modulename)
}

```

# Configuracion microfrontend apps
En el archivo webpack de cada una de las apps colocar lo siguiente

```typescript
const { shareAll, withModuleFederationPlugin } = require('@angular-architects/module-federation/webpack');

module.exports = withModuleFederationPlugin({
    name: "appmfe2"

    exposes: {
      './routes': '.src/app/app.routes
    }
    //si usa module
    exposes: {
      './module': '.src/app/app.module.ts
    }
    shared: shareAll({ singleton: true, strictVersion: true, requiredVersion: 'auto' })

});

```

```typescript
const { shareAll, withModuleFederationPlugin } = require('@angular-architects/module-federation/webpack');

module.exports = withModuleFederationPlugin({
    name: "appmfe1"

    exposes: {
      './routes': '.src/app/app.routes
    }
    //si usa module
    exposes: {
      './module': '.src/app/app.module.ts
    }
    shared: shareAll({ singleton: true, strictVersion: true, requiredVersion: 'auto' })

});

```
## Parte final y probar el proyecto

```bash
ng server [en cada uno de los microfrontend]
ng serve host [en el host principal]
```
