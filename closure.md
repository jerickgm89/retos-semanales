## PREGUNTA 1: Closures levanta tu mano

Considere las siguientes funciones `clickHandler,` `inmediate` y `delayedReload`  Cual de las tres funciones tiene acceso a una variable global?

```jsx
let counClicks = 0;
button.addEventListener('clic', function clickHandler(){
	countClicks++;
});
```

```jsx
const result = (function inmediate(number){
	const message = `number is: $(number)`;
	return message;
})
```

```jsx
setTimeout(function delayedReload(){
	location.reload();
}), 1000);
```

- **Respuesta**
    1. `clickHandler` tiene acceso a la variable global `countClicks`
    2. `inmediate` no tiene acceso a ninguna variable gobgloballal
    3. `delayedReload` accede a la variable global `location` del scope global
    

## Pregunta 2: Parámetros perdidos

Cual es el resultado en la consola, del siguiente codigo

```jsx
(function inmediateA(a){
	return (function inmediateB(b){
		console.log(a); //Que muestra?
	)(1);
)(0);
```

- Respuesta
    
    La consola mostrara 0
    

## Pregunta 3: Quien es quien

Que mostrara la consola al ejecutar el siguiente codigo

```jsx
let count = 0;
(function immediate() {
  if (count === 0) {
    let count = 1;
    console.log(count); // Que salida es?
  }
  console.log(count); // Que salida es?
})();
```

- **Respuesta**
    
    `1` y `0` se muestra en la consola, la primera instruccion`let count = 0` declara una variable `count`. `inmediate()` es un closure este captura la variable `count` desde el otro scope. Dentro de la funcion inmediate() `count` es 0. Sin embargo, dentro de la condicional, otro `let count = 1` esta declarada en local, esta sobreescribe a `count` del scope global. El primer resultado del `console.log(count)` tiene como resultado `1`.
    
    El segundo `console.log(count)` tiene como resultado `0`, ya que este accede a la variable global `count`
    

## Pregunta 4: Closure confuso

Que datos saldrá en la consola el siguiente codigo:

```jsx
for (var i = 0; i < 3; i++) {
  setTimeout(function log() {
    console.log(i); // Que muestra en consola?
  }, 1000);
}
```

- **Respuesta**
    
    En la consola saldra `3`, `3`, `3`
    
    Vamos a explicarlo en 2 pasos:
    
    **Paso 1:**
    
    - En primera fase `for()` itera en 3 tiempos. durante cada iteracion crea una nueva funcion `log()`, el cual captura la variable y lo programara para su ejecución luego de 1000ms
    - Cuando `for()` completa el ciclo, la variable `i` tiene como valor 3
    
    **Paso 2:**
    
    La segunda fase ocurre despues de 1000ms
    
    - `setTimeout()` ejecuta la programación de la función `log()` el cual lee el valor de la variable `i`, el cual es 3 y los manda a la consola `3`.

## Pregunta 5: Mensaje correcto o Incorrecto

Que mostrara en la consola la ejecución del siguiente código

```jsx
function createIncrement() {
  let count = 0;
  function increment() { 
    count++;
  }

  let message = `Count is ${count}`;
  function log() {
    console.log(message);
  }
  
  return [increment, log];
}

const [increment, log] = createIncrement();
increment(); 
increment(); 
increment(); 
log(); // Que muestra?
```

- **Respuesta**
    
    La `Count = 0`, eso muestra en consola
    
    La función `increment()` es invocadaa 3 veces efectivamente incrementa `count` en un valor de 3
    
    La variable `message` existe dentro del ámbito de la función `createIncrement()` El valor inicial de `Count = 0`.  Sin embargo, aun si la variable `count` es incrementada  3 veces, la variable `message` mantiene el valor de `0`
    
    La función `log()` es un closure que captura `message` del scope de `createIncrement()`, `console.log(message)` muestra `count = 0` en la consola.
    
    **Desafío**: Como podrias modificar la funcion `log()` para que la variable message retorne el valor actual de `count`? Dame un feeback de la solucion.
    

## Restaurar la encapsulación

La siguiente función `createStack()` crea instancias de estructuras de datos de pilas (stack)

```jsx
function createStack() {
  return {
    items: [],
    push(item) {
      this.items.push(item);
    },
    pop() {
      return this.items.pop();
    }
  };
}

const stack = createStack();
stack.push(10);
stack.push(5);
stack.pop(); // => 5

stack.items; // => [10]
stack.items = [10, 100, 1000]; // Encapsulamiento roto!
```

La pila funciona como se esperaba, pero con un pequeño problema. Cualquiera puede modificar directamente el array porque la propiedad `stack.items` esta expuesta.

Esto es un problema ya que rompe el encapsulamiento de la pila: solo los metodos `push() pop()` deben ser publicos pero fuera de esto no debe ser accesible modificar el array mediante `stack.items`

Refactorize la implementación de la pila, utilizando el concepto de closure, de modo que no haya forma de acceder al array fuera del ámbito de la función `createStack()`

```jsx
function createStack() {
  // Write your code here...
}

const stack = createStack();
stack.push(10);
stack.push(5);
stack.pop(); // => 5

stack.items; // => undefined
```

- **Respuesta**
    
    Esto es una posible solucion de refactorizacion
    
    ```jsx
    function createStack() {
      const items = [];
      return {
        push(item) {
          items.push(item);
        },
        pop() {
          return items.pop();
        }
      };
    }
    
    const stack = createStack();
    stack.push(10);
    stack.push(5);
    stack.pop(); // => 5
    
    stack.items; // => undefined
    ```
    
    la variable `items` fue movido dentro del scope de la funcion `createStack()`.
    
    Gracias a este cambio, deja fuera de alcance a la variable `items` y no se puede modificar el array porque ahora la variable es privada, y la pila esta encapsulada y solo los métodos `push()` y `pop()` son públicos.
    

## Pregunta 7: Multiplicación inteligente

Escribe una función `multiply()` que multiplique por 2

```jsx
function multiply(num1, num2) {
  // Escribe tu código aqui...
}
```

A esta función se le pasa 2 parámetros `num1` y `num2`, y este debería retornar su multiplicación

Pero si se pasa 1 argumento, este debe devolver otra función. La función devuelta realiza esta multiplicación por el parámetro establecido ejm:

`const otraFuncion = multiply(num1)`

`otraFuncion(num2) → num1 * num2`

```jsx
multiply(4, 5); // => 20
multiply(3, 3); // => 9

const double = multiply(2);
double(5);  // => 10
double(11); // => 22
```

- **Respuesta**
    
    Este es una posible solución
    
    ```jsx
    function multiply(number1, number2) {
      if (number2 !== undefined) {
        return number1 * number2;
      }
      return function doMultiply(number2) {
        return number1 * number2;
      };
    }
    
    multiply(4, 5); // => 20
    multiply(3, 3); // => 9
    
    const double = multiply(2);
    double(5);  // => 10
    double(11); // => 22
    ```