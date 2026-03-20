#  Control de Gastos Personales LAB 1 C2

##  Situación Problemática

### Enunciado
Muchas personas en El Salvador, especialmente jóvenes universitarios y familias de
clase media, no llevan un registro ordenado de sus gastos diarios. Esto provoca que
al final del mes no sepan en qué gastaron su dinero, excedan su presupuesto sin
darse cuenta y no puedan ahorrar. La falta de una herramienta sencilla y accesible
para registrar gastos desde el navegador agrava este problema.

### Sector al que va dirigido
-  Estudiantes universitarios que manejan un presupuesto mensual limitado
-  Familias que desean controlar sus finanzas del hogar
-  Personas independientes o freelancers que necesitan llevar control de sus egresos

### ¿Cómo lo resuelve el programa?
La aplicación web permite al usuario registrar cada gasto indicando una descripción,
el monto y la categoría a la que pertenece (alimentación, transporte, salud, etc.).
El sistema muestra en tiempo real el total gastado, lo compara con un presupuesto
mensual establecido y alerta al usuario si lo ha superado. También permite eliminar
gastos registrados por error. Todo esto sin necesidad de crear una cuenta ni instalar
nada, directamente desde el navegador.

---

##  Funciones principales del programa

| Función | Descripción |
|---|---|
| `agregarGasto()` | Valida y agrega un nuevo gasto a la lista |
| `eliminarGasto(index)` | Elimina un gasto específico por su posición en la lista |
| `validar()` | Verifica que los campos no estén vacíos y que los datos sean correctos |
| `totalGastado` (computed) | Calcula automáticamente la suma total de todos los gastos |

---

##  Tecnologías utilizadas

