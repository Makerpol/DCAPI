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
例：
````javascript
var paymentsByTotal = payments.dimension(function(d) { return d.total; });
````

给定的dimension访问器函数返回的值必须是确定的，并且对于crossfilter的存在永远不变。
性能说明：在内部，缓存给定的dimension的值。因此，如果 dimension值是从其他属性派生的，
没有必要在crossfilter以外派生值。只有当records添加到crossfilter中时才调用值函数。

dimension一旦创建就会绑定到crossfilter。创建超过8个dimension和超过16个dimension
会带来额外的开销。目前不支持一次超过32个dimension。但是可以用<a href="">**dimension.dispose**</a>
布置dimension来给新的dimension腾出空间。dimension的状态，记录关联的特定dimension的过滤器，如果真有的话。初期，如果dimension没有指定过滤器：所有的records都会被选中。由于创建dimension比较占用资源，因此应该谨慎保留对创建的任何dimension的引用。

<a>#</a> dimension<b>.filter</b>(<i>value</i>)

过滤records，使dimension值和<i>value</i>匹配，然后返回这个dimension。
>这个被指定的<i>value</i>如果是**null**，这种情况下此方法相当于<a href="">**filterAll**</a>；
>这个<i>value</i>如果是**数组**，这种情况下此方法相当于<a href="">**filterRange**</a>；
>这个<i>value</i>如果是**函数**，这种情况下此方法相当于<a href="">**filterFunction**</a>；
>除此之外，此方法相当于<a href="">**filterExact**</a>。

例：
````js
paymentsByTotal.filter([100, 200]); // 选取总额在100到200之间的付款
paymentsByTotal.filter(120); // 选取总额等于120的付款
paymentsByTotal.filter(function(d) { return d % 2; }); // 选取总额是奇数的付款
paymentsByTotal.filter(null); // 选取所有的付款
````
<i>如果调用过滤器将会替换现有的过滤器,如果dimension有的话</i>

<a>#</a> dimension<b>.filterExact</b>(<i>value</i>)

过滤records，使dimension值和<i>value</i>匹配，然后返回这个dimension。
例：
````js
paymentsByTotal.filterExact(120); // 选取总额等于120的付款
````
注意使用排序运算符<kbd><</kbd>, <kbd><=</kbd>和<kbd>></kbd>,<kbd>>=</kbd>进行精确比较。例如，如果传递的值是**null**， 这相当于0；
过滤不要用<kbd>==</kbd>和<kbd>===</kbd>运算符。

<i>如果调用过滤器将会替换现有的过滤器,如果dimension有的话</i>

<a>#</a> dimension<b>.filterRange</b>(<i>range</i>)

过滤records，使dimension值大于等于<i>range[0]</i>，小于<i>range[1]</i>，返回这个dimension。
例： 
````js
paymentsByTotal.filterRange([100, 200]); // 选取总额在100到200之间的付款
````

<a>#</a> dimension<b>.filterFunction</b>(<i>function</i>)

过滤records，被调用时使用指定的函数返回dimension值的真值，并返回dimension。
例：
````js
paymentsByTotal.filterFunction(function(d) { return d % 2; }); // // 选取总额是奇数的付款
````
这可以用于实现联合过滤器，例如：
````js
// 选取总额在0到10或者20到30之间的付款
paymentsByTotal.filterFunction(function(d) { return 0 <= d && d < 10 || 20 <= d && d < 30; });
````

<a>#</a> dimension<b>.filterAll</b>()

清除任何dimension的过滤器，选取所有的records并返回dimension。
例：
````js
paymentsByTotal.filterAll(); // 选取所有的付款
````

<a>#</a> dimension<b>.top</b>(<i>k</i>)

返回一个包含前<i>k</i>个records的数组，根据此dimension的自然顺序。被返回的数组是按照降序的自然顺序进行排列。这个方法和crossfilter当前的过滤器相交，只返回满足所有能动的过滤器（包括此维度的过滤器）的records。
例：
````js
var topPayments = paymentsByTotal.top(4); // the top four payments, by total
topPayments[0]; // the biggest payment
topPayments[1]; // the second-biggest payment
// etc.
````
如果根据crossfilter所有的过滤器选择的records比<i>k</i>少，那就返回一个少于<i>k</i>的数组。
例：
````js
var allPayments = paymentsByTotal.top(Infinity); //返回所有的
````

<a>#</a> dimension<b>.bottom</b>(<i>k</i>)

返回一个包含后<i>k</i>个records的数组，根据此dimension的自然顺序。被返回的数组是按照升序的自然顺序进行排列。这个方法和crossfilter当前的过滤器相交，只返回满足所有能动的过滤器（包括此维度的过滤器）的records。
例：
````js
var bottomPayments = paymentsByTotal.bottom(4); // the bottom four payments, by total
bottomPayments[0]; // the smallest payment
bottomPayments[1]; // the second-smallest payment
// etc.
````
<a>#</a> dimension<b>.dispose</b>()

删除这个dimension（包括它的groups）从crossfilter。这为添加到这个crossfilter其他的dimension释放了空间。


## Group (Map-Reduce)
<a>#</a> dimension<b>.group</b>([<i>groupValue</i>])

为给定的dimension构造一个新的群组，根据指定的<i>groupValue</i>函数，采取dimension的值作为输入值，并返回相应的四舍五入值。这个<i>groupValue</i>是可选的；如果不指定，则默认为<a href="https://en.wikipedia.org/wiki/Identity_function">恒等函数</a>。类似的值函数，<i>groupValue</i>必须返回自然有序的值；此外，这种顺序必须符合这个dimension的值函数。
例：
````js
var paymentGroupsByTotal = paymentsByTotal.group(function(total) { return Math.floor(total / 100); });
````

默认情况下，这个group的reduce函数将计算每个组的记录数。此外，这个groups将按照记录数排序。

注意：这个分组和crossfire当前的过滤器相交，**除了相关联的dimension的过滤器**。因此，group方法仅考虑满足除dimension的过滤器之外的每个过滤器的记录。所以，如果付款的ceossfilter是按照type和total过滤，则总额的group只按照type观察过滤器。
