> 我们知道，在render function 会被转化为VNode节点。然后再通过一系列操作将虚拟节点映射到真实环境中。因为我们VDom是以VNode（Javascript）的形式存在，所以它拥有了跨平台的能力。

```javascript
    class VNode{
        constructor(tag, data, text, children, elm){
            this.tag = tag; // 当前节点的标签名
            this.data = data; // 当前节点的相关信息，比如prop、attr
            this.text = text; // 当前节点的文本
            this.children = children; // 当前节点的子节点
            this.elm = elm; // 当前虚拟节点对应的真实节点
        }
    }
    // 创建一个空节点
    function createEmptyNode() {
        const vnode = new VNode();
        vnode.text = '';
        return vnode;
    }
    // 创建一个文本内容节点
    function createTextVNode(val) {
        return new VNode(undefined, undefined, String(val));
    }
    // 克隆一个节点
    function cloneVNode(node) {
        const cloneNode = new VNode(
            node.tag,
            node.data,
            node.text,
            node.children,
            node.elm
        );
        return cloneNode;
    }
```