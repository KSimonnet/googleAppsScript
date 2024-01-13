# About

Illustration of client-server communication on Google Apps Script.

## Case study

In this example, an argument is passed from the client-side to the server-side for processing, and its reulst is sent back to the client-side asynchronously.

```html
<script>
  google.script.run.withSuccessHandler(clientSideFunction).bufferFunction(0);

  async function clientSideFunction(index) {
    const target_val = await captureFunction(index);
    console.log(target_val);
  }

  function captureFunction(x) {
    return new Promise((resolve) => {
      google.script.run
        .withSuccessHandler((result) => resolve(result))
        .serverSideFunction(x);
    });
  }
</script>
```

**Explanation of the algorithm**

Control follows the below steps:

1. Pass the argument '0' to the server-side function `bufferFunction`,
   which is located in a .gs file and whose sole purpose is to
   pass the argument to the client-side function `clientSideFunction`.

```js
function bufferFunction(x) {
  return x;
}
```

2. Then, control calls the `captureFunction` and passes the argument to it.
3. In turn, `captureFunction` calls `google.script.run`, which calls the server-side function `serverSideFunction`, located in a .gs file, with the argument.
   `serverSideFunction` processes the argument

```js
function serverSideFunction(i) {
  return i++;
}
```

4. Then, its return value is funneled back up via the client-side method `.withSuccessHandler()`,
5. which is then passed to the client-side function `clientSideFunction` through the resolve function of the Promise
6. whose fullfilled value is then assigned to the variable `target_val` by the `await` keyword.
7. Control can then use `target_val` on the client-side
