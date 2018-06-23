# Tratamiento de Arrays

Ambos métodos, a diferencia de otros más usados como filter o map, no devuelven un array filtrado en base a una condición, sino que devuelven valores booleanos (true o false).

## .some

El método .some filtra nuestro array de acuerdo a una condición que hayamoss preestablecido y busca entre los elementos del array si al menos uno de ellos cumple con dicha condición. En caso de que alguno (some) de ellos la cumpla, el resultado que devolverá será true, en caso negativo devolverá false.

## .every

A diferencia del método .some, .every busca que todos los elementos del array cumplan esa condición, si alguno la cumple, en este caso, ya no nos devolverá true. Todos y cada uno de los elementos (every) del array deben cumplir dicha condición para que retorne true, en caso contrario devolverá false.