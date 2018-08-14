## Set-Up
### Step 1 : Download a JS-Editor
* Using visual studio code right now.
[Download link](https://code.visualstudio.com/)

### Step 2 : Download and install Node
* We can run JS commands on browser>inspect-element>console (tiring and confusing).
* But we will use node.js to execute JS code and to insttall third party libraries. [Download link](https://nodejs.org/en/download/)
* Run command `export PATH=$PATH:/<path-directory>/bin`

### Step 3 : Folder set-up
* Create a folder inside your drive and drag and drop it onto `VS code` running.
* This will add the folder as current working directory for js.

### Step 4 : Add a live server
* Go to `Extensions` tab of your editor and search for `live server`
* Create a file `index.html` and dummy run the server by `right-click > Open with live server`.

> This will open your browser and display your content in `index.html`.


## Project Demo
### Basic concepts
* We can add JS scripts in `head` or in `body` sections. Best practice is to put it at the end of the `body` section.

* Reason for putting it at the end:
	* Browser parses file from top to bottom. If element is at the `head`, we will end up having many js-elements in the `head` section of the js file and browser may get busy parsing and executing the scripts instead of rendering the content of html page.

	This will result in bad user experience and user will see a blank/white page on boot-up.

	* Almost all the code that we have in scripts needs to talk with elements of html present in `body` section. Adding element at the end of body section will ensure that all those elemetns are rendered.

> Note : There are certain exceptions when we have elements inside `head` section but this is the best method/ideology.

* We have millions of lines of js code and it is not apt to write it along with html code. We want to separate it. 
	* `html` is all abouot content and `js` is all about behavior.
	* Create separate js files and refer them at the end of html>body.

### Statements
* A piece of code that expresses an action to be carried out.
* All statements of scripts are terminated by a `;`.
* Strings are insite `''`.
* Comments are just like java `//`.

### Executing code in Node
* We have `index.html` and `index.js` files and we will be running it on `Node`.
* 