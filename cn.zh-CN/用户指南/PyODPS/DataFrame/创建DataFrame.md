# 创建DataFrame {#concept_nfg_tr4_cfb .concept}

在使用DataFrame时，你需要了解三个对象上的操作：`Collection`\(`DataFrame`\) ，`Sequence`，`Scalar`。 这三个对象分别表示表结构（或者二维结构）、列（一维结构）、标量。需要注意的是，这些对象仅在使用Pandas数据创建后会包含实际数据， 而在ODPS表上创建的对象中并不包含实际的数据，而仅仅包含对这些数据的操作，实质的存储和计算会在 ODPS中进行。

## 创建DataFrame {#section_e1j_bs4_cfb .section}

通常情况下，你唯一需要直接创建的Collection对象是 [DataFrame Reference](cn.zh-CN/用户指南/PyODPS/API Reference/API概述.md#)，这一对象用于引用数据源，可能是一个ODPS表， ODPS分区，Pandas DataFrame或sqlalchemy.Table（数据库表）。 使用这几种数据源时，相关的操作相同，这意味着你可以不更改数据处理的代码，仅仅修改输入/输出的指向， 便可以简单地将小数据量上本地测试运行的代码迁移到ODPS上，而迁移的正确性由PyODPS来保证。

创建DataFrame非常简单，只需将Table对象、 pandas DataFrame对象或者sqlalchemy Table 对象传入即可。

```
from odps.df import DataFrame

# 从ODPS表创建
 iris = DataFrame(o.get_table('pyodps_iris'))
 iris2 = o.get_table('pyodps_iris').to_df()  # 使用表的to_df方法

# 从ODPS分区创建
 pt_df = DataFrame(o.get_table('partitioned_table').get_partition('pt=20171111'))
 pt_df2 = o.get_table('partitioned_table').get_partition('pt=20171111').to_df()  # 使用分区的to_df方法

# 从Pandas DataFrame创建
 import pandas as pd
 import numpy as np
 df = DataFrame(pd.DataFrame(np.arange(9).reshape(3, 3), columns=list('abc')))

# 从sqlalchemy Table创建
 engine = sqlalchemy.create_engine('mysql://root:123456@localhost/movielens')
 metadata = sqlalchemy.MetaData(bind=engine) # 需要绑定到engine
 table = sqlalchemy.Table('top_users', metadata, extend_existing=True, autoload=True)
 users = DataFrame(table)
```

在用pandas DataFrame初始化时，对于numpy object类型或者string类型，PyODPS DataFrame会尝试推断类型， 如果一整列都为空，则会报错。这时，用户可以指定

unknown\_as\_string

为True，会将这些列指定为string类型。 用户也可以指定as\_type参数。若类型为基本类型，会在创建PyODPS DataFrame时进行强制类型转换。 如果Pandas DataFrame中包含 list或者dict列，该列的类型不会被推断，必须手动使用as\_type指定。 as\_type参数类型必须是dict。

```
 df2 = DataFrame(df, unknown_as_string=True, as_type={'null_col2': 'float'})
df2.dtypes
odps.Schema {
  sepallength           float64
  sepalwidth            float64
  petallength           float64
  petalwidth            float64
  name                  string
  null_col1             string   # 无法识别，通过unknown_as_string设置成string类型
  null_col2             float64  # 强制转换成float类型
}
 df4 = DataFrame(df3, as_type={'list_col': 'list<int64>'})
 df4.dtypes
odps.Schema {
  id        int64
  list_col  list<int64>  # 无法识别且无法自动转换，通过as_type设置
}
```

