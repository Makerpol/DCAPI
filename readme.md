## Crossfilter API Reference
crossfilter 表示多维数组集

<a name="crossfilter" href="#crossfilter">#</a> <b>crossfilter</b>([<i>records</i>])

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
<a name="crossfilter_add" href="#crossfilter_add">#</a> crossfilter<b>.add</b>(<i>records</i>)

添加指定的记录records到多维数组集crossfilter。

<a name="crossfilter_remove" href="#crossfilter_remove">#</a> crossfilter<b>.remove</b>()

从多维数组集中删除所有当前过滤器匹配的记录records。

<a name="crossfilter_size" href="#crossfilter_size">#</a> crossfilter<b>.size</b>()

返回记录records的数量在多维数组集，独立的任何过滤器中。例如，已经添加单一批次的
记录到多维数据集中，这个方法将返回records.length。

<a name="crossfilter_groupAll" href="#crossfilter_groupAll">#</a> crossfilter<b>.groupAll</b>()

一个便捷的功能对所有的记录进行分组和减少为单一值。
<i>注意：这个groupAll观察所有的过滤器，和dimension的groupAll不一样。</i>


## Dimension

<a name="crossfilter_dimension" href="#crossfilter_dimension">#</a> crossfilter<b>.dimension</b>(<i>value</i>)

使用指定的值访问器函数构造一个新的dimension。这个函数必须返回自然有序的值，即：
正确的行为关于javascript的`<`, `<=`和`>`,`>=`运算符。
这通常意味着基础数据：booleans，数字或者字符串； 然而： 可以重写object.valueOf来保证这个值和给定的对象值相似，如日期。

特别是，注意`null`和`undefined`是不支持的。此外，混合类型应该采用，例如：字符串和数字。
如果字符串和数字混合，那么字符串将会强制转换成数字，因此字符串必须可以强制转换成数字，否则就会导致`NaN`。
例：
````javascript
var paymentsByTotal = payments.dimension(function(d) { return d.total; });
````

给定的dimension访问器函数返回的值必须是确定的，并且对于crossfilter的存在永远不变。
性能说明：在内部，缓存给定的dimension的值。因此，如果 dimension值是从其他属性派生的，
没有必要在crossfilter以外派生值。只有当records添加到crossfilter中时才调用值函数。

dimension一旦创建就会绑定到crossfilter。创建超过8个dimension和超过16个dimension
会带来额外的开销。目前不支持一次超过32个dimension。但是可以用<a href="#dimension_dispose">**dimension.dispose**</a>
布置dimension来给新的dimension腾出空间。dimension的状态，记录关联的特定dimension的过滤器，如果真有的话。初期，如果dimension没有指定过滤器：所有的records都会被选中。由于创建dimension比较占用资源，因此应该谨慎保留对创建的任何dimension的引用。

<a name="dimension_filter" href="#dimension_filter">#</a> dimension<b>.filter</b>(<i>value</i>)

过滤records，使dimension值和<i>value</i>匹配，然后返回这个dimension。
>这个被指定的<i>value</i>如果是**null**，这种情况下此方法相当于<a href="#dimension_filterAll">**filterAll**</a>；
>这个<i>value</i>如果是**数组**，这种情况下此方法相当于<a href="#dimension_filterRange">**filterRange**</a>；
>这个<i>value</i>如果是**函数**，这种情况下此方法相当于<a href="#dimension_filterFunction">**filterFunction**</a>；
>除此之外，此方法相当于<a href="#dimension_filterExact">**filterExact**</a>。

例：
````js
paymentsByTotal.filter([100, 200]); // 选取总额在100到200之间的付款
paymentsByTotal.filter(120); // 选取总额等于120的付款
paymentsByTotal.filter(function(d) { return d % 2; }); // 选取总额是奇数的付款
paymentsByTotal.filter(null); // 选取所有的付款
````
<i>如果调用过滤器将会替换现有的过滤器,如果dimension有的话</i>

<a name="dimension_filterExact" href="#dimension_filterExact">#</a> dimension<b>.filterExact</b>(<i>value</i>)

过滤records，使dimension值和<i>value</i>匹配，然后返回这个dimension。
例：
````js
paymentsByTotal.filterExact(120); // 选取总额等于120的付款
````
注意使用排序运算符`<`, `<=`和`>`,`>=`进行精确比较。例如，如果传递的值是**null**， 这相当于0；
过滤不要用`==`和`===`运算符。

<i>如果调用过滤器将会替换现有的过滤器,如果dimension有的话</i>

<a name="dimension_filterRange" href="#dimension_filterRange">#</a> dimension<b>.filterRange</b>(<i>range</i>)

过滤records，使dimension值大于等于<i>range[0]</i>，小于<i>range[1]</i>，返回这个dimension。
例： 
````js
paymentsByTotal.filterRange([100, 200]); // 选取总额在100到200之间的付款
````

