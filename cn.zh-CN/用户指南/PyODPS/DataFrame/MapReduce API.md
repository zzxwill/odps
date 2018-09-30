# MapReduce API {#concept_cyr_tfg_cfb .concept}

PyODPS DataFrame也支持MapReduce API，用户可以分别编写map和reduce函数（map\_reduce可以只有mapper或者reducer过程）。

首先我们来看个简单的wordcount的例子。

```
>>> def mapper(row):
>>>     for word in row[0].split():
>>>         yield word.lower(), 1
>>>
>>> def reducer(keys):
>>>     cnt = [0]
>>>     def h(row, done):  # done表示这个key已经迭代结束
>>>         cnt[0] += row[1]
>>>         if done:
>>>             yield keys[0], cnt[0]
>>>     return h
>>>
>>> words_df.map_reduce(mapper, reducer, group=['word', ],
>>>                     mapper_output_names=['word', 'cnt'],
>>>                     mapper_output_types=['string', 'int'],
>>>                     reducer_output_names=['word', 'cnt'],
>>>                     reducer_output_types=['string', 'int'])
     word  cnt
0   hello    2
1       i    1
2      is    1
3    life    1
4  python    2
5   short    1
6     use    1
7   world    1
```

group参数用来指定reduce按哪些字段做分组，如果不指定，会按全部字段做分组。

其中对于reducer来说，会稍微有些不同。它需要接收聚合的keys初始化，并能继续处理按这些keys聚合的每行数据。 第2个参数表示这些keys相关的所有行是不是都迭代完成。

这里写成函数闭包的方式，主要为了方便，当然我们也能写成callable的类。

```
class reducer(object):
    def __init__(self, keys):
        self.cnt = 0

    def __call__(self, row, done):  # done表示这个key已经迭代结束
        self.cnt += row.cnt
        if done:
            yield row.word, self.cnt
```

使用 `output`来注释会让代码更简单些。

```
>>> from odps.df import output
>>>
>>> @output(['word', 'cnt'], ['string', 'int'])
>>> def mapper(row):
>>>     for word in row[0].split():
>>>         yield word.lower(), 1
>>>
>>> @output(['word', 'cnt'], ['string', 'int'])
>>> def reducer(keys):
>>>     cnt = [0]
>>>     def h(row, done):  # done表示这个key已经迭代结束
>>>         cnt[0] += row.cnt
>>>         if done:
>>>             yield keys.word, cnt[0]
>>>     return h
>>>
>>> words_df.map_reduce(mapper, reducer, group='word')
     word  cnt
0   hello    2
1       i    1
2      is    1
3    life    1
4  python    2
5   short    1
6     use    1
7   world    1
```

有时候我们在迭代的时候需要按某些列排序，则可以使用 `sort`参数，来指定按哪些列排序，升序降序则通过 `ascending`参数指定。 `ascending` 参数可以是一个bool值，表示所有的 `sort`字段是相同升序或降序， 也可以是一个列表，长度必须和 `sort`字段长度相同。

## 指定combiner {#section_lhr_f5n_cfb .section}

combiner表示在map\_reduce API里表示在mapper端，就先对数据进行聚合操作，它的用法和reducer是完全一致的，但不能引用资源。 并且，combiner的输出的字段名和字段类型必须和mapper完全一致。

上面的例子，我们就可以使用reducer作为combiner来先在mapper端对数据做初步的聚合，减少shuffle出去的数据量。

```
>>> words_df.map_reduce(mapper, reducer, combiner=reducer, group='word')
```

## 引用资源 {#section_h22_l5n_cfb .section}

在MapReduce API里，我们能分别指定mapper和reducer所要引用的资源。

如下面的例子，我们对mapper里的单词做停词过滤，在reducer里对白名单的单词数量加5。

```
>>> white_list_file = o.create_resource('pyodps_white_list_words', 'file', file_obj='Python\nWorld')
>>>
>>> @output(['word', 'cnt'], ['string', 'int'])
>>> def mapper(resources):
>>>     stop_words = set(r[0].strip() for r in resources[0])
>>>     def h(row):
>>>         for word in row[0].split():
>>>             if word not in stop_words:
>>>                 yield word, 1
>>>     return h
>>>
>>> @output(['word', 'cnt'], ['string', 'int'])
>>> def reducer(resources):
>>>     d = dict()
>>>     d['white_list'] = set(word.strip() for word in resources[0])
>>>     d['cnt'] = 0
>>>     def inner(keys):
>>>         d['cnt'] = 0
>>>         def h(row, done):
>>>             d['cnt'] += row.cnt
>>>             if done:
>>>                 if row.word in d['white_list']:
>>>                     d['cnt'] += 5
>>>                 yield keys.word, d['cnt']
>>>         return h
>>>     return inner
>>>
>>> words_df.map_reduce(mapper, reducer, group='word',
>>>                     mapper_resources=[stop_words], reducer_resources=[white_list_file])
     word  cnt
0   hello    2
1    life    1
2  python    7
3   world    6
4   short    1
5     use    1
```

