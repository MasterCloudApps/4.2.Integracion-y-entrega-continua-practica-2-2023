# Practica 2 - Integración y Entrega Continua

Autor(es): Michel Maes Bermejo y Francisco Gortázar Bellas

[Repositorio](https://github.com/MasterCloudApps/4.2.Integracion-y-entrega-continua-practica-2-2023)

## Desarrollo con GitFlow

Una vez creados los workflows y funcionando estos, pasamos a crear la nueva funcionalidad:

```
$ git flow init -d
```

Subimos develop (si no estaba creado antes) -> Esto puede desencadenar un Workflow

```
$ git push --set-upstream origin develop
```

Creamos la nueva rama feature

```
$ git flow feature start add-split-lines
```

Realizamos la feature

- En el pom.xml, poner 0.2.0-SNAPSHOT
- Implementar la funcionalidad -> Copy/Paste

Subimos los cambios

```
$ git add pom.xml
$ git commit -m "Change version to 0.2.0-SNAPSHOT"
$ git add src/test/java/es/urjc/code/daw/library/unitary/LineBreakerUnitaryTest.java
$ git commit -m "Add LineBreaker tests"
$ git add src/main/java/es/urjc/code/daw/library/book/LineBreaker.java
$ git commit -m "Add LineBreaker feature"
$ git add src/main/java/es/urjc/code/daw/library/book/BookService.java
$ git commit -m "Add LineBreaker to BookService"
$ git push --set-upstream origin feature/add-split-lines
```

Esto crea una pull-request (OJO! por defecto intenta mergear a master)

Tras crear el pull request y mergear, se han ejecutado los siguientes workflows:

- [**W1: Run Unitary and Integration tests**](https://github.com/MasterCloudApps/4.2.Integracion-y-entrega-continua-practica-2-2023/actions/runs/5247281211/jobs/9477225128?pr=1)
  - Test unitarios y REST (feature/add-split-lines)

- [**W2: Develop workflow**](https://github.com/MasterCloudApps/4.2.Integracion-y-entrega-continua-practica-2-2023/actions/runs/5247296295)
  - Test unitarios, REST y Selenium sobre la rama develop (que incluye los nuevos cambios)
  - Se genera un artefacto Docker de desarrollo con dos tags en dos repositorios
    - DockerHub: [books-reviewer:0.2.0-SNAPSHOT-dev](https://hub.docker.com/layers/maes95/books-reviewer/0.2.0-SNAPSHOT-dev/images/sha256-336f94724e7b28021924282536c73799bdcfbe567c6e8e58257497099552b0db) y [books-reviewer:dev](https://hub.docker.com/layers/maes95/books-reviewer/dev/images/sha256-336f94724e7b28021924282536c73799bdcfbe567c6e8e58257497099552b0db)
    - GitHub Packages: [books-reviewer:0.2.0-SNAPSHOT-dev](https://github.com/MasterCloudApps/4.2.Integracion-y-entrega-continua-practica-2-2023/pkgs/container/books-reviewer/100878570?tag=0.2.0-SNAPSHOT-dev) y [books-reviewer:dev](https://github.com/MasterCloudApps/4.2.Integracion-y-entrega-continua-practica-2-2023/pkgs/container/books-reviewer/100878570?tag=dev)

En local, nos bajamos los cambios:

```
$ git checkout develop && git pull
```

Iniciamos la release

```
$ git flow release start 0.2.0
```

Cambiamos "-SNAPSHOT" por ".RC1" en el pom.xml

Subimos los cambios

```
$ git add pom.xml
$ git commit -m "Preparing release candidate: Set version to 0.2.0.RC1"
$ git push -u origin release/0.2.0
```

Se ejecutará el siguiente workflow:

- [**W4: Run Unitary and Integration tests**](https://github.com/MasterCloudApps/4.2.Integracion-y-entrega-continua-practica-2-2023/actions/runs/5247335812)
  - Test unitarios y REST (release/0.2.0)
  - Se genera un artefacto Docker de release candidate con dos tags en dos repositorios
    - DockerHub: [books-reviewer:0.2.0.RC1](https://hub.docker.com/layers/maes95/books-reviewer/0.2.0.RC1/images/sha256-78d404fa8d167adf326bf4079ce5ec3f5503a8e0c9dd4c62ac6b31c6e1c714e8) y [books-reviewer:rc](https://hub.docker.com/layers/maes95/books-reviewer/rc/images/sha256-78d404fa8d167adf326bf4079ce5ec3f5503a8e0c9dd4c62ac6b31c6e1c714e8)
    - GitHub Packages: [books-reviewer:0.2.0.RC1](https://github.com/MasterCloudApps/4.2.Integracion-y-entrega-continua-practica-2-2023/pkgs/container/books-reviewer/100879717?tag=0.2.0.RC1) y [books-reviewer:rc](https://github.com/MasterCloudApps/4.2.Integracion-y-entrega-continua-practica-2-2023/pkgs/container/books-reviewer/100879717?tag=rc)

Una vez consideramos que la release está lista, creamos un nuevo pull request a master.

En este caso, el pull request no genera la ejecución de ningún workflow.

Al mergear, se desencadena el workflow de master:

- [**W5: Run Unitary and Integration tests**](https://github.com/MasterCloudApps/4.2.Integracion-y-entrega-continua-practica-2-2023/actions/runs/5247382788)
  - Se descarga la última release candidate (books-reviewer:rc), se realiza un re-tag a versión definitiva (0.2.0) y se publica en dos repositorios:
    - DockerHub: [books-reviewer:0.2.0](https://hub.docker.com/layers/maes95/books-reviewer/0.2.0/images/sha256-78d404fa8d167adf326bf4079ce5ec3f5503a8e0c9dd4c62ac6b31c6e1c714e8)
    - GitHub Packages: [books-reviewer:0.2.0](https://github.com/MasterCloudApps/4.2.Integracion-y-entrega-continua-practica-2-2023/pkgs/container/books-reviewer/100879717?tag=0.2.0)

## Job de Nightly

Todos los días a las 2:00am se ejecuta el job de Nightly 

- [**W3: Nightly**]()
  - Test unitarios, REST y Selenium sobre la rama develop
  - Se genera un artefacto Docker de nightly con dos tags en dos repositorios
    - DockerHub: [books-reviewer:20220620.162029-nightly]() y [books-reviewer:nightly]()
    - GitHub Packages: [books-reviewer:20220620.162029-nightly]() y [books-reviewer:nightly]()
