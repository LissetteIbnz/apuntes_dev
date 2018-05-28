# GIT

## Tabla de contenidos

### .gitkeep – Incluyendo carpetas en los repositorios

Cuando queremos que una carpeta vacía se clone en el repositorio, debemos agregar un fichero y comitearlo. Por convenio, se utiliza un fichero con el nombre _.gitkeep_, de esta forma, Git tendrá en cuenta nuestra carpeta.

Si además queremos que se ignoren los ficheros creados en ella, sólo debemos agregar en el fichero _.gitignore_ la carpeta tal que así:

```bash
# Ignorando los ficheros de la carpeta cache/
cache/
```

### Completar un commit mediante consola

- press "i"
- write your merge message
- press "esc"
- write ":wq"
- then press enter