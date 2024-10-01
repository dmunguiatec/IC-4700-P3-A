IC-4700 Lenguajes de programación  
Prof. Diego Munguia Molina  
IC-AL
---
# Proyecto 3

## Objetivos de aprendizaje

1. Modelar el funcionamiento interno de los principios de programación funcional en un lenguaje de programación (III).

## Descripción

Realizamos una implementación en Kotlin de un intérprete para un subconjunto del lenguaje de programación funcional 
Racket que llamaremos `byorkt`.

## Requerimientos funcionales

Nuestro intérprete debe funcionar como un REPL donde la persona usuaria ingresa S-expressions y el 
intérprete las evalúa para producir un valor que es impreso en pantalla como resultado.

```racket
> 1
1
> (quote a)
'a
> (* 2 3)
6
>
```

El REPL leerá entrada de `stdin` hasta que los paréntesis estén balanceados.

```racket
> (define inc
    (lambda (n)
        (+ n 1)
        ))
>
```

Consideremos el conteo de paréntesis en el ejemplo anterior:

* línea 1 `(define inc` `(`: 1 `)`: 0  
* línea 2 `(lambda (n)` `(`: 3 `)`: 1  
* línea 3 `(+ n 1)` `(`: 4 `)`: 2  
* línea 4 `))` `(`: 4 `)`: 4

Cuando el conteo acumulativo de `(` es igual al conteo de `)` se acepta la entrada para procesar.

En el siguiente caso se acepta de inmediato porque `(`: 0 y `)`: 0
```racket
> 1
1
```

Especificamos a continuación los elementos del lenguaje que vamos a implementar.

### Valores

Números enteros

```racket
> 1
1
> -3
-3
```

Valores de verdad

```racket
> #t
#t
> #f
#f
```

Símbolos

```racket
> (quote a)
'a
> (quote hola)
'hola
```

Null

```racket
> (quote ())
'()
```

### Primitivas

`quote` para construir Q-expressions.

```racket
> (quote a)
'a
> (quote (1 2 3))
'(1 2 3)
> (quote (+ (* 2 3) 1))
'(+ (* 2 3) 1)
```

`lambda` para construir funciones.

```racket
> (lambda (x) x)
#<procedure>
> ((lambda (x y) (+ x y)) 1 2)
3
```

`define` permite hacer declaraciones para asociar nombres con valores en un alcance global.

```racket
> (define flag #t)
> flag
#t
> (define inc (lambda (n) (+ n 1)))
> inc
#<procedure:inc>
> (inc 2)
3
```

`eval` permite evaluar un Q-expression como un S-expression.

```racket
> (eval (quote (+ 1 2)))
3
```

`apply` permite aplicar una función a una lista de argumentos.

```racket
> (apply + (quote (1 2)))
3
```

`cons` permite construir pares.

```racket
> (cons 1 2)
'(1 . 2)
> (cons 1 '())
'(1)
> (cons 1 (cons 2 '()))
'(1 2)
```

`car` para acceder al primer elemento de un par.

```racket
> (car (cons 1 2))
1
```

`cdr` para acceder al segundo elemento de un par.

```racket
> (cdr (cons 1 2))
2
> (cdr (quote (1 2 3)))
'(2 3)
```

`cond` para hacer evaluaciones condicionales.

```racket
> (cond 
    ((equal? 1 2) (quote caso-1)) 
    ((equal? 2 2) (quote caso-2))
    (else (quote caso-3)))
'caso-2
> (cond 
    ((equal? 1 2) (quote caso-1)) 
    ((equal? 3 2) (quote caso-2))
    (else (quote caso-3)))
'caso-3
```

### Funciones incorporadas

`equal?` para determinar si dos objetos son iguales.

```racket
> (equal? 1 1)
#t
> (equal? 1 2)
#f
> (equal? (quote a) (quote a))
#t
> (equal? (quote a) (quote b))
#f
> (equal? (cons 1 2) (cons 1 2))
#t
> (equal? (cons 1 2) (cons 1 3))
#f
> (equal? (quote (1 2 3)) (quote (1 2 3)))
#t
> (equal? (quote (1 2 3)) (quote (2 2 3)))
#f
```

Operadores binarios relacionales `<`, `>`, `<=`, `>=`

```racket
> (>= 1 1)
#t
> (> 1 1)
#f
> (< 0 1)
#t
> (<= 1 1)
#t
```

Operadores binarios lógicos `and`, `or`, `not`

```racket
> (and (> 1 1) (equal? 1 1))
#f
> (or (> 1 1) (equal? 1 1))
#t
> (not (> 1 1))
#t
```

Operadores binarios de aritmética entera `+`, `-`, `*`, `quotient`, `remainder`

```racket
> (+ 1 1)
2
> (* 2 3)
6
> (- 1 2)
-1
> (quotient 4 2)
2
> (remainder 3 2)
1
```

### Predicados de tipo

`symbol?`

```racket
> (symbol? (quote a))
#t
> (symbol? (cons (quote a) (quote b)))
#f
```

`number?`

