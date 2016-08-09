很多朋友在用多了Jquery之后，用javascript的时候获取class name 时总会会感觉力不从心，
那么，我之前也是那样，所以基本上都是写ID，但是后台很多地方都会用到ID，很多时候我们
就会和他们起冲突，那么为了我们愉快的配合后台，所以一般我们都会去获取Class name ，
那么，如果在只能用javascript原生的情况下，获取不到Class name怎么办？
其他的废话我就不多说了，我给大家一段解决的代码。
请将这段代码加在你的common.js里面，代码如下：

    function get ElementsByClassName(n)  {
      var classElements = [],allElements = document.getElementsByTagName('*');
      for (var i=0; i< allElements.length; i++ ) {
        if (allElements[i].className == n ) {
          classElements[classElements.length] = allElements[i];
        }
      }
      return classElements;
    }

友情提示：这段代码记得加在你的common.js里面。
