watch: {
  input: function () {
    if (isLocalStorage() /* function to detect if localstorage is supported*/) {
      localStorage.setItem('storedData', this.input)
    }
  }
}
This will update variable’s value whenever user add new inputs.

Then assign the new value to variable like this:

app.input = localStorage.getItem('storedData');