```racket
> (number? 1)
#t
> (number? (quote a))
#f
> (number? (quote 1))
#t
```

`null?`

```racket
> (null? (quote ()))
#t
> (null? (cons 1 (quote ())))
#f
```

`pair?`

```racket
> (pair? (cons 1 2))
#t
> (pair? (cons 1 (quote ())))
#t
> (pair? (quote ()))
#f
> (pair? (quote (1 2 3)))
#t
```

`list?`

```racket
> (list? (cons 1 2))
#f
> (list? (cons 1 (quote ())))
#t
> (list? (quote ()))
#t
> (list? (quote (1 2 3)))
#t
```

## Ejercicios

Para probar nuestra implementación del lenguaje desarrollaremos los siguientes ejercicios en la carpeta `ejercicios`:

1. Implementar con `byorkt` las funciones de listas `append`, `length`, `member?`, `take`, `drop`, `list-ref`

```racket
> (append (quote (1 2)) (quote (2 3)))
'(1 2 2 3)
> (length (quote (4 5 6)))
3
> (member? (quote x3) (quote (x1 x2 x3)))
#t
> (take 3 (quote (4 5 6 7 8 9)))
'(4 5 6)
> (drop 2 (quote (4 5 6 7 8 9)))
'(8 9)
> (list-ref 0 (quote (4 5 6 7 8 9)))
4
> (list-ref 3 (quote (4 5 6 7 8 9)))
7
```

2. Implementar con `byorkt` las funciones de orden superior `filter`, `map`, `foldl`, `foldr`

```racket
> (filter even? (quote 1 2 3 4 5 6))
'(2 4 6)
> (map car (quote ((a 1) (b 3) (c -1))))
'(a b c)
> (foldl cons (quote ()) (quote (1 2 3 4)))
'(4 3 2 1)
> (foldr - 0 (quote (1 2 3 4)))
-2
> (foldl - 0 (quote (1 2 3 4)))
2
```

3. Implementar con `byorkt` una función para hacer derivación simbólica de expresiones algebraicas a partir de las 
siguientes reglas:
   * `k d/dx = 0`
   * `x d/dx = 1`
   * `(f + g) d/dx = f d/dx + g d/dx`
   * `(f * g) d/dx = f d/dx * g + f * g d/dx`
   * `x^n d/dx = n * x ^ (n - 1)`

```racket
> (derivar (quote (+ (* 2 x) (expt x 2))) 'x)
'(+ (* 2 1) (* 2 (* 1 (expt x 1))))
> (derivar (quote (+ x y)) 'x)
'(+ 1 0)
```

4. Implementar con `byorkt` una función para simplicar expresiones algebraicas asumiendo que no conocemos el valor las 
variables:

```racket
> (simplificar (quote (* x 0)))
0
> (simplificar (quote (* y 1)))
'y
> (simplificar (quote (expt x 0)))
1
> (simplificar (quote (expt x 1)))
'x
> (simplificar (quote (+ 0 a)))
'a
> (simplificar (quote (+ x x)))
'(* 2 x)
> (simplificar (quote (+ (* 2 1) (* 2 (* 1 (expt x 1)))))
'(+ 2 (* 2 x))
```

5. Implementar con `byorkt` las operaciones básicas de conjuntos `conjunto`,
`unión`, `diferencia`, `intersección`, `subconjunto?`, `prod-cart`

```racket
> (conjunto (quote (1 2 3 4 3 2 5)))
'(1 2 3 4 5)
> (unión 
    (conjunto (quote (1 2 3)))
    (conjunto (quote (2 3 4))))
'(1 2 3 4)
> (diferencia
    (conjunto (quote (1 2 3)))
    (conjunto (quote (2 3 4))))
'(1)
> (intersección
    (conjunto (quote (1 2 3)))
    (conjunto (quote (2 3 4))))
'(2 3)
> (subconjunto?       
    (conjunto (quote (1 2 3)))
    (conjunto (quote (2 3 4))))
#f
> (subconjunto?       
    (conjunto (quote (3 5)))
    (conjunto (quote (2 3 4 5))))
#t
> (prod-cart
    (conjunto (quote (a b)))
    (conjunto (quote (1 2 3))))
'((a 1) (a 2) (a 3) (b 1) (b 2) (b 3))
```

## Metodología

* El proyecto se trabajará en equipos.
* Cada equipo designará dos personas con el rol de relatoras, estas personas tendrán la responsabilidad de tomar notas durante las reuniones de revisión que serán utilizadas posteriormente para hacer mejoras al código.
* El proyecto se desarrollará en el transcurso de dos semanas y tendrá dos entregas, una entrega parcial después de la primera semana de trabajo, y una entrega final después de la segunda semana de trabajo.
* El proyecto se desarrollará en Kotlin siguiendo el paradigma orientado a objetos.

## Rúbricas de evaluación

Disponible en el siguiente enlace:

https://docs.google.com/spreadsheets/d/1_LX3k9GQz1oiP0dluWCX2S326DpykSfS8fzf2eZYyHY/edit?usp=sharing
