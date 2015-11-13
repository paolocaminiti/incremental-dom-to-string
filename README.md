# incremental-dom-to-string
Early experiments with IncrementalDOM server-side rendering. As of now this is just a [proof of concept](https://github.com/google/incremental-dom/issues/50) all comments and ideas are welcome.

### Usage
Import it as you would import the real thing
```
var IncrementalDOM = require('incremental-dom-to-string.js')
```

Before running the patch code you can (optionally) set a target output with:
```
setOutput(prettyPrint, output, doneCallback, keepOpen)
```
- prettyPrint: nothing or a string to be used for indentation.
- output: 
  - an object: it will get a .rendered property containing the output. it defaults to a new Object.
  - a stream: the stream will be directly filled using it's .write method, it's reference is then passed to the callback, it can be an http response, a file stream, std.process...
- doneCallback: a function being called when patch is done, it receives the output reference.
- keepOpen: prevents flushing the internal state when patch is done, it can later be flushed calling .forceReset()

IncrementalDOM.patch "node" argument can now be empty or one of the following:
- a function: get passed the patch description function as a partial, this can be used to wrap the patch into an outer description function to generate an approot, note that executing the partial is responsability of the node function. Check document.js in the demos for examples
- an object: if it contains a .innerHTML property it will be assigned the patch rendered result, this can enable some other kind of composition or fill an Element.innerHTML when testing in a browser. When no output or doneCallback is set you can still read the output from the node reference.

The methods expected to return an element will return a mock object, you can assign or merge to this object whatever suits your code using:
```
setElementDummy(obj, merge)
```

### Examples
all examples are based on [localvoid's incremental dom port of dbmonster](https://github.com/localvoid/idom-dbmonster)

##### dbmonster-server
```
$ cd demo/dbmonster-server
$ node server.js
```
serves a server side rendered version of dbmonster, look at server.js to check some possible usages of .setOutput for serving. Once the prerendered document is loaded the normal js app will kick in (throttle your network to notice it)

##### dbmonster-streaming
```
$ cd demo/dbmonster-streaming
$ node server.js
```
streams a living dbmonster output, be advised that when streming to http it will likely flood your browser tab process in a few seconds.

##### dbmonster-browser
start a static server in the root of the project

localhost:8080/demo/dbmonster-browser/index.html is a client side dbmonster only using the string interpreter, this is just usefull for test and development.