## 使用第三方Python库 {#section_np5_n5n_cfb .section}

使用方法类似 [map中使用第三方Python库](cn.zh-CN/用户指南/PyODPS/DataFrame/使用自定义函数.md#) 。

可以在全局指定使用的库：

```
>>> from odps import options
>>> options.df.libraries = ['six.whl', 'python_dateutil.whl']
```

或者在立即执行的方法中，局部指定：

```
>>> df.map_reduce(mapper=my_mapper, reducer=my_reducer, group='key').execute(libraries=['six.whl', 'python_dateutil.whl'])
```

**说明：** 由于字节码定义的差异，Python 3 下使用新语言特性（例如 `yield from`）时，代码在使用 Python 2.7 的 ODPS Worker 上执行时会发生错误。因而建议在 Python 3 下使用 MapReduce API 编写生产作业前，先确认相关代码是否能正常 执行。

## 重排数据 {#section_oss_2vn_cfb .section}

有时候我们的数据在集群上分布可能是不均匀的，我们需要对数据重排。调用 `reshuffle`接口即可。

```
>>> df1 = df.reshuffle()
```

默认会按随机数做哈希来分布。也可以指定按那些列做分布，且可以指定重排后的排序顺序。

```
>>> df1.reshuffle('name', sort='id', ascending=False)
```

## 布隆过滤器 {#section_pgn_3vn_cfb .section}

PyODPS DataFrame提供了 `bloom_filter` 接口来进行布隆过滤器的计算。

给定某个collection，和它的某个列计算的sequence1，我们能对另外一个sequence2进行布隆过滤，sequence1不在sequence2中的一定会过滤， 但可能不能完全过滤掉不存在于sequence2中的数据，这也是一种近似的方法。

这样的好处是能快速对collection进行快速过滤一些无用数据。

这在大规模join的时候，一边数据量远大过另一边数据，而大部分并不会join上的场景很有用。 比如，我们在join用户的浏览数据和交易数据时，用户的浏览大部分不会带来交易，我们可以利用交易数据先对浏览数据进行布隆过滤， 然后再join能很好提升性能。

```
>>> df1 = DataFrame(pd.DataFrame({'a': ['name1', 'name2', 'name3', 'name1'], 'b': [1, 2, 3, 4]}))
>>> df1
       a  b
0  name1  1
1  name2  2
2  name3  3
3  name1  4
>>> df2 = DataFrame(pd.DataFrame({'a': ['name1']}))
>>> df2
       a
0  name1
>>> df1.bloom_filter('a', df2.a) # 这里第0个参数可以是个计算表达式如: df1.a + '1'
       a  b
0  name1  1
1  name1  4
```

这里由于数据量很小，df1中的a为name2和name3的行都被正确过滤掉了，当数据量很大的时候，可能会有一定的数据不能被过滤。

如之前提的join场景中，少量不能过滤并不能并不会影响正确性，但能较大提升join的性能。

我们可以传入 `capacity` 和 `error_rate` 来设置数据的量以及错误率，默认值是 `3000` 和 `0.01`。

**说明：** 要注意，调大 `capacity` 或者减小 `error_rate`会增加内存的使用，所以应当根据实际情况选择一个合理的值。

Collection对象操作请参见[DataFrame执行](cn.zh-CN/用户指南/PyODPS/DataFrame/执行.md#)。

## 透视表（pivot\_table） {#section_gmz_jwn_cfb .section}

PyODPS DataFrame提供透视表的功能。我们通过几个例子来看使用。

```
>>> df
     A    B      C  D  E
0  foo  one  small  1  3
1  foo  one  large  2  4
2  foo  one  large  2  5
3  foo  two  small  3  6
4  foo  two  small  3  4
5  bar  one  large  4  5
6  bar  one  small  5  3
7  bar  two  small  6  2
8  bar  two  large  7  1
```

最简单的透视表必须提供一个`rows` 参数，表示按一个或者多个字段做取平均值的操作。

```
>>> df['A', 'D', 'E'].pivot_table(rows='A')
     A  D_mean  E_mean
0  bar     5.5    2.75
1  foo     2.2    4.40
```

rows可以提供多个，表示按多个字段做聚合。

```
>>> df.pivot_table(rows=['A', 'B', 'C'])
     A    B      C  D_mean  E_mean
0  bar  one  large     4.0     5.0
1  bar  one  small     5.0     3.0
2  bar  two  large     7.0     1.0
3  bar  two  small     6.0     2.0
4  foo  one  large     2.0     4.5
5  foo  one  small     1.0     3.0
6  foo  two  small     3.0     5.0
```

我们可以指定 `values`来显示指定要计算的列。

```
>>> df.pivot_table(rows=['A', 'B'], values='D')
     A    B    D_mean
0  bar  one  4.500000
1  bar  two  6.500000
2  foo  one  1.666667
3  foo  two  3.000000
```

计算值列时，默认会计算平均值，用户可以指定一个或者多个聚合函数。

```
>>> df.pivot_table(rows=['A', 'B'], values=['D'], aggfunc=['mean', 'count', 'sum'])
     A    B    D_mean  D_count  D_sum
0  bar  one  4.500000        2      9
1  bar  two  6.500000        2     13
2  foo  one  1.666667        3      5
3  foo  two  3.000000        2      6
```

我们也可以把原始数据的某一列的值，作为新的collection的列。 这也是透视表最强大的地方。

```
>>> df.pivot_table(rows=['A', 'B'], values='D', columns='C')
     A    B  large_D_mean  small_D_mean
0  bar  one           4.0           5.0
1  bar  two           7.0           6.0
2  foo  one           2.0           1.0
3  foo  two           NaN           3.0
```

我们可以提供 `fill_value` 来填充空值。

```
>>> df.pivot_table(rows=['A', 'B'], values='D', columns='C', fill_value=0)
     A    B  large_D_mean  small_D_mean
0  bar  one             4             5
1  bar  two             7             6
2  foo  one             2             1
3  foo  two             0             3
```

## Key-Value 字符串转换 {#section_nq4_ywn_cfb .section}

DataFrame 提供了将 Key-Value 对展开为列，以及将普通列转换为 Key-Value 列的功能。

我们的数据为

```
>>> df
    name               kv
0  name1  k1=1,k2=3,k5=10
1  name1    k1=7.1,k7=8.2
2  name2    k2=1.2,k3=1.5
3  name2      k9=1.1,k2=1
```

可以通过 extract\_kv 方法将 Key-Value 字段展开：

```
>>> df.extract_kv(columns=['kv'], kv_delim='=', item_delim=',')
   name   kv_k1  kv_k2  kv_k3  kv_k5  kv_k7  kv_k9
0  name1    1.0    3.0    NaN   10.0    NaN    NaN
1  name1    7.0    NaN    NaN    NaN    8.2    NaN
2  name2    NaN    1.2    1.5    NaN    NaN    NaN
3  name2    NaN    1.0    NaN    NaN    NaN    1.1
```

其中，需要展开的字段名由 columns 指定，Key 和 Value 之间的分隔符，以及 Key-Value 对之间的分隔符分别由 kv\_delim 和 item\_delim 这两个参数指定，默认分别为半角冒号和半角逗号。输出的字段名为原字段名和 Key 值的组合，通过“\_”相连。缺失值默认为 None，可通过 `fill_value`选择需要填充的值。例如，相同的 df，

```
>>> df.extract_kv(columns=['kv'], kv_delim='=', fill_value=0)
   name   kv_k1  kv_k2  kv_k3  kv_k5  kv_k7  kv_k9
0  name1    1.0    3.0    0.0   10.0    0.0    0.0
1  name1    7.0    0.0    0.0    0.0    8.2    0.0
2  name2    0.0    1.2    1.5    0.0    0.0    0.0
3  name2    0.0    1.0    0.0    0.0    0.0    1.1
```

DataFrame 也支持将多列数据转换为一个 Key-Value 列。例如，

```
>>> df
   name    k1   k2   k3    k5   k7   k9
0  name1  1.0  3.0  NaN  10.0  NaN  NaN
1  name1  7.0  NaN  NaN   NaN  8.2  NaN
2  name2  NaN  1.2  1.5   NaN  NaN  NaN
3  name2  NaN  1.0  NaN   NaN  NaN  1.1
```

可通过 to\_kv 方法转换为 Key-Value 表示的格式：

```
>>> df.to_kv(columns=['k1', 'k2', 'k3', 'k5', 'k7', 'k9'], kv_delim='=')
    name               kv
0  name1  k1=1,k2=3,k5=10
1  name1    k1=7.1,k7=8.2
2  name2    k2=1.2,k3=1.5
3  name2      k9=1.1,k2=1
```

DataFrame创建及其对象操作请参见[创建 DataFrame](cn.zh-CN/用户指南/PyODPS/DataFrame/创建 DataFrame.md#)。

