# Python UDF {#concept_pjv_qs2_xdb .concept}

The MaxCompute UDF consists of UDF, UDAF, and UDTF functions. This article explains how to implement these three functions through Python.

## RESTRICTED ENVIRONMENT {#section_gwg_3ff_xdb .section}

The Python version of MaxCompute UDF is 2.7 and executes user code in sandbox mode; that is, the code is executed in a restricted environment.

-   Read and Write local files
-   Promoter process
-   Start thread
-   Use SOCKET to communicate
-   Other system calls

Because of these restrictions, user-uploaded code must be implemented throughj pure Python, and the C extension module is disabled.

In addition, not all modules are available in the Python standard library, and modules that involve these features are disabled. Description of available modules in the standard library are as follows:

-   All modules implemented by pure Python are available.
-   The following modules are available in C-implemented extended modules.
    -   array
    -   audioop
    -   binascii
    -   \_bisect
    -   cmath
    -   \_codecs\_cn
    -   \_codecs\_hk
    -   \_codecs\_iso2022
    -   \_codecs\_jp
    -   \_codecs\_kr
    -   \_codecs\_tw
    -   \_collections
    -   cStringIO
    -   datetime
    -   \_functools
    -   future\_builtins
    -   \_hashlib
    -   \_heapq
    -   itertools
    -   \_json
    -   \_locale
    -   \_lsprof
    -   math
    -   \_md5
    -   \_multibytecodec
    -   operator
    -   \_random
    -   \_sha256
    -   \_sha512
    -   \_sha
    -   \_struct
    -   strop
    -   time
    -   unicodedat
    -   \_weakref
    -   cPickle
-   Some modules have limited functionalities. For example, the sandbox limits the degree to which user code can write data to the standard output and the standard error output; that is,  `sys.stdout/sys.stderr`  can write 20 KB at most; otherwise, the excessive characters will be ignored.

## Third-party Libraries {#section_lrw_zff_xdb .section}

Common third-party libraries are installed in the operating environment to supplement the standard library. The supported third-party libraries also include **numpy**.

**Note:** The use of third-party libraries is also subject to 'prohibit local', 'network I/O', and other restrictions. Therefore, APIs that have such functions are also prohibited in a third-party library.

## Parameters and return value types {#section_mrj_cgf_xdb .section}

The parameters and return values are specified as follows:

```
@odps.udf.annotate(signature)
```

MaxCompute SQL data types that are currently supported by the Python UDF include bigint, String, double, Boolean, and datetime. The SQL statement must determine the parameter type and the return value type for all functions before execution. So for Python, a dynamically-typed language, you must specify the function signature by adding a decorator to the UDF class.

The function signature is specified by a string. The syntax is as follows:

```
arg_type_list '->' type_list
 arg_type_list: type_list | '*' | ''
type_list: [type_list ','] type
'bigint' | 'string' | 'double' | 'boolean' | 'datetime'
```

-   The left side of the arrow indicates the type of the parameter and the right side indicates the type of the returned value.
-   Only the UDTF returned value can be multiple columns, while UDF and UDAF can only return one column.
-   ‘\*’ represents varargs. By using varargs, UDF/UDTF/UDAF can match any type of parameter.

A valid signature example is as follows:

```
The 'bigint, double-> string' # parameter is bigint, double, and the return value is string

The 'bigint, boolean-> string, datetime '# udtf parameter is bigint, Boolean, the return value is string, datetime

'*->string' #  variable length parameter, input parameter arbitrary, return value string

The '-> doubles' # parameter is empty and the return value is double
```

At the query semantic parsing stage, unqualified signatures are removed, and an error is returned. The execution is then stopped. During execution, the UDF parameter will be passed to the user as the type specified by the function signature. The type of the user returned value must be consistent with the type specified by the function signature; otherwise, an error is returned. MaxCompute SQL data type corresponds to the Python type as follows:

|ODPS SQL type|Bigint|String|Double|Boolean |Datetime|
|:------------|:-----|:-----|:-----|:-------|:-------|
|Python Type|int|str|float|bool|int|

**Note:** 

-   Datetime type is passed to user code in the form of an int, with a value of epoch UTC Number of milliseconds from time to date. The user can deal with ‘datetime’ type through the ‘datetime’ module in the Python standard library.
-   NULL corresponds to NONE in Python.

In addition, the parameter of odps.udf.int\(value\[, silent=True\]\) has been adjusted. Parameter ‘silent’ is added. . When ‘silent’ is true, if the value cannot be converted into ‘int’, report no error and return NONE.

## UDF {#section_if4_fhf_xdb .section}

Implementation of the Python UDF is very simple. You are required to define a new-style  class, and implements the evaluate method. For example:

```
 from odps.udf import annotate

 @annotate("bigint,bigint->bigint")
class myplus (object ):

   def evaluate (self, arg0, arg1 ):
       If none in (arg0, arg1 ):
           return none
       return arg0 + arg1
```

**Note:** A Python UDF must have its signature specified through annotate.

Python UDF permissions on the public cloud need to be submitted for work order application, which can be filled out as prompted.

