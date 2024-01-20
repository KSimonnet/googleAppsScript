# About

Illustration of client-server communication on Google Apps Script.

## Case study

In this example, an argument is passed from the client-side to the server-side for processing, and its reulst is sent back to the client-side asynchronously. The `google.script.run` object is a client-side API provided by Google Apps Script (https://developers.google.com/apps-script/guides/html/reference/run). It allows communication between the client-side code and server-side code.

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

1. `google.script.run.withSuccessHandler(clientSideFunction).bufferFunction(0)` is the entry point of the client-side code. It calls the server-side function `bufferFunction` located in a .gs file and passes the argument '0' to it.
2. `bufferFunction` is a simple function that takes an argument 'x' and returns it as is:

```js
function bufferFunction(x) {
  return x;
}
```

its sole purpose is to pass the argument to the client-side function `clientSideFunction`.

3. Then, control pauses execution over `await`, which calls the `captureFunction` and passes the argument to it.

[//] # (TODO - `google.script.run` is an asynchronous client-side JavaScript API. Async functions always return a promise. Hence, I suspect the use of `new Promise` is redundant)

4. Inside it a new `Promise` is created, which wraps the server-side function call (i.e. `google.script.run` calls the server-side function `serverSideFunction`, located in a .gs file, and passes the argument to it).
5. `captureFunction` holds the Promise settlement in its `return`
6. `serverSideFunction` processes the argument, as follows:

```js
function serverSideFunction(i) {
  return i++;
}
```

7. Then, its return value is passed, via the GAS API, to the client-side method `.withSuccessHandler()`,
   which specifies a callback function that is then executed upon successfull completion.
8. This anonymous function then passes the aforementioned value and wraps it in `Promise.resolve`,
9. which funnels it back up as a fulfilled result and settles the Promise.
10. Upon settlement, the Promise response is returned by the `captureFunction`, back to the `await` inside the client-side function `clientSideFunction`
11. `await` passes the resolved value of the Promise returned, for it to then be assigned to the variable `target_val`.
12. Control can then use `target_val` on the client-side
