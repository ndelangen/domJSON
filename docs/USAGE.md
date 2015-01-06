
##FilterLists

A `FilterList` is a custom type for certain options passed to domJSON's [`.toJSON()` method](#domJSON.toJSON).  It allows for very granular control over which fields are included in the final JSON output, allowing developers to eliminate useless information from the result, and produce extremely compact JSON objects.  It operates based on boolean logic: an _inclusive_ `FilterList` allows the developer to explicity sepcify which fields they would like to see in the output, while an _exclusive_ `FilterList` allows them to specify which fields they would like to omity (e.g., "Copy every available field _except_ X, Y, and Z").  The `FilterList` accepts an object as an argument, or a shorthand array (which is the recommended style, since it's much less verbose).

Take this example: suppose we have a single `div` that we would like to convert into a JSON object using domJSON.  It looks like this in its native HTML:

```html
<div id="myDiv" class="testClass" style="margin-top: 10px;" data-foo="bar" data-quux="baz">
	This is some sample text.
</div>
```

Let's try and make a JSON object out of this field, but _only_ include the `class` and `style` attributes, and only the `offsetTop` and `offsetLeft` DOM properties.  Both of the following inputs will have the same output:

```javascript
var myDiv = document.getElementById('myDiv');

//Using object notation for our filterLists
var jsonOutput = domJSON.toJSON(myDiv, {
	attributes: {
		values: ['class', 'style']
	},
	domProperties: {
		values: ['offsetLeft', 'offsetTop']
	},
	metadata: false
});

//Same thing, using the array notation
var jsonOutput = domJSON.toJSON(myDiv, {
	attributes: [false, 'class', 'style'],
	domProperties: [false, 'offsetLeft', 'offsetTop'],
	metadata: false
});
```

The result:
```json
{
	"attributes": {
		"class": "testClass",
		"style": "margin-top: 10px;"
	},
	"childNodes": [{
		"nodeType": 3,
		"nodeValue": "This is some sample text"
	}],
	"nodeType": 1,
	"nodeValue": "This is some sample text",
	"offsetLeft": 123,
	"offsetTop": 456,
	"tagName": "DIV"
}
```

The array notation is much shorter, and very easy to parse: the first value is a boolean that determines whether the list is exclusive or not, and the remaining elements are just the filter values themselves.  Here is an example for making an exclusive list of attributes on the same `div` as above:

```javascript
var myDiv = document.getElementById('myDiv');

//Using object notation for our filterLists, but this time specify values to EXCLUDE
var jsonOutput = domJSON.toJSON(myDiv, {
	attributes: {
		exclude: true,
		values: ['class', 'style']
	},
	domProperties: {
		values: ['offsetLeft', 'offsetTop']
	},
	metadata: false
});

//Same thing, using the array notation
var jsonOutput = domJSON.toJSON(myDiv, {
	attributes: [true, 'class', 'style'],
	domProperties: [false, 'offsetLeft', 'offsetTop'],
	metadata: false
});
```

The result:
```json
{
	"attributes": {
		"id": "myDiv",
		"data-foo": "bar",
		"data-quux": "baz"
	},
	"childNodes": [{
		"nodeType": 3,
		"nodeValue": "This is some sample text"
	}],
	"nodeType": 1,
	"nodeValue": "This is some sample text",
	"offsetLeft": 123,
	"offsetTop": 456,
	"tagName": "DIV"
}
```