- [Vue.js 3](https://vuejs.org/)
- [Vite](https://vitejs.dev/)
- HTML5, CSS3

---

##  Preguntas del Laboratorio

---

### 1. ¿Qué es Vue.js y cuál es su función dentro de la página web desarrollada?

Vue.js es un framework progresivo de JavaScript que se utiliza para construir
interfaces de usuario de forma reactiva y organizada. A diferencia de manipular
el HTML directamente con JavaScript puro, Vue permite declarar cómo debe verse
la interfaz según el estado de los datos, y él se encarga de actualizar la pantalla
automáticamente cuando esos datos cambian.

En este proyecto, Vue cumple la función de:
- Mostrar y actualizar en tiempo real la lista de gastos cada vez que se agrega
  o elimina uno
- Vincular los campos del formulario con las variables del sistema mediante v-model
- Mostrar u ocultar secciones de la página según si hay gastos registrados o no
- Recorrer la lista de gastos para mostrarlos en la tabla automáticamente

Sin Vue, cada vez que el usuario agregara un gasto habría que actualizar el DOM
manualmente, lo cual haría el código mucho más largo y propenso a errores.

---

### 2. ¿Qué variables reactivas utilizó y cuál es la función de cada una?

En Vue.js, una variable reactiva es aquella que está declarada dentro de `data()`
y que, al cambiar su valor, provoca automáticamente una actualización en la
interfaz sin necesidad de recargar la página.

Las variables reactivas utilizadas en este proyecto son:

| Variable | Tipo | Función |
|---|---|---|
| `descripcion` | String | Almacena el texto ingresado en el campo de descripción del gasto |
| `monto` | String/Number | Guarda el valor numérico del monto ingresado por el usuario |
| `categoria` | String | Guarda la categoría seleccionada en el menú desplegable |
| `presupuesto` | Number | Define el límite de gasto mensual (fijado en $500) |
| `gastos` | Array | Contiene la lista completa de todos los gastos registrados |
| `errores` | Object | Almacena los mensajes de error de validación para cada campo |
| `categorias` | Array | Lista fija de categorías disponibles para mostrar en el select |

---

### 3. ¿Cuál es la diferencia entre v-bind y v-model?

Aunque ambas directivas conectan datos de Vue con elementos HTML, tienen
propósitos distintos:

**v-bind (`:`):**
- Es una vinculación **en una sola dirección**: de Vue hacia el HTML.
- Se usa para asignar dinámicamente atributos HTML como `class`, `href`,
  `src`, `disabled`, etc.
- El HTML se actualiza cuando cambia la variable en Vue, pero si el usuario
  interactúa con el elemento, Vue no se entera.

Ejemplo usado en el proyecto:
```html
:class="{ 'input-error': errores.descripcion }"
```
Esto aplica la clase CSS `input-error` al input únicamente cuando la variable
`errores.descripcion` tiene un valor, es decir, cuando hay un error de validación.

**v-model:**
- Es una vinculación **en dos direcciones**: de Vue hacia el HTML Y del HTML
  hacia Vue al mismo tiempo.
- Se usa principalmente en elementos de formulario como `input`, `select` y
  `textarea`.
- Cuando el usuario escribe o selecciona algo, la variable en Vue se actualiza
  automáticamente, y viceversa.

Ejemplo usado en el proyecto:
```html
v-model="descripcion"
```
Cada letra que el usuario escribe en el campo actualiza instantáneamente la
variable `descripcion` en Vue.

**Resumen:**
| | v-bind | v-model |
|---|---|---|
| Dirección | Vue → HTML | Vue ↔ HTML |
| Uso típico | Atributos HTML dinámicos | Campos de formulario |
| Actualiza Vue |  No |  Sí |

---

### 4. Mencione al menos un ejemplo de evento utilizado en la aplicación

En Vue.js, los eventos se manejan con la directiva `@` (que es el atajo de
`v-on:`). Permiten ejecutar funciones cuando el usuario realiza una acción
como hacer clic, escribir, etc.

**Ejemplo 1 — Clic en el botón de agregar:**
```html
<button @click="agregarGasto"> Agregar Gasto</button>
```
Cuando el usuario hace clic en este botón, Vue ejecuta el método `agregarGasto()`,
que primero valida los datos del formulario y, si todo es correcto, agrega el
nuevo gasto al arreglo `gastos[]`.

**Ejemplo 2 — Clic en el botón de eliminar:**
```html
<button @click="eliminarGasto(index)"> Eliminar</button>
```
Este evento se encuentra dentro del `v-for` que recorre la lista de gastos.
Al hacer clic, llama al método `eliminarGasto()` pasándole el índice del gasto
correspondiente para eliminarlo del arreglo.

---

### 5. ¿Para qué se utilizó la directiva v-for en la aplicación?

`v-for` es una directiva de Vue que permite recorrer un arreglo o lista y
renderizar un elemento HTML por cada ítem encontrado, de forma automática.

En este proyecto se utilizó en dos lugares:

**Lugar 1 — Para generar las opciones del select de categorías:**
```html
<option v-for="cat in categorias" :key="cat" :value="cat">{{ cat }}</option>
```
Esto recorre el arreglo `categorias` y crea automáticamente un `<option>` por
cada categoría definida (Alimentación, Transporte, Salud, etc.), evitando
escribir cada opción manualmente en el HTML.

**Lugar 2 — Para mostrar la tabla de gastos registrados:**
```html
<tr v-for="(gasto, index) in gastos" :key="index">
```
Cada vez que el usuario agrega un gasto al arreglo `gastos[]`, Vue recorre
automáticamente ese arreglo y genera una fila `<tr>` por cada elemento,
mostrando su descripción, categoría y monto.

La propiedad `:key` es importante porque le da a Vue un identificador único
para cada elemento, lo que mejora el rendimiento al actualizar la lista.

---

### 6. ¿En qué situación se utilizó v-if y qué problema resuelve?

`v-if` es una directiva que renderiza o elimina un elemento del DOM según si
una condición es verdadera o falsa. Si la condición es falsa, el elemento
directamente no existe en el HTML.

En este proyecto se utilizó en los siguientes casos:

**Caso 1 — Mostrar mensajes de error de validación:**
```html
<span v-if="errores.descripcion" class="error">{{ errores.descripcion }}</span>
```
El mensaje de error solo aparece si la variable `errores.descripcion` tiene
contenido. Si el campo es válido, el mensaje desaparece completamente.

**Caso 2 — Mostrar el resumen y la tabla solo si hay gastos:**
```html
<section class="resumen" v-if="gastos.length > 0">
<section class="lista"   v-if="gastos.length > 0">
```
Esto evita mostrar una tabla vacía cuando el usuario aún no ha registrado
ningún gasto, lo cual mejoraría la experiencia visual.

**Caso 3 — Mostrar mensaje vacío cuando no hay gastos:**
```html
<section v-if="gastos.length === 0" class="vacio">
```
Muestra un mensaje amigable indicando que aún no hay gastos, solo cuando
la lista está vacía.

**Caso 4 — Alerta de presupuesto excedido:**
```html
<p v-if="totalGastado > presupuesto" class="alerta"> ¡Has superado tu presupuesto!</p>
<p v-else class="bien"> Estás dentro del presupuesto.</p>
```
Muestra una advertencia en rojo si el total supera el presupuesto, o un
mensaje positivo en verde si aún hay saldo disponible.

---

### 7. ¿Cómo se realiza la validación de datos y por qué es importante?

**¿Cómo se valida?**

La validación se realiza en el método `validar()` que se ejecuta antes de
agregar cualquier gasto. Este método revisa tres condiciones:
```javascript
validar() {
  let valido = true
  this.errores = { descripcion: '', monto: '', categoria: '' }

  // Validación de descripción
  if (!this.descripcion.trim()) {
    this.errores.descripcion = 'La descripción es obligatoria.'
    valido = false
  } else if (this.descripcion.trim().length < 3) {
    this.errores.descripcion = 'La descripción debe tener al menos 3 caracteres.'
    valido = false
  }

  // Validación de monto
  if (!this.monto) {
    this.errores.monto = 'El monto es obligatorio.'
    valido = false
  } else if (isNaN(this.monto) || parseFloat(this.monto) <= 0) {
    this.errores.monto = 'Ingresa un monto válido mayor a 0.'
    valido = false
  }

  // Validación de categoría
  if (!this.categoria) {
    this.errores.categoria = 'Debes seleccionar una categoría.'
    valido = false
  }

  return valido
}
```

Si alguna validación falla, se guarda el mensaje de error en la variable
reactiva `errores`, que es mostrado en pantalla gracias a `v-if`. Solo si
todas las validaciones pasan (`valido = true`), se ejecuta `agregarGasto()`.

**¿Por qué es importante validar?**

Validar la información ingresada por el usuario es fundamental por las
siguientes razones:

1. **Integridad de los datos:** Evita que se registren gastos con descripción
   vacía o montos de $0, lo cual no tendría sentido en el sistema.
2. **Experiencia de usuario:** El usuario recibe retroalimentación inmediata
   sobre qué campo está incorrecto, sin que la página se recargue.
3. **Prevención de errores:** Un monto negativo o texto donde se espera un
   número podría romper los cálculos del total y el presupuesto.
4. **Confiabilidad del sistema:** Un sistema que acepta cualquier dato sin
   validar no es confiable ni profesional.

---

## 👥 Autores

- Nombre del estudiante 1
- Nombre del estudiante 2

**Universidad Gerardo Barrios — Programación Computacional IV**  
Laboratorio 1 — Segundo Cómputo — Semana 9
