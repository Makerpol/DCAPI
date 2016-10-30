## Crossfilter API Reference
crossfilter 表示多维数组集

<a>#</a> <b>crossfilter</b>([<i>records</i>])

构建新的多维数组集。记录records被指定，添加指定的记录records。
记录records可以是任意的数组，JavaScript对象或者基础数据。如例：

````javascript
var payments = crossfilter([
  {date: "2011-11-14T16:17:54Z", quantity: 2, total: 190, tip: 100, type: "tab"},
  {date: "2011-11-14T16:20:19Z", quantity: 2, total: 190, tip: 100, type: "tab"},
  {date: "2011-11-14T16:28:54Z", quantity: 1, total: 300, tip: 200, type: "visa"},
  {date: "2011-11-14T16:30:43Z", quantity: 2, total: 90, tip: 0, type: "tab"},
  {date: "2011-11-14T16:48:46Z", quantity: 2, total: 90, tip: 0, type: "tab"},
  {date: "2011-11-14T16:53:41Z", quantity: 2, total: 90, tip: 0, type: "tab"},
  {date: "2011-11-14T16:54:06Z", quantity: 1, total: 100, tip: 0, type: "cash"},
  {date: "2011-11-14T16:58:03Z", quantity: 2, total: 90, tip: 0, type: "tab"},
  {date: "2011-11-14T17:07:21Z", quantity: 2, total: 90, tip: 0, type: "tab"},
  {date: "2011-11-14T17:22:59Z", quantity: 2, total: 90, tip: 0, type: "tab"},
  {date: "2011-11-14T17:25:45Z", quantity: 2, total: 200, tip: 0, type: "cash"},
  {date: "2011-11-14T17:29:52Z", quantity: 1, total: 200, tip: 100, type: "visa"}
]);
````
<a>#</a> crossfilter<b>.add</b>(<i>records</i>)

添加指定的记录records到多维数组集crossfilter。

<a>#</a> crossfilter<b>.remove</b>()

从多维数组集中删除所有当前过滤器匹配的记录records。

<a>#</a> crossfilter<b>.size</b>()

返回记录records的数量在多维数组集，独立的任何过滤器中。例如，已经添加单一批次的
记录到多维数据集中，这个方法将返回records.length。

<a>#</a> crossfilter<b>.groupAll</b>()

一个便捷的功能对所有的记录进行分组和减少为单一值。
<i>注意：这个groupAll观察所有的过滤器，和dimension的groupAll不一样。</i>


## Dimension

<a>#</a> crossfilter<b>.dimension</b>(<i>value</i>)

使用指定的值访问器函数构造一个新的dimension。这个函数必须返回自然有序的值，即：
正确的行为关于javascript的<kbd><</kbd>, <kbd><=</kbd>和<kbd>></kbd>,<kbd>>=</kbd>运算符。
这通常意味着基础数据：booleans，数字或者字符串； 然而： 可以重写object.valueOf来保证这个值和给定的对象值相似，如日期。

特别是，注意<kbd>null</kbd>和<kbd>undefined</kbd>是不支持的。此外，混合类型应该采用，例如：字符串和数字。
如果字符串和数字混合，那么字符串将会强制转换成数字，因此字符串必须可以强制转换成数字，否则就会导致<kbd>NaN</kbd>。

例：通过付款总额创建新的dimension
````javascript
var paymentsByTotal = payments.dimension(function(d) { return d.total; });
````

给定的dimension访问器函数返回的值必须是确定的，并且对于crossfilter的存在永远不变。
性能说明：在内部，缓存给定的dimension的值。因此，如果 dimension值是从其他属性派生的，
没有必要在crossfilter以外派生值。只有当records添加到crossfilter中时才调用值函数。

dimension一旦创建就会绑定到crossfilter。创建超过8个dimension和超过16个dimension
会带来额外的开销。目前不支持一次超过32个dimension。但是可以用<a>**dimension.dispose**</a>
布置dimension来给新的dimension腾出空间。