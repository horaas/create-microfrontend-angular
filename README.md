
Iniciar con npm

```bash
  ng new mfe-host --create-application="false"
  cd mfe-host && ng g application host
```

- Por otro lado generar las apps independientes
```bash
  ng new app-mfe-1 && ng new app-mfe-1
```
- Seleccionar Y
- Siguiente seleccionar Module Federation with webpack(classic)
------

A cada proyecto una agregar las siguientes configuraciones

```bash
  ng add @angular-architects/module-federation --project host --port 4200
  ng add @angular-architects/module-federation --project app-mfe-1 --port 4201
  ng add @angular-architects/module-federation --project app-mfe-2 --port 4202
``` 
