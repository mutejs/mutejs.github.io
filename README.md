# MUTE - Multi-User Text Editor

MUTE is a solution for enabling a multi-user editing experience in a variety
of web-based editors.

Projects include:

* [MUTE Core](https://github.com/mutejs/core) - the client-side application which coordinates with a remote server and your local editor
* [MUTE Server](https://github.com/mutejs/server) - a node.js library you can use to build your backend
* [MUTE CodeMirror](https://github.com/mutejs/codemirror) - a CodeMirror plugin to enable multi-user editing with remote cursors & selections in [CodeMirror](https://codemirror.net)

## MUTE is based on operational transform

Operational transform just one method that can be used to synchronize multiple
client in the same document. The use of operational transform requires some
sort of central authority to coordinate the multiple nodes. While this can
be a drawback, it also enables less data to be sent over the wire, and clients
don't need to be visible to each other, just to the central authority. (Technically
if the library implements the transform-property 2 you don't need a central
authority, but we're gonna simplify things here.)

## MUTE is operational transform library agnostic

MUTE is based on the [ot-types](https://github.com/otypes/docs) system, but
requires the following properties implemented:

* `transform(op1:Operation, op2:Operation, side:String) -> op:Operation` - used to make a change based on a previous version of a document apply-able to a later version of the document.  `side` is either `'left'` or `'right'`. In MUTE, the `'left'` hand side of the transform is kept on the server end and the `'right'` hand side is emitted to the client which originated the out-of-date change so that it may update it's local document to the latest version synced between the clients
* `compose(op1:Operation, op2:Operation) -> op:Operation` - used to "add" two subsequent changes together to generate a single operation that encompasses the intent of both underlying operations.
* `apply(document:Operation, op:Operation) -> document:Operation` - used to add a change to an existing document, where a document is a object which contains only contiguous insert statements, and no other operations.
* `transformCursor(cursor:number, op:Operation, isOwnOp:boolean) -> cursor:number` - used to determine the new location of a cursor after an operation has been applied to a document. The boolean flag allows the function to correctly place the cursor of a client involved in making the change to the correct place. (If the edit occurs at the place your cursor is located and you are not the one editing text, your cursor likely should not move)

## License
Copyright © 2018, Apache-2.0 License