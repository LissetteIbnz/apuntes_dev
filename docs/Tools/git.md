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

### STAGE & SNAPSHOT

Working with snapshots and the Git staging area.

`git status` show modified files in working directory, staged for your next commit

`git add [file]` add a file as it looks now to your next commit (stage)

`git reset [file]` unstage a file while retaining the changes in working directory

`git diff` diff of what is changed but not staged

`git diff --staged` diff of what is staged but not yet commited

`git commit -m “[descriptive message]”` commit your staged content as a new commit snapshot

### BRANCH & MERGE
Isolating work in branches, changing context, and integrating changes

`git branch` list your branches. a * will appear next to the currently active branch

`git branch [branch-name]` create a new branch at the current commit

`git checkout` switch to another branch and check it out into your working directory

`git merge [branch]` merge the specified branch’s history into the current one

`-- git log` show all commits in the current branch’s history