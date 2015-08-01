# JsSearch: client-side search library

Js Search enables efficient client-side searches of JavaScript and JSON objects.
It is ES5 compatible and does not require jQuery or any other third-party libraries.

Js Search is a lightweight version of [Lunr JS](http://lunrjs.com/). It doesn't offer as many features but it is faster which may make it a compelling option in some cases. Here are some JS Perf benchmarks comparing the two libraries:

* [Js Search vs Lunr JS: Building an index](http://jsperf.com/js-search-vs-lunr-js-build-search-index)
* [Js Search vs Lunr JS: Running searches](http://jsperf.com/js-search-vs-lunr-js-running-searches)

### Installation

You can install using either [Bower](http://bower.io/) or [NPM](https://www.npmjs.com/) like so:

```shell
npm install js-search
bower install js-search
```

### Overview

At a high level you configure Js Search by telling it which fields it should index for searching and then add the objects to be searched.

For example, a simple use of JS Search would be as follows:

```javascript
var theGreatGatsby = {
  isbn: '9781597226769',
  title: 'The Great Gatsby',
  author: 'F. Scott Fitzgerald'
};
var theDaVinciCode = {
  isbn: '0307474275',
  title: 'The DaVinci Code',
  author: 'Dan Brown'
};
var angelsAndDemons = {
  isbn: '074349346X',
  title: 'Angels & Demons',
  author: 'Dan Brown'
};

var search = new JsSearch('isbn');
search.addIndex('title');
search.addIndex('author');
search.addDocuments([theGreatGatsby, theDaVinciCode, angelsAndDemons]);
search.search('The');   // [theGreatGatsby, theDaVinciCode]
search.search('scott'); // [theGreatGatsby]
search.search('d');     // [angelsAndDemons, theDaVinciCode]
```

### Tokenization

Tokenization is the process of breaking text (e.g. sentences) into smaller, searchable tokens (e.g. words or parts of words). Js Search provides a basic tokenizer by default that should work well for English but you can provider your own like so:

```javascript
search.tokenizer = {
  tokenize(text) {
    // Convert text to an Array of strings however you'd like
  }
};
```

### Stemming

Stemming is the process of reducing search tokens to their root (or "stem") so that searches for different forms of a word will still yield results. For example "search", "searching" and "searched" all get reduced to the stem "search".

Js Search does not currently provide stemming but you can add it by supplying a custom index strategy like so:

```javascript
search.indexStrategy = {
  expandToken(token) {
    // Token is a string (e.g. search, searching, searched)
    // You could run it throug a stemmer first
    // Then pass it onto another built-in class like PrefixIndexStrategy
  }
};
```

### Stop Words

Stop words are very common (e.g. a, an, and, the, of) and are often not semantically meaningful. By default Js Search does not filter these words, but filtering can be achieved using a custom index strategy like so:

```javascript
search.indexStrategy = {
  expandToken(token) {
    // Token is a string (e.g. search, searching, searched)
    // If token is a stop-word (e.g. a, and, the)
    // Then return an empty array to remove it from the search index
  }
};
```