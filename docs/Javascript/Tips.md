# Tips

## Crear regiones de código en Javascript

```js
// #region [Name_Region]
import VueMoment from 'vue-moment';
import Vuetify from 'vuetify';
import 'vuetify/dist/vuetify.min.css';
import Vddl from 'vddl';
import Swatches from 'vue-swatches';
import 'vue-swatches/dist/vue-swatches.min.css';
// #endregion
```

## Copiar array

```js

const namesCopy = Object.values(names)
```

## Agregar una key a un objeto

```js
var result = arrOfObj.map(function(el) {
  var o = Object.assign({}, el);
  o.isActive = true;
  return o;
});
```
