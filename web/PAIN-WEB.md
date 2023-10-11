# PAIN-WEB
The WASM code calls a console.log and then an alert function and then releasing itself, disabling any further requests to the exposed function `__()`

Specifically, the original main of the WASM code looked like this:

```golang
func main() {
	js.Global().Set("__", js.FuncOf(printMessage))
	js.Global().Get("console").Call("log", "__(\\d+): available")
	js.Global().Call("__", 0)
	<-c //releasing this stops the WASM execution
}
```

And the "`printMessage`" function looks like this:

```golang
func printMessage(this js.Value, inputs []js.Value) interface{} {
	key := inputs[0].Int()
	var msg string
	body := js.Global().Get("document").Call("getElementsByTagName", "body").Index(0)
	if key == 1337 {
		js.Global().Set("getFlag", js.FuncOf(getFlag))
		js.Global().Get("console").Call("log", "getflag(\\d+): available")
		msg = "The key is cool! Go and retrieve the flag!"
		body.Set("style", "background:yellow")
	} else {
		body.Set("style", "background:purple")
		msg = fmt.Sprintf("The key %d is not correct. It should be 1337! Try again!", key)
	}
	js.Global().Get("console").Call("log", msg)
	js.Global().Call("alert", msg)
	body.Set("style", "background:red")
	c <- true // this releases the main
	return nil
}
```

In this way, the program automatically disables itself at the first run, by releasing "`c`".

The logic is: breaking the js call causes wasm not to reach the end of the function, thus not releasing the app.\
So, setting a breakpoint before the `go.run(wasmModule.instance);` we can run:
```js
alert = '';
```
Then, we can resume the app and just run the following:
```js
__(1337);
```
This unlocks a new getflag function (typo, it actually is getFlag) that we can now brute:
```js
for (let i = 0; i< 100000; i++){
    if (getFlag(i)) {
        console.log(getFlag(i));
        break;
    }
}
```
That's it!