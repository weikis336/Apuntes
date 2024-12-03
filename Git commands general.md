## Añadir nuestas credenciales de github en el equipo, si hay más de un usuario en el sistema operativo omitimos el parámetro --global:

```git
git config --global user.name "nombre_de_github"
git config --global user.email "email_de_github"
```
## Iniciar un repositorio git en local, lanzamos este comando habiendo abierto la carpeta del proyecto
```git
git init
```
## Conectar con nuestro repositorio de github
```git
git remote add origin <URL_REPOSITORIO_GITHUB>
```
## Ver en qué rama estamos y si quedan archivos por commitar
```git
git status
```
## Añadir archivos que queremos subir con el commit:
```git
git add Readme.md (por nombre de archivo) 
git add -A || git add . (todos los archivos)
```
## Añadir un mensaje al commit
```git
git commit -m "Creación del fichero Readme"
```
## Publicar la rama que tenemos en el repositorio local en el repositorio remoto, la rama por defecto se llama "master":
```git
git push -u origin master
```
## Ver el historial de commits realizados:
```git
git log 
```
## Crear una nueva rama en nuestro repositorio local y cambiar a ella
```git
git checkout -b nombre_de_la_rama
```
## Publicar la nueva rama que tenemos en el repositorio local en el repositorio remoto:
```git
git push -u origin nombre_de_la_rama 
```
## Ver qué ramas tenemos creadas:
```git
git branch
```
## Cambiar de rama
```git
git checkout nombre_de_la_rama
```
## Mezclar los cambios de una rama con los archivos de la rama en la que nos encontramos:
```git
git merge nombre_de_la_rama
git push
```
## Volver a un commit del pasado:
```git
git checkout 3ad05a613cb06aadbec4bf45ac6eac868b858ebb (este número se saca de github)
git checkout HEAD^ (ir al commit previo)
git checkout --detach nombre_de_rama (ir al último commit de una rama)
```
## Hacer modificaciones sobre un checkout del pasado en una nueva rama:
```git
git switch -c nombre_de_la_nueva_rama
```
## Eliminar una rama:
```git
git branch -d nombre_de_la_rama_a_eliminar (en el repositorio local)
git push origin :nombre_de_la_rama_a_eliminar (en el repositorio remoto, importante poner los : delante del nombre de la rama)
```
