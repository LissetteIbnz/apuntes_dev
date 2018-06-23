# Tips VSC

## JavaScript Type Checking

Sometimes type checking your JavaScript code can help you spot mistakes you might have not caught otherwise. You can run the TypeScript type checker against your existing JavaScript code by simply adding a // @ts-check comment to the top of your file.

```js
// @ts-check

let easy = true;
easy = 42; // marca easy como error porque easy fue previamente asignado como Boolean
```

## Rename Refactoring

It's easy to rename a symbol such as a function name or variable name. Hit F2 while in the symbol Book to rename all instances - this will occur across all files in a project. You can also see refactoring in the right-click context menu.

## Refactoring via Extraction

Sometimes you want to refactor already written code into a separate function or constant to reuse it later. Select the lines you want to refactor out and press Ctrl+. or click the little light bulb and choose one of the respective Extract to... options. Try it by selecting the code inside the if-clause on line 3 or any other common code you want to refactor out.

_Before_

```js
function findFirstEvenNumber(arr) {
    for (let i = 0; i < arr.length; i++) {
        if (typeof arr[i] === 'number' && arr[i] % 2 === 0) {
            return arr[i];
        }
    }
    return null;
}
```

_After_

```js
function findFirstEvenNumber(arr) {
    for (let i = 0; i < arr.length; i++) {
        if (extractFunction(i)) {
            return arr[i];
        }
    }
    return null;

    function extractFunction(i) {
        return typeof arr[i] === 'number' && arr[i] % 2 === 0;
    }
}
```

## Code Folding o contraer el código

`Ctrl+Mayús+'` to fold the code, press `Ctrl+Mayús+¡`.
To fold all sections use `Ctrl+K Ctrl+0` or to unfold all use `Ctrl+K Ctrl+J`.