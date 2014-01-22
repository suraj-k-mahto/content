---
title: 'A spelling and grammar checker for Node'
tags: [node, grammar, javascript, spelling, language, library]
header: 'http://cdn.robinvdvleuten.nl/articles/52c6e411b8b90.jpeg'
published: true
---

When you need to write in a foreign language, you'll probably use [Google Translate](http://translate.google.com). An awesome service that does a great job for translating individual words. But what about the grammar of a sentence? Like for example 'Edwards will be sck yesterday'. It will corrects 'sck' to 'sick', but it doesn't correct 'will be' to 'was'.
Thankfully there are some other services for that and I created a Node wrapper for one of them. Say hello to [Gingerbread](https://github.com/RobinvdVleuten/gingerbread).
<!-- more -->

Gingerbread uses the excellent free API from [Ginger](http://www.gingersoftware.com/) and is based on the [gingerice](https://github.com/subosito/gingerice) gem. You can install it as CLI executable or use it in your Node scripts. Install the library with the following command through NPM;

```bash
$ npm install gingerbread
```

To install it as global executable, append `-g` to the above command.
Then in your scripts, use the library like this;

```javascript
var gingerbread = require('gingerbread');

gingerbread('The smelt of fliwers bring back memories.', {/** options to override **/}, function (error, text, result, corrections) {
  // result contains 'The smell of flowers brings back memories.'
});
```

See the [README](https://github.com/RobinvdVleuten/gingerbread/blob/master/README.md) for more usage information or report any issues [here](https://github.com/RobinvdVleuten/gingerbread/issues).

Photo credits: [http://www.flickr.com/photos/dichohecho/4279468557/](http://www.flickr.com/photos/dichohecho/4279468557/)