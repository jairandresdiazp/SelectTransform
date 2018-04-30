# ST

It is based on the original package [st.js](https://github.com/SelectTransform/st.js)

JSON Selector + Transformer

- Website: [https://jairandresdiazp.github.io/SelectTransform/](https://jairandresdiazp.github.io/SelectTransform/)
- Editor: [https://jairandresdiazp.github.io/SelectTransform/EditorTemplate/EditorTemplate](https://jairandresdiazp.github.io/SelectTransform/EditorTemplate/EditorTemplate)

![preview](https://raw.githubusercontent.com/jairandresdiazp/SelectTransform/master/demo.gif)

1. **Select:** Query any JSON tree to select exactly the subtree you are looking for.
2. **Transform:** Transform any JSON object to another by parsing with a template, also written in JSON

You can also mix and match Select AND Transform to perform partial transform, modularize JSON objects, etc.

# Features

## 1. Select

Select a JSON object or its subtree that matches your criteria

> Step 1. Take any JSON object

```js
var data = {
  "links": [
    { "remote_url": "http://localhost" },
    { "file_url": "file://documents" },
    { "remote_url": "https://blahblah.com" }
  ],
  "preview": "https://image",
  "metadata": "This is a link collection"
}
```

> Step 2. Find all key/value pairs that match a selector function

```js
var sel = ST.select(data, function(key, val) {
  return /https?:/.test(val);
})
```

> Step 3. Get the result

```js
var keys = sel.keys();
//  [
//    "remote_url",
//    "remote_url",
//    "preview"
//  ]

var values = sel.values();
//  [
//    "http://localhost",
//    "https://blahblah.com",
//    "https://image"
//  ]

var paths = sel.paths();
//  [
//    "[\"links\"]",
//    "[\"links\"]",
//    ""
//  ]
```

## 2. Transform

Use template to transform one object to another

> Step 1. Take any JSON object

```js
var data = {
  "title": "List of websites",
  "description": "This is a list of popular websites"
  "data": {
    "sites": [{
      "name": "Google",
      "url": "https://google.com"
    }, {
      "name": "Facebook",
      "url": "https://facebook.com"
    }, {
      "name": "Twitter",
      "url": "https://twitter.com"
    }, {
      "name": "Github",
      "url": "https://github.com"
    }]
  }
}
```

> Step 2. Select and transform with a template JSON object

```js
var sel = ST.select(data, function(key, val){
            return key === 'sites';
          })
          .transformWith({
            "items": {
              "{{#each sites}}": {
                "tag": "<a href='{{url}}'>{{name}}</a>"
              }
            }
          })

```


> Step 3. Get the result

```js
var keys = sel.keys();
//  [
//    "tag",
//    "tag",
//    "tag",
//    "tag"
//  ]

var values = sel.values();
//  [
//    "<a href='https://google.com'>Google</a>",
//    "<a href='https://facebook.com'>Facebook</a>",
//    "<a href='https://twitter.com'>Twitter</a>",
//    "<a href='https://github.com'>Github</a>"
//  ]

var objects = sel.objects();
//  [
//    {
//      "tag": "<a href='https://google.com'>Google</a>"
//    }, {
//      "tag": "<a href='https://facebook.com'>Facebook</a>"
//    }, {
//      "tag": "<a href='https://twitter.com'>Twitter</a>"
//    }, {
//      "tag": "<a href='https://github.com'>Github</a>"
//    }
//  ]

var root = sel.root();
//  {
//    "items": [{
//      "tag": "<a href='https://google.com'>Google</a>"
//    }, {
//      "tag": "<a href='https://facebook.com'>Facebook</a>"
//    }, {
//      "tag": "<a href='https://twitter.com'>Twitter</a>"
//    }, {
//      "tag": "<a href='https://github.com'>Github</a>"
//    }]
//  }
```

## 3. Transform HTTP

> allows to recover by post any value of an external dataset

```js
var data = {
    "id": 1
};

var template = {
  "test":"{{id.toString().trim()}}",
  "test2":"{{httpPost('webhook%2F860001619','GET',null)}}"
}
```

> Get the result

```js
{
  "test": "1",
  "test2": {
    "error": true,
    "message": "key invalid"
  }
}
```

### function http

allows to recover any value of an external dataset

| Param | Description |Required |
| ------ | ------ | ------ |
| URL | url where the POST is made | yes|
| Type | HTTP verb used is supported POST and GET | yes|
| Headers | arrangement with the following structure that represents the header of the POST to send, by default it is assigned "Content-Type", "application / json" the others must be specified if not required you must send null, eg [{'name': 'filter','value': {'tabla': 'adquiriente','nit': '900032159'}},{'name': 'field','value': 'email'}]' | yes|
| body | body of the request in JSON format if it does not exist, do not send the value eg httpPost ('url', null)}} | yes|


in case of requiring values of the data as parameter of the function use the syntax

```js
[[data]]  httpPost('[[id]]',null)
``` 

the parameters of the function must be encoded [see](https://www.freeformatter.com/url-encoder.html) 

### example

```js
httpPost('webhook%2F860001619','POST', 0, '%5B%7B%27name%27%3A%20%27filter%27%2C%27value%27%3A%20%7B%27tabla%27%3A%20%27adquiriente%27%2C%27nit%27%3A%20%27900032159%27%7D%7D%2C%7B%27name%27%3A%20%27field%27%2C%27value%27%3A%20%27email%27%7D%5D')
```

# Usage

## In a browser

```js
<script src="st.js"></script>
<script>
var parsed = ST.select({ "items": [1,2,3,4] })
                .transformWith({
                  "{{#each items}}": {
                    "type": "label", "text": "{{this}}"
                  }
                })
                .root();
</script>
```

## In node.js

> Install through npm:

```bash
$ npm install stjs
```

> Use

```js
const ST = require('st');

const parsed = ST.select({ "items": [1,2,3,4] })
                .transformWith({
                  "{{#each items}}": {
                    "type": "label", "text": "{{this}}"
                  }
                })
                .root();
```

### Learn more at [https://github.com/jairandresdiazp/SelectTransform/wiki](https://github.com/jairandresdiazp/SelectTransform/wiki)