<a name="dimension_filterFunction" href="#dimension_filterFunction">#</a> dimension<b>.filterFunction</b>(<i>function</i>)

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

<a name="dimension_filterAll" href="#dimension_filterAll">#</a> dimension<b>.filterAll</b>()

清除任何dimension的过滤器，选取所有的records并返回dimension。
例：
````js
paymentsByTotal.filterAll(); // 选取所有的付款
````

<a name="dimension_top" href="#dimension_top">#</a> dimension<b>.top</b>(<i>k</i>)

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

<a name="dimension_bottom" href="#dimension_bottom">#</a> dimension<b>.bottom</b>(<i>k</i>)

返回一个包含后<i>k</i>个records的数组，根据此dimension的自然顺序。被返回的数组是按照升序的自然顺序进行排列。这个方法和crossfilter当前的过滤器相交，只返回满足所有能动的过滤器（包括此维度的过滤器）的records。
例：
````js
var bottomPayments = paymentsByTotal.bottom(4); // the bottom four payments, by total
bottomPayments[0]; // the smallest payment
bottomPayments[1]; // the second-smallest payment
// etc.
````
<a name="dimension_dispose" href="#dimension_dispose">#</a> dimension<b>.dispose</b>()

删除这个dimension（包括它的groups）从crossfilter。这为添加到这个crossfilter其他的dimension释放了空间。


## Group (Map-Reduce)
<a name="dimension_group" href="#dimension_group">#</a> dimension<b>.group</b>([<i>groupValue</i>])

为给定的dimension构造一个新的群组，根据指定的<i>groupValue</i>函数，采取dimension的值作为输入值，并返回相应的四舍五入值。这个<i>groupValue</i>是可选的；如果不指定，则默认为<a href="https://en.wikipedia.org/wiki/Identity_function">**恒等函数**</a>。类似的值函数，<i>groupValue</i>必须返回自然有序的值；此外，这种顺序必须符合这个dimension的值函数。
例：
````js
var paymentGroupsByTotal = paymentsByTotal.group(function(total) { return Math.floor(total / 100); });
````

默认情况下，这个group的reduce函数将计算每个组的记录数。此外，这个groups将按照记录数排序。

注意：这个分组和crossfire当前的过滤器相交，**除了相关联的dimension的过滤器**。因此，group方法仅考虑满足除dimension的过滤器之外的每个过滤器的记录。所以，如果付款的ceossfilter是按照type和total过滤，则总额的group只按照type观察过滤器。

<a name="group" href="#group_size">#</a> group<b>.size</b>()

返回group中不同值的数量，无关任何过滤器；基础数据。

<a name="group_reduce" href="#group_reduce">#</a> group<b>.reduce</b>(<i>add, remove, initial</i>)

指定分组的reduce函数，并返回这个group。默认的行为，通过count进行缩减。
例：
````js
//p是上一数据，v是当前数据
function reduceAdd(p, v) {
  return p + 1;
}

function reduceRemove(p, v) {
  return p - 1;
}

function reduceInitial() {
  return 0;
}
````
为了缩减total的总数(**计算total的总和**)，你可以修改add函数和remove函数，如下实施：
````js
function reduceAdd(p, v) {
  return p + v.total;
}

function reduceRemove(p, v) {
  return p - v.total;
}
````
除了add函数以外，remove函数也是需要的，因为group的缩减是逐渐地随着记录被过滤而更新的；在一些情况下，需要从先前计算的group reduction中删除记录。使用许多不同的属性，<a href="https://github.com/square/crossfilter/issues/102#issuecomment-31570749">你可以使用javascript闭包创建add和remove函数</a>。

<a name="group_reduceCount" href="#group_reduceCount">#</a> group<b>.reduceCount</b>()

一个便捷的方法为了统计记录数量的reduce函数，返回这个group。

<a name="group_reduceSum" href="#group_reduceSum">#</a> group<b>.reduceSum</b>(<i>value</i>)

一个便捷的方法使用指定的<i>value</i>访问器计算记录总和的reduce函数，返回这个group。
例：
````js
//按照付款类型，计算付款总额，相同类型的付款值累加
var paymentsByType = payments.dimension(function(d) { return d.type; }),
    paymentVolumeByType = paymentsByType.group().reduceSum(function(d) { return d.total; }),
    topTypes = paymentVolumeByType.top(1);
topTypes[0].key; // the top payment type (e.g., "tab")
topTypes[0].value; // the payment volume for that type (e.g., 920)
````

<a name="group_order" href="#group_order">#</a> group<b>.order</b>(<i>orderValue</i>)