## UDAF {#section_ilj_mhf_xdb .section}

-   class odps.udf.BaseUDAF: Inherit this class to implement a Python UDAF.
-   BaseUDAF.new\_buffer\(\): Implement this method and return the median ‘buffer’ of the aggregate function. Buffer must be mutable Object \(such as list, dict\), and the size of the buffer must not increase with the amount of data, in case of limit, Buffer size after Marshal must not exceed 2 MB.
-   BaseUDAF.iterate\(buffer\[, args, ...\]\): This method aggregates ‘args’ into the median ‘buffer’.
-   BaseUDAF.merge\(buffer, pbuffer\): This method aggregates two median buffers; that is, aggregate ‘pbuffer merger’ into ‘buffer’.
-   BaseUDAF.terminate\(buffer\): This method converts the median ‘buffer’ into the MaxCompute SQL basic types.

An example of an average value of UDAF is as follows:

```
@annotate('double->double')
 
class Average(BaseUDAF):

    def new_buffer(self):
        return [0, 0]

    def iterate(self, buffer, number):
        If number is not None:
            buffer[0] += number
            buffer[1] += 1

    def merge(self, buffer, pbuffer):
        buffer [0] + = pbuffer [0]
        buffer [1] + = pbuffer [1]

    def terminate (self, buffer ):
        If buffer [1] = 0:
            return 0.0
        return buffer[0] / buffer[1]
```

## UDTF {#section_ipk_thf_xdb .section}

-   class odps.udf.BaseUDTF: The basic class of Python UDTF. Users inherit this class and implement methods such as process, close, and so on.
-   BaseUDTF.\_\_init\_\_\(\): The initialization method, the inheritance class, if you implement this method, the base class's initialization method, super\(BaseUDTF, self\).\_\_init\_\_\(\)  must be called in the beginning.

    The init method can only be called once during the entire UDTF life cycle; that is, before the first record is processed. When the UDTF must save the internal state, all states can be initialized in this method.

-   BaseUDTF. process \(\[args,...\]\): This is one of the MaxCompute methods. The framework calls this method. Each record in SQL calls ‘process’ once accordingly. The parameters of ‘process’ are the specified UDTF input parameters in SQL.
-   BaseUDTF.forward\(\[args, ...\]\): The UDTF output method, which is called by user codes. Each time ‘forward’ is called, a record is output. The parameters of ‘forward’ are the UDTF output parameters specified in SQL.
-   BaseUDTF.close\(\): The termination method of UDTF. This method is called by the MaxCompute SQL framework and only to be called once; that is, after processing the last record.

Examples of UDTF are:

```
#coding:utf-8
# explode. py

 
from odps.udf import annotate
 
from odps.udf import BaseUDTF


 
@annotate('string -> string')
class Explode(BaseUDTF):
   """Output string comma-separated to multiple records
   """

   def process(self, arg):
       props = arg.split(',')
        for p in props:
           self.forward(p)
```

**Note:** A Python UDTF can also specify the parameter type or returned value type without adding ‘annotate’. In this case, the function can match any input parameter in SQL. The returned value type cannot be deduced, but all output parameters will be considered to be ‘String’ type. So when ‘forward’ is called, all output values must be converted into ‘str’ type.

## Referring to resources {#section_bj3_h3f_xdb .section}

Python UDF can reference resource files through the ‘odps.distcache’ module. Currently, referencing file resources and table resources are supported.

-   odps.distcache.get\_cache\_file\(resource\_name\)

    -   Returns the resource content for the specified name. resource\_name: ‘str’ type, corresponding to the existing resource name in the current project. If the resource name is invalid or has no responding resources, returns an error.
    -   The return value is file-like object the caller must call the close method to release the open resource file after this object has been used.
    The example of using ‘get\_cache\_file’ is as follows:

    ```
     @annotate('bigint->string')
     
    class DistCacheExample(object):
    
     def __init__(self):
        cache_file = get_cache_file('test_distcache.txt')
         kv = {}
        for line in cache_file:
            line = line. strip ()
            If not line:
                continue
            k, v = line.split()
            kv[int(k)] = v
        cache_file.close ()
        self.kv = kv
    
    def evaluate(self, arg):
        return self.kv.get(arg)
    ```

-   odps.distcache.get\_cache\_table\(resource\_name\):

    -   Returns the contents of the specified resource table. resource\_name: ‘str’ type, corresponding to the existing resource table name in the current project. If the resource name is invalid or has no responding resources, returns an error.
    -   Returned value: Returned value is a ‘generator’ type. The caller obtains the table content through traversal. Each traversal has a record stored in the table in the form of a tuple.
    The example of using ‘get\_cache\_table’ is as follows:

    ```
     from odps.udf import annotate
    from odps.distcache import get_cache_table
    
    @ attenuate ('-> string ')
    class DistCacheTableExample(object):
        def __init__(self):-
             self.records = list(get_cache_table('udf_test'))
            self.counter = 0
            self.ln = len(self.records)
    
        def evaluate(self):
            if self.counter > self.ln - 1:
                 return None
            ret = self.records[self.counter]
            self.counter += 1
            return str(ret)
    ```


