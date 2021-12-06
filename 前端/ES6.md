# ES6
## Sample
### 拼接字符串
```
function StringBuffer () {
  this._strings_ = new Array();
}

StringBuffer.prototype.append = function(str) {
  this._strings_.push(str);
};

StringBuffer.prototype.toString = function() {
  return this._strings_.join("");
};

StringBuffer.prototype.join = function(char) {
  return this._strings_.join(char);
};
```