## Cache by request path, client ip and request params. Also cache by hh:mm:ss, with seconds rounded up to the duration you want to cache. E.g. for every 5 seconds
```js
const ss = (function () {
  const seconds = new Date().getSeconds()
  return seconds - seconds % 5
})()

const path = url.path
const key = `path=${path},${hh}:${mm}:${ss}`
```