指定<i>orderValue</i>计算前K个组。默认的order函数是<a href="https://en.wikipedia.org/wiki/Identity_function">**恒等函数**</a>，假定reduction值是自然顺序（如简单的计数和款项）。
例：(//TODO 理解不能 - - ！)
````js
function reduceAdd(p, v) {
  ++p.count;
  p.total += v.total;
  return p;
}

function reduceRemove(p, v) {
  --p.count;
  p.total -= v.total;
  return p;
}

function reduceInitial() {
  return {count: 0, total: 0};
}

function orderValue(p) {
  return p.total;
}

var topTotals = paymentVolumeByType.reduce(reduceAdd, reduceRemove, reduceInitial).order(orderValue).top(2);
topTotals[0].key;   // payment type with highest total (e.g., "tab")
topTotals[0].value; // reduced value for that type (e.g., {count:8, total:920})
````
这种技术同样可以计算每个组中特殊值的个数，把计数每一个组缩减的值存储为map，当计数到达零时删除这个map。

<a name="group_orderNatural" href="#group_orderNatural">#</a> group<b>.orderNatural</b>()

使用自然顺序缩减值的便捷方法。返回这个分组。

<a name="group_top" href="#group_top">#</a> group<b>.top</b>(<i>k</i>)

返回一个包含前<i>k</i>个组的新数组，根据被关联缩减值的组顺序。被返回的数组是按照缩减（**统计会不会更容易理解？**）值的降序排列。
例：
````js
//检索付款类型数量最多的一个
var paymentsByType = payments.dimension(function(d) { return d.type; }),
    paymentCountByType = paymentsByType.group(),
    topTypes = paymentCountByType.top(1);
topTypes[0].key; // the top payment type (e.g., "tab")
topTypes[0].value; // the count of payments of that type (e.g., 8)
````
通过crossfilter的过滤器如果group少于<i>k</i>个，就返回一个少于<i>k</i>的数组。如果有一个少于<i>k</i>的非空组，这个方法也有可能返回空的组（零个被选定的记录）。

<a name="group_all" href="#group_all">#</a> group<b>.all</b>()

返回包含所有的组的数组，按照key的升序排列。就像<a href="#group_top">top</a>，被返回的对象包含`key`和`value`两个属性。被返回的数组也有可能包含空的组：group缩减初始化函数返回的值。
例：
````js
//通过类型计数付款
var types = paymentCountByType.all();
````
这个方法比'top(Infinity)'更快，因为整个数组原样被返回，而不是选择新数组和排序。不要修改被返回的数组！

<a name="group_dispose" href="#group_dispose">#</a> group<b>.dispose</b>()

从dimension中删除group。当新的过滤器应用于crossfilter时这个组将不再更新，如果他没有其他引用，则可能被垃圾回收。


## Group All（Reduce）
<a name="dimension_groupAll" href="#dimension_groupAll">#</a> dimension<b>.groupAll</b>()

将所有的记录分组到一个简单的组中的便捷函数。被返回的对象相当于一个标准的<a href="#dimension_group">grouping</a>，除了没有<a href="#group_top">top</a>和<a href="#group_order">order</a>方法。相反，使用<a href="#groupAll_value">value</a>检索符合所有records的缩减值。

<a name="groupAll_reduce" href="#groupAll_reduce">#</a> groupAll<b>.reduce</b>(<i>add, remove, initial</i>)

相当于<a href="#group_reduce">reduce</a>。

<a name="groupAll_value" href="#groupAll_value">#</a> groupAll<b>.value</b>()

相当于<a href="#group_all">all()[0].value</a>。


## Extras
>这部分完全不能理解，正在实际检验学习中。

crossfilter有几个额外的东西，你应该会感觉很有用。

<a name="crossfilter_bisect" href="#crossfilter_bisect">#</a> crossfilter<b>.bisect</b>

恒等平分；适用于数字，字符串，日期和自然可比的对象。

<a name="crossfilter_bisect_by" href="#crossfilter_bisect_by">#</a> crossfilter.bisect<b>.by</b>(<i>value</i>)

使用指定的<i>value</i>访问器构建一个新的平分器，这个访问器必须能返回一个自然有序的值。
例：
````js
//通过属性foo将对象数组对分
var bisectByFoo = crossfilter.bisect.by(function(d) { return d.foo; });
````
返回的对象也是<a href="#bisect_right">bisect.right</a>函数。

<a name="bisect" href="#bisect">#</a> <b>bisect</b>(<i>array, value, lo, hi</i>)<br>
<a name="bisect_right" href="#bisect_right">#</a> bisect<b>.right</b>(<i>array, value, lo, hi</i>)

类似<a href="#bisect_left">bisect.left</a>，但返回一个插入点，在**array**中的任何现有的输入**value**的后边（右边）。


***
参考：<a href="https://github.com/square/crossfilter/wiki/API-Reference">crossfilter API 原文</a>
