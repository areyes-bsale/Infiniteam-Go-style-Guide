# Infiniteam Go style Guide
Propuesta de guía de estilo aplicaciones Go


## Sobre este documento

Este documento constituye una guía de estilo y buenas prácticas para escribir aplicaciones go.

## Objetivo

Establecer una convención de estilo que siguiendo la línea propuesta por el estándar Bsale enfuerce una forma de escribir código go que cumpla ciertos *objetivos*

## Versión

Este documento se encuentra en la versión 0.0.1 y sigue versionado semántico.

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

### Formato

Principios Guía: Concisión, Consistencia

Use [gofmt](https://gist.github.com/areyes-bsale/029d3bdae2e92b52dd466748b5781e1b#built-in-tools) para garantizar que el formato de su código sea idiomático.  

### Identificadores

Principios Guía: Claridad

* Preferir MixedCaps o mixedCaps para identificadores compuestos de multiples palabras  
* Seguir las [convenciones idiomáticas de Go](https://go.dev/doc/effective_go#names)  
* Mas corto es mejor  


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

* Antes de generar un pull request asegurese de que todos los test son exitosos

En la raíz del repositorio:

```bash
$ go run test ./...
```

Se implementará una github action que realice los test automáticamente, y si estos fallan el PR será rechazado.

* Para testear funciones que accedan a BD use mocks para la conexión y para los datos. Puede revisar este [artículo](https://github.com/Bsale-IO/bs_web_datalayer/discussions/67)


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

