## Standalone UUID generator in Javascript

Source: 

Standalone UUID generator in Javascript
https://abhishekdutta.org/blog/standalone_uuid_generator_in_javascript.html

```js
// Author: Abhishek Dutta, 12 June 2020
// License: CC0 (https://creativecommons.org/choose/zero/)
function uuid() {
  var temp_url = URL.createObjectURL(new Blob());
  var uuid = temp_url.toString();
  URL.revokeObjectURL(temp_url);
  return uuid.substr(uuid.lastIndexOf('/') + 1); // remove prefix (e.g. blob:null/, blob:www.test.com/, ...)
}

```