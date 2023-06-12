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

- [**W1: Run Unitary and Integration tests**]()
  - Test unitarios y REST (feature/add-split-lines)

- [**W2: Develop workflow**]()
  - Test unitarios, REST y Selenium sobre la rama develop (que incluye los nuevos cambios)
  - Se genera un artefacto Docker de desarrollo con dos tags en dos repositorios
    - DockerHub: [books-reviewer:0.2.0-SNAPSHOT-dev]() y [books-reviewer:dev]()
    - GitHub Packages: [books-reviewer:0.2.0-SNAPSHOT-dev]() y [books-reviewer:dev]()

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

- [**W4: Run Unitary and Integration tests**]()
  - Test unitarios y REST (release/0.2.0)
  - Se genera un artefacto Docker de release candidate con dos tags en dos repositorios
    - DockerHub: [books-reviewer:0.2.0.RC1]() y [books-reviewer:rc]()
    - GitHub Packages: [books-reviewer:0.2.0.RC1]() y [books-reviewer:rc]()

Una vez consideramos que la release está lista, creamos un nuevo pull request a master.

En este caso, el pull request no genera la ejecución de ningún workflow.

Al mergear, se desencadena el workflow de master:

- [**W5: Run Unitary and Integration tests**]()
  - Se descarga la última release candidate (books-reviewer:rc), se realiza un re-tag a versión definitiva (0.2.0) y se publica en dos repositorios:
    - DockerHub: [books-reviewer:0.2.0]()
    - GitHub Packages: [books-reviewer:0.2.0]()

## Job de Nightly

Todos los días a las 2:00am se ejecuta el job de Nightly 

- [**W3: Nightly**]()
  - Test unitarios, REST y Selenium sobre la rama develop
  - Se genera un artefacto Docker de nightly con dos tags en dos repositorios
    - DockerHub: [books-reviewer:20220620.162029-nightly]() y [books-reviewer:nightly]()
    - GitHub Packages: [books-reviewer:20220620.162029-nightly]() y [books-reviewer:nightly]()
