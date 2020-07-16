# Buscador NY Times (buscadornytimes)

Buscador de noticias del NY Times

## Install the dependencies
```bash
npm install
```

### Start the app in development mode (hot-code reloading, error reporting, etc.)
```bash
quasar dev
```


### Build the app for production
```bash
quasar build
```

### Configurar buscador
Crear cuenta en https://developer.nytimes.com/accounts/create
Insertar la APIKEY obtenida en src/pages/buscador.vue en la línea 171 sustituyendo 
```bash
/*Inserta tu APIKEY aqui*/
```
Ejemplo de línea:
```bash
"&api-key=Sd9fdfj9dudfjfd9fdjfl")
```
