# Pasos para deployar tu backend (por si no te quedó claro en el video)

A tener en consideración: Los archivos distintos a la API de ayudantia-creacion-api-template son `src/config/config.js` y `src/models/index.js`

## Añade a tu aplicación el workflow para Render

En primer lugar, lo que tienes que hacer es, luego de tener creada tu api, agregar `.github/workflows/render.yml` en el directorio principal de su api. Este archivo debe contener lo siguiente:
```
name: Deploy to Render

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - name: Set up Node.js
      uses: actions/setup-node@v2

    - name: Install dependencies
      run: yarn

  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy to Render
        uses: johnbeynon/render-deploy-action@v0.0.8
        with:
          service-id: ${{ secrets.MY_RENDER_SERVICE_ID }}
          api-key: ${{ secrets.MY_RENDER_API_KEY }}
```
Lo que hace esto es correr un workflow en el repositorio indicado usando los secrets de MY_RENDER_SERVICE_ID y MY_RENDER_API_KEY.

## Inicia sesión en Render y crea un Web Service

En este paso debes iniciar sesión en Render con tu cuenta de GitHub y luego crear un Web Service, este puede tener el nombre que quieras y debes agregar:
- Build command: `yarn install`
- Start command: `yarn sequelize-cli db:migrate && yarn dev`

En este último puedes agregar el comando de las seeds, pero sería sólo para poblar la base de datos.

## Crea una Base de Datos de PostgreSQL en Render y conéctala a tu Web Service

Aquí vas a +New en el Dashboard de Render y vas a la sección de PostgreSQL, le agregas un nombre y luego la creas. Luego, debes esperar unos minutos a que esté totalmente configurada y obtener los parámetros que se necesitan para las variables de entorno de la aplicación. Estas se obtienen en "Connections":
- DB_HOST: En External Database URL, sacar los datos desde "dpg" hasta "render.com".
- DB_NAME: Database
- DB_PASSWORD: Password
- DB_USER: Username

Todo esto se debe incluir en "Environments" en el Web Service. Cabe recalcar que los nombres de las variables de entorno deben ser las mismas que ustedes les asignen cuando corran la aplicación en local.

## Agrega los Secrets a tu repositorio

En este paso debes obtener los datos de MY_RENDER_API_KEY y MY_RENDER_SERVICE_ID. Para esto, debes hacer lo siguiente:
- MY_RENDER_API_KEY: Debes ir a My Profile -> Account Settings -> API KEYS y seleccionas en crear.
- MY_RENDER_SERVICE_ID: Debes ir a Dashboard -> My Web Service -> Settings -> Deploy Hook. Aquí debes tomar los valores entre "srv" hasta "?key" sin incluir esto último.

Ambos valores debes agregarlos como secrets en "Repositorio de GitHub" -> "Settings" -> "Secrets and variables" -> "Actions" -> "Repository secrets".

A partir de aquí, queda listo el deployment de tu aplicación en Render.

Ayudantía realizada por @PSepulvedaS