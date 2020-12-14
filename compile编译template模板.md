> <b style="color:red;">compile</b>（编译）分为三个部分：parse、optimize、generate。最终得到render function

```html
    <div :class="c" class="demo" v-if="isShow">
        <span v-for="item in sz">{{item}}</span>
    </div>
```
```javascript
    var html = '<div :class="c" class="demo" v-if="isShow"><span v-for="item in sz">{{item}}</span></div>';
```

> parse所用到的正则

 ```javascript
    // 字符串里用正则需要多一层转义，所以'[\\w]'相当于在正则表达式中的[\w]
    const ncname = '[a-zA-Z_][\\w\\-\\.]*'; // 匹配a到z，A到Z或者_的一个加\w、\-、\. 的一个或者多个
    const singleAttrIdentifier = /([^\s"'<>/=]+)/; // 匹配不包括空格、制表符、换页符、"、'、<>、/、= 的一个或者多个
    const singleAttrAssign = /(?:=)/; // 非捕获元 匹配=的第一个 ?:用来清除使用括号的缓存
    const singleAttrValues = [ // [""([^"]*)"+", "'([^']*)'+", "([^\s"'=<>`]+)"]
    /"([^"]*)"+/.source,
    /'([^']*)'+/.source,
    /([^\s"'=<>`]+)/.source
    ];
    const attribute = new RegExp(
    '^\\s*' + singleAttrIdentifier.source + // /^\s*([^\s"'<>/=]+)(?:\s*((?:=))\s*(?:"([^"]*)"+|'([^']*)'+|([^\s"'=<>`]+)))?/
    '(?:\\s*(' + singleAttrAssign.source + ')' + // 匹配\s的一个或者多个 不包括空格、制表符、换页符、"、'、<>、/、= 的一个或者多个 匹配\s的一个或者多个= \s的一个或者多个"
    '\\s*(?:' + singleAttrValues.join('|') + '))?'
    );

    const qnameCapture = '((?:' + ncname + '\\:)?' + ncname + ')';
    const startTagOpen = new RegExp('^<' + qnameCapture);
    const startTagClose = /^\s*(\/?)>/;

    const endTag = new RegExp('^<\\/' + qnameCapture + '[^>]*>');

    const defaultTagRE = /\{\{((?:.|\n)+?)\}\}/g;

    const forAliasRE = /(.*?)\s+(?:in|of)\s+(.*)/; 
 ```
* **parse**

```javascript
    // 我们要解析完某段html需要删掉所以
    const stack = [];
    let currentParent, root;
    let index = 0;
    function advance (n) {
        index += n;
        html = html.substring(n);
    }
```