# Infiniteam Go style Guide
Propuesta de guía de estilo aplicaciones Go


## Sobre este documento

Este documento constituye una guía de estilo y buenas prácticas para escribir aplicaciones go.

## Objetivo

Establecer una convención de estilo que siguiendo la línea propuesta por el estándar Bsale enfuerce una forma de escribir código go que cumpla ciertos *objetivos*

## Versión

Este documento se encuentra en la versión 0.0.2 y sigue versionado semántico.


## TOC


- [Infiniteam Go style Guide](#infiniteam-go-style-guide)
  * [Sobre este documento](#sobre-este-documento)
  * [Objetivo](#objetivo)
  * [Versión](#versión)
  * [Introducción](#introducción)
  * [Principios de estilo](#principios-de-estilo)
  * [Análisis estático](#análisis-estático)
    + [Built in tools](#built-in-tools)
    + [Herramientas de la comunidad](#herramientas-de-la-comunidad)
  * [Guía](#guía)
    + [Commits](#commits)
    + [Formato](#formato)
    + [Identificadores (General)](#identificadores-general)
    + [Variables](#variables)
      - [Objetivo](#objetivo-1)
      - [Convenciones de GO  ](#convenciones-de-go)
      - [Convenciones adicionales propuestas](#convenciones-adicionales-propuestas)
    + [Comentarios](#comentarios)
    + [Complejidad Ciclomática](#complejidad-ciclomática)
    + [Linters. ](#linters)
    + [Testing](#testing)
      - [Ejecución correcta de tests antes de generar PR](#ejecución-correcta-de-tests-antes-de-generar-pr)
      - [Paquete especial para tests de integración:](#paquete-especial-para-tests-de-integración)
        * [Objetivos ](#objetivos)
        * [¿Que tipos de tests deben ir aquí?](#que-tipos-de-tests-deben-ir-aquí)
          + [Ejemplo. ](#ejemplo)
        * [Requisitos](#requisitos)
      - [Ex. Como construir un test de integración para probar conexión a base de datos.](#ex-como-construir-un-test-de-integración-para-probar-conexión-a-base-de-datos)
    + [Verificar cumplimiento de interfaces](#verificar-cumplimiento-de-interfaces)
    + [Evite almacenar estado en globales](#evite-almacenar-estado-en-globales)
    + [Casteo](#casteo)
    + [Cuidado al copiar slices y mapas](#cuidado-al-copiar-slices-y-mapas)
    + [Use defer para labores de limpieza](#use-defer-para-labores-de-limpieza)
    + [Acceder a los servicios que utiliza cada APP a través de su configer (bs_foundation v2)](#acceder-a-los-servicios-que-utiliza-cada-app-a-través-de-su-configer-bs_foundation-v2)
      - [Objetivo:](#objetivo-2)
      

## Introducción

Estilos son las convenciones que gobiernan nuestro código, mas alla del simple formato. Su alcance es inmediatamente posterior al estándar que es más abstracto, e intenta cubrir áreas que este deja a la propia discreción.

## Principios de estilo

Hay algunos principios que resumen como escribir código legible en Go. Los siguientes son atributos del código legible, en orden de importancia:

1 **Claridad**.  El proposito del código es claro y racional para el lector.  
2 **Simplicidad**. El código alcanza su meta de la forma mas simple posible.  
3 **Concision**. El código tiene poco ruido visual  
4 **Mantenible**. El código ofrece formas de ser extendido, reparado, refaccionado facilmente   
5 **Consistencia**. El código se ve, siente y comporta de manera similar a través de distintos módulos  

Estas ídeas guía están alineadas con la estrategia de innovación tecnológica de Bsale. La famosa piramide.

## Análisis estático

Go provee de excelentes herramientas para cuidar de la salud del código, existiendo además otras proporcionadas por la comunidad. Uselas exhaustivamente para garantizar la calidad de la base de código.

### Built in tools

* go fmt  Cuida del formato de nuestro código  
  - Uso. En la raìz de nuestro repositorio:  
  
```bash
$ gofmt -w ./...
```

* go vet  Examina nuestro código e informa  posibles errores o construcciones sospechosas de mal olor
  - Uso. En la raìz de nuestro repositorio:  



```bash
$ go vet ./...
```

* golint Busca errores en las convenciones de estilode nuestro código

  - Uso
 
```bash
$ golint ./...
```

### Herramientas de la comunidad

* [goreportcardcli](https://github.com/gojp/goreportcard)

  - Instalación

```bash 
git clone https://github.com/gojp/goreportcard.git
cd goreportcard
make install
go install ./cmd/goreportcard-cli
```

  - Uso. En la raíz de su repositorio:  
 
```go
goreportcard-cli
```
  
* [fieldaligment](https://pkg.go.dev/golang.org/x/tools/go/analysis/passes/fieldalignment)  
  Ordena los camps de los structs d enuestra aplicación para ahorrar memoria. Puede ver [este artículo](https://medium.com/@chess.coach.ar/el-orden-de-los-campos-de-una-struct-de-go-importa-d8a01b5b0f85) para mas detalles.
  
  - Instalación
 
```bash 
go install golang.org/x/tools/go/analysis/passes/fieldalignment/cmd/fieldalignment@latest
```

   - Uso:

```bash
$ ~/go/bin/fieldalignment -fix ./...
```

* [golangci-lint](https://golangci-lint.run/)

  - [Instalación]([https://golangci-lint.run/](https://golangci-lint.run/usage/install/#binaries))  
  - Uso:

```bash
$ golangci-lint run ./...
```  

  Puede obtener un archivo yml con una configuración aceptable para golangci-lint desde [aqui](https://gist.github.com/areyes-bsale/a3e17e235d0b8dd11212d85e8dcae9b7)
  

## Guía

### Commits

Origen: [Proposal 1](https://gist.github.com/areyes-bsale/029d3bdae2e92b52dd466748b5781e1b?permalink_comment_id=4522941#gistcomment-4522941)

*  Al hacer commits y pull request indicar con un tag de texto a que versión se dirige el cambio.

Ej:

```bash
$ git commit -m '[v2] se define el struct user en v2/maestro/model/user.go'
```

### Formato

Principios Guía: Concisión, Consistencia

Use [gofmt](https://gist.github.com/areyes-bsale/029d3bdae2e92b52dd466748b5781e1b#built-in-tools) para garantizar que el formato de su código sea idiomático.  

### Identificadores (General)

Principios Guía: Claridad

* Preferir MixedCaps o mixedCaps para identificadores compuestos de multiples palabras  
* Seguir las [convenciones idiomáticas de Go](https://go.dev/doc/effective_go#names)  
* Mas corto es mejor  


### Variables

Completa 

Origen: [Proposal 5](https://gist.github.com/areyes-bsale/029d3bdae2e92b52dd466748b5781e1b?permalink_comment_id=4525611#gistcomment-4525611)



#### Objetivo

* Se propone que, al momento de nombrar variables de distintos tipos, se utilicen ciertas reglas para hacer el código más fácil de leer y seguir en cualquier punto.

No existen reglas escritas en piedra para nombrar variables. Sin embargo, existen algunas buenas prácticas que pueden ayudar a mantener el orden mientras se genera un código fácilmente legible.

#### Convenciones de GO  

1. Utilizar "mixed case" cuando se nombren variables con más de una palabra. (Ej: variableOculta, VariableVisible)  
2. Para nombrar interfaces, utilizar `nombre del método + "er"` (Ej: Metodo "Write" -> Interface "Writer") 

3. Preferir nombres cortos pero descriptivos.  


Sin embargo, la tercera regla de los nombres cortos debe seguir un principio: "Mientras más lejos se utilice una variable desde que fue declarada, más descriptivo debe ser el nombre". Para ello, se proponen algunos lineamientos generales no-oficiales:

#### Convenciones adicionales propuestas

1. Nombres descriptivos que ayuden a entender qué función cumple la variable.

2. Preferir nombres cortos, sin perder que sean descriptivos. Se propone utilizar **máximo** 2 o 3 palabras en una variable (Ej: variableTresPalabras)

3. Utilizar nombres de una sola letra solamente cuando el alcance de dicha variable será limitado y corto. (Ej: El `ìndex` dentro de un loop con nombre `i, j, k`, o una variable que tendrá un uso único en todo el código)

4. Utilizar nombres "Shorthand" (Abreviaturas) cuando sea posible, **siempre y cuando** sean fáciles de entender para quien lea el código por primera vez. Mientras más grande sea el alcance de la variable (Scope), más descriptivo debiese ser el nombre.

Ej de Shorthands: 

```go
pid // Dependiendo del contexto, puede ser poco descriptivo (Podría referirse a personID, productID, paymentID)
err // Descriptivo (Referencia a "Error")
cfg // Descriptivo (Referencia a "Config")
```

5. Mantener los acrónimos con su formato original. Palabras como "API", "HTTP", "DB", "ID" deben permanecer con las mayúsculas / minúsculas correspondientes al nombrar una variable (Ej: connectDB, userID, testAPI) 

6. Usar "preguntas" como nombre para las variables con valores boolean (Ej: "isLogged", "hasData", "isAuthorized")


### Comentarios

Principios Guía: Claridad, Mantenibilidad

Los comentarios que se escrin sobre la declaración de un paquete, función, tipo, constante, variable, etc. son usados por la herramienta Godoc para construir la documentación del sistema.

* Use // para comentar
* Toda función, tipo, interface, struct, variable o constante merece un comentario
* Puede usar comentarios multilínea si es necesario. Use //
* En paquetes que contienen muchos archivos basta que solo uno de ellos tenga el comentario explicativo

Ejemplo :


```go
// Package utils encapsula tipos y funciones utiles para el manejo de ...
// teniendo en cuenta que ...
// siendo su ámbito de acción el ...
package utils

// Timeout indica el tiempo máximo que puede durar una operación
const Timeout = time.Duration.Second * 60 

// MyInterface representa una cosa capaz de hacer ...
type MyInterface interface {

    // Method1 devuelve un string el cual ...
    func Method1() string
}

// MyImplementation Cumple con la interface MyInterface y su objetivo es ...
type MyImplementation struct {}

// Method1 devuelve un string el cual...
func (m MyImplementation) Method1() string {
```

* Puede revisar este [artículo](https://www.digitalocean.com/community/tutorials/how-to-write-comments-in-go) para leer una argumentaci
* Puede revisar este [repositorio](https://cs.opensource.google/go/go/+/refs/tags/go1.19.2:src/sort/) para ver ejemplos de código bien documentado


### [Complejidad Ciclomática](https://es.wikipedia.org/wiki/Complejidad_ciclom%C3%A1tica)

Principios Guía: Claridad, Simplicidad, Concisión

* Nuestras funciones no deben superar a 25 lìneas efectivas. Para este límite con considerar el espacio en blanco ni los comentarios (que la guía sea que la función quepa visualmente en el viewport de la pantalla)
* Nuestras funciones deben realizar solo una labor
* Evitar if anidados
* Controlar la cantdad de condiciones que nuestras funciones controlan. Si hay mas de un if en nuestra función considerar tener una función para cada condición


### Linters. 

Principios guía: Mantenibilidad, Consistencia.

Para garantizar un piso mínimo de calidad, antes de generar un pull request ejecutemos el set completo de herramientas de análisis estático:


```bash
$ gofmt -w ./... 
$ go vet ./...
$ golint ./...
$ goreportcard-cli
$ fieldalignment -fix ./...
$ golangci-lint run ./...
```

Si alguna de ellas reporta un problema, reparemoslo antes de generar el pull request.


### Testing

#### Ejecución correcta de tests antes de generar PR

Antes de generar un pull request asegurese de que todos los test son exitosos

En la raíz del repositorio:

```bash
$ go run test ./...
```

Se implementará una github action que realice los test automáticamente, y si estos fallan el PR será rechazado.

* Para testear funciones que accedan a BD use mocks para la conexión y para los datos. Puede revisar este [artículo](https://github.com/Bsale-IO/bs_web_datalayer/discussions/67)




#### Paquete especial para tests de integración:

Origen: [Proposal 2](https://gist.github.com/areyes-bsale/029d3bdae2e92b52dd466748b5781e1b?permalink_comment_id=4524082#gistcomment-4524082)

Cada paquete que representa flujo de bsale, *master*, *reporter*, *genext*, *db*, *cfg*, *bs*; tendrá un subpaquete especial llamado *tests*, que contendrá los **test de integración**.  

##### Objetivos 

* Cumplir con que todo repositorio sea posible de clonar, instalar dependencias y correr tests exitosos sin depender de variables de infraestructura, como la conexión a base de datos

* Proveer un medio para garantizar que las operaciones a BD sean testeadas y garantizar su funcionamiento, sin que interfieran en otros tipos de tests, como los unitarios,  


##### ¿Que tipos de tests deben ir aquí?

1 Tests que deban probar el uso combinado de componentes de este paquete. repositorios, modelos e implementaciones.

2 Tests que deban probar estos componentes contra un elemento de infraestructura, como una conección de tests a base de datos.


###### Ejemplo. 

* He construido un repositorio representando el acceso a la capa de datos. Construí el modelo y la implementación en SQL y necesito probar que se accede a los datos de manera correcta.

* Punto 1 se cumple, porque necesito testear combinadamente repositorio, modelo e implementación, y posiblemente la dependencia de un repositorio en otro, como se da por ejemplo en el caso de tener que obtener detalles desde un documento.

* Punto 2 se cumple, porqué necesito probar mi código contra un elemento d einfraestructura, una conexión a base de datos.

![image](https://user-images.githubusercontent.com/80769303/229370368-e3004959-a61f-449c-9f0b-d51d867fa054.png)


##### Requisitos

* Use variables de entorno para guardar usuario, clave, host, etc. y otros elementos que permitan usar componentes de infraestructura.

* Use variables de entorno para saber si se puede ejecutar el test de integración y si está disponible el recurso de infraestructura

Ej.

Quiero probar mi código contra una base de datos de pruebas.

```go
func TestIntegrationClient(t *testing.T) {
	ctx := checkIntegratinTestENV(t)

	var conector sql.DataOpener = &sql.Connector{}
	ctx, err := conector.Open(ctx)

	if err != nil {
		t.Log(err)
		t.FailNow()
	}

	defer conector.Close(ctx)

	var clientID int64 = 1

	var cli repository.Client[int64] = implementation.Client[int64]{}
	_, err = cli.Get(ctx, clientID)

	if err != nil {
		t.Log(err)
		t.FailNow()
	}
}
```

Se provee la función `checkIntegratinTestENV` que verifica la obtención de las variables de entorno necesarias para ejecutar un test de integración contra la base de datos de tests usando `os.Getenv` para obtenerlas. Si estas variables de entorno no están presentes, se gatilla `t.Skip` con un mensaje aclaratorio para que el test no se ejecute ni sea considerado.

Ud no está obligado a usar la función `checkIntegratinTestENV`. si lo prefiere puede usar sus propias variables de entorno y chequear ud mismo su existencia, terminando el test con `Skip` en caso de ausencia.


Cuerpo de `checkIntegratinTestENV`

```go

func getTestContext(usr string, pass string, host string, db string) context.Context {
	dsn := fmt.Sprintf(
		"%s:%s@tcp(%s:%v)/%s?parseTime=True",
		usr,
		pass,
		host,
		3306,
		db,
	)
	c := context.Background()
	c = context.WithValue(c, sql.KeyDsnID, dsn)
	return c
}

func checkIntegratinTestENV(t testing.TB) context.Context {
	testUsr := os.Getenv("TEST_DB_USR")
	testPass := os.Getenv("TEST_DB_PASS")
	testHost := os.Getenv("TEST_DB_HOST")
	testDB := os.Getenv("TEST_DB_DB")

	if testUsr == "" {
		t.Skip("set the env var TEST_DB_USR with the user of the test database to run this package integration tests")
	}

	if testPass == "" {
		t.Skip("set the env var TEST_DB_PASS with the test password of the test db to run this package integration tests")
	}

	if testHost == "" {
		t.Skip("set the env var TEST_DB_HOST with the host of the test database to run this package integration tests")
	}

	if testDB == "" {
		t.Skip("set the env var TEST_DB_DB with the test db to run this package integration tests")
	}

	return getTestContext(testUsr, testPass, testHost, testDB)
}

```




#### Ex. Como construir un test de integración para probar conexión a base de datos.

Origen: [Proposal 3](https://gist.github.com/areyes-bsale/029d3bdae2e92b52dd466748b5781e1b?permalink_comment_id=4524304#gistcomment-4524304)


Caso: Testear método del repositorio que obtiene un cliente:

```go
func TestIntegrationGetCart(t *testing.T) {

        // Obtener contexto para pruebas según [proposal 2]()
	ctx := checkIntegratinTestENV(t)

        // Abrir conexión usando los medios del repositorio
	var conector bssql.DataOpener = &bssql.Connector{}
	ctx, err := conector.Open(ctx)

	if err != nil {
		t.Log(err)
		t.FailNow()
	}

	defer conector.Close(ctx)

        // Parametrizar los valores atestear, en este caso el id del cliente  a obtener
	var cartID int64 = 1

        // instanciar el repositorio a probar
	var cli repository.Cart[int64] = implementation.Cart[int64]{}
	
	// Testear la función descartando el resultado
	_, err = cli.Get(ctx, cartID)

        // Comprobar error
	if err != nil {
                // En caso de que error sea que no se encontró el cliente, Skip al test 
                // con un mensaje adecuado para qeu el mantenedor del test pueda corregirlo
		if err == sql.ErrNoRows {
			t.Skipf("No se encontró cart con ID %d para testear.\n", cartID)
		}

		t.Log(err)
		t.FailNow()
	}
}

```


### Verificar cumplimiento de [interfaces](https://go.dev/doc/effective_go#interfaces)

Verificar cumplimiento d einterfaces en tiempo de compilación.

Ejemplo: Tengo una interface en mi repositorio. el repositorio cliente

```go
// Client encapsulates access to client entitites
type Client[T types.Identificable] interface {
	GET(ctx context.Context, key T) (*model.Client, error)
	GETFromDocument(ctx context.Context, doc *serializemodel.SzDocument) (*model.Client, error)
	GETDynamics(ctx context.Context, clientID T) (map[string]interface{}, error)
}
```

Posteriormente, cuando escriba mi implementación debo verificar que cumple con implementar los métodos de la interface con el siguiente código:

```go 

var (
	_ repository.Client[int64] = Client[int64]{} // Esto generara un error de compilación o un aviso de error en el editor de texto si el struct no implementa correctamente la interface
)

type Client[T int64] struct{}
```



### Evite almacenar estado en globales

Evite variables globales mutables. Prefiera inyección de dependencias.

@todo poner ejemplo


### Casteo

Para convertir interfaces en el tipo de dato subyacente, siempre valide el resultado de la operación.

Ej:

```go
type A interface {
  Method1()
}

type B struct {}

func(b B) Method1() {}

var c A := B{}

// mal

b := c.(B)

// bien

b, ok := c.(B)

if !ok {
   // manejar el error de casteo
}


```


### Cuidado al copiar slices y mapas

Slices y mapas se basan en punteros. Una función cliente de su código puede modificar si almacena una referencia a ellos como argumento:


¡Cuidado!

```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = trips
}

trips := ...
d1.SetTrips(trips)

// Ojo! modificaras d1.trips
trips[0] = ...      
```   
 
 Versión sin mutaciones
 
```go
func (d *Driver) SetTrips(trips []Trip) {
  d.trips = make([]Trip, len(trips))
  copy(d.trips, trips)
}

trips := ...
d1.SetTrips(trips)

// podemos modificar trips[0] sin afectar d1.trips.
trips[0] = ...     
```         

### Use defer para labores de limpieza

Use defer para garantizar el cierre y liberación de recursos

```go
rows, err := executer.QueryContext(ct, q, p...)
if err != nil {
		return nil, err
}

defer rows.Close() // defer garantiza que al terminar la función, sea como sea que termine, lo deferido se ejecutará

var rels []*transport.RationalizedMovementAmount

```



### Acceder a los servicios que utiliza cada APP a través de su configer (bs_foundation v2)

Origen: [Proposal 4](https://gist.github.com/areyes-bsale/029d3bdae2e92b52dd466748b5781e1b?permalink_comment_id=4525206#gistcomment-4525206)  

#### Objetivo:

 -  Poder acceder a los servicios que utiliza la aplicación dependiendo del entorno.

De este modo, solo sería necesario agregar los servicios que utilizaría cada aplicación en su archivo de configuración yml. El nodo sería definido como `services`

![imagen](https://user-images.githubusercontent.com/116005249/229566182-070251d3-c62f-4020-aff5-9b20f8aaffa2.png)

Así ya quedaría en el struct `APPConfig` en el campo `ServiceMap` a través del cual ya podríamos obtener los servicios

![imagen](https://user-images.githubusercontent.com/116005249/229567364-41dbfd9b-551d-4c44-a264-9c59630012f9.png)

Utilizando los métodos `Service` y `Services`

![imagen](https://user-images.githubusercontent.com/116005249/229567681-bd7bbd66-cb02-4eac-9d0d-f87af80082db.png)

Más detalles sobre el configer v2 aquí:
[Configer Bs Foundation V2](https://github.com/Bsale-IO/bs_foundation/blob/master/docs/v2/configer.md)


