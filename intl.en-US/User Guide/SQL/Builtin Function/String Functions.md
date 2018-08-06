# String Functions {#concept_ulf_pfm_vdb .concept}

## CHAR\_MATCHCOUNT {#section_mnd_gvz_vdb .section}

The command format is as follows:

```
bigint char_matchcount(string str1, string str2)
```

Usage:

It is used to calculate total times for which each character in str1 appears in str2.

Parameter description:

-   str1, str2: String type, must be effective UTF-8 strings. If invalid character is in matching process, return a negative value.
-   Return value: Bigint type,  Any NULL input, return NULL.

Examples:

```
char_matchcount('abd','aabc') = 2
-- Two strings 'a', 'b' in str1 appear in str2.
```

## CHR {#section_s5r_lwz_vdb .section}

The command format is as follows:

```
string chr(bigint ascii)
```

Usage:

Convert the specified ASCII code ‘ascii’ into character.

Parameter description:

-   ascii: Bigint type ASCII value. If the input is ‘string’ or ‘double’, it is converted to ‘bigint’ by implicit conversion. If the input is other types, an error is thrown.
-   Return value: String type. The parameter value range is 0~255. An exception indicated if exceeding this range. If the input is NULL, return NULL.

## CONCAT {#section_xxj_wwz_vdb .section}

The command format is as follows:

```
string concat(string a, string b...)
```

Usage:

The return value is a result after connecting all strings.

Parameter description:

-   a, b… String type. If the input is Bigint, Double, Decimal or Datetime, it is converted to String by implicit conversion. If the input is other types, an error is thrown.
-   String: Return value: String type. If no parameter exists or a certain parameter is NULL, return NULL.

Examples:

```
concat('ab','c') = 'abc'
concat() = NULL
concat('a', null, 'b') = NULL
```

## GET\_JSON\_OBJECT {#section_cdt_gxz_vdb .section}

The command format is as follows:

```
STRING GET_JSON_OBJECT(STRING json,STRING path)
```

Usage:

In a standard json string, the specified string is extracted according to path.

Parameter description:

-   json: String type, standard json format string.
-   path: **String type, describing the path in json, starting with ‘$’**.  For a description of the [JsonPath](http://goessner.net/articles/JsonPath/index.html#e2) in the new implementation, see: jsonpath, $ for the root node, \(.\) Represents child, \[number\] represents the subscript of an array, for an array, the format is key \[sub1\] \[sub2\] \[sub3\]..., \[\*\] Returns the entire array, \* escape is not supported.
-   String: Returns string type.

**Note:** 

-   Return NULL if json is null or invalid json format.
-   Return NULL if path is null or invalid \(does not exist in json\).
-   If json is legal and path also exists, the corresponding string is returned.

Example one is as follows:

```
+----+
json
+----+
{"store":
{"fruit":[{"weight":8,"type":"apple"},{"weight":9,"type":"pear"}],
"bicycle":{"price":19.95,"color":"red"}
},
"email":"amy@only_for_json_udf_test.net",
"owner":"amy"
}
```

You can extract information in the JSON object through the following query process:

```
odps> SELECT get_json_object(src_json.json, '$.owner') FROM src_json;
amy
odps> SELECT get_json_object(src_json.json, '$.store.fruit\[0]') FROM src_json;
{"weight":8,"type":"apple"} 
odps> SELECT get_json_object(src_json.json, '$.non_exist_key') FROM src_json;
NULL
```

Example two is as follows:

```
get_json_object('{"array":[["aaaa",1111],["bbbb",2222],["cccc",3333]]}','$.array[1][1]')= "2222"
get_json_object('{"aaa":"bbb","ccc":{"ddd":"eee","fff":"ggg","hhh":["h0","h1","h2"]},"iii":"jjj"}','$.ccc.hhh[*]') = "["h0","h1","h2"]"
get_json_object('{"aaa":"bbb","ccc":{"ddd":"eee","fff":"ggg","hhh":["h0","h1","h2"]},"iii":"jjj"}','$.ccc.hhh[1]') = "h1"
```

## INSTR {#section_vft_yxz_vdb .section}

The command format is as follows:

```
bigint instr(string str1, string str2[, bigint start_position[, bigint nth_appearance]])
```

Usage:

Calculate the position that a substring str2 is located in str1.

Parameter description:

-   str1: String type. If the input is Bigint, Double, Decimal or Datetime, it is converted to String by implicit conversion. If the input is other types, an error is thrown.
-   str2: String type. If the input is Bigint, Double, Decimal or Datetime, it is converted to String by implicit conversion. If the input is other types, an error is thrown.
-   start\_position: Bigint type, for other types, an exception is indicated. It indicates which character of str1 is searched from and the default staring position is the first character position 1. If it is less than 0, it causes abnormality.
-   nth\_appearance：bigint type, greater than 0, represents the position of the second match of the substring in the string, if the chain is of a different type or less than or equal to 0, an exception is thrown.
-   Return value: Bigint type.

**Note:** 

-   If str2 is not found in str1, return 0.
-   If any input parameter is null, return null.
-   If str2 is NULL and always can be matched successfully, so instr \(‘abc’, ‘’\) returns 1.

Examples:

```
instr('Tech on the net', 'e') = 2
instr('Tech on the net', 'e', 1, 1) = 2
instr('Tech on the net', 'e', 1, 2) = 11
instr('Tech on the net', 'e', 1, 3) = 14
```

## IS\_ENCODING {#section_qdj_kyz_vdb .section}

The command format is as follows:

```
boolean is_encoding(string str, string from_encoding, string to_encoding)
```

Usage:

Judge whether the input string ‘str’ can be changed into a character set ‘to\_encoding’ from a specified character set ‘from\_encoding’. It can be used to judge whether the input is garbled. The common use is to set ‘from\_encoding’ to be ‘utf-8’ and ‘to\_encoding’ to be ‘gbk’.

Parameter description:

-   str: String type, if the input is NULL, return NULL. The empty string can be considered belong to any character set.
-   from\_encoding, to\_encoding: String type, source, destination character sets.  If the input is NULL, return NULL.
-   Return value: Boolean type. If ‘str’ can be converted successfully, return true, otherwise, return false.

Examples:

```
is_encoding('测试', 'utf-8', 'gbk') = true
is_encoding('測試', 'utf-8', 'gbk') = true
-- These two traditional Chinese characters are in GBK stock.
is_encoding('測試', 'utf-8', 'gb2312') = false
-- The grapheme inventory of ‘GB2312’ does not contain these two Chinese characters.
```

## KEYVALUE {#section_lnq_tyz_vdb .section}

The command format is as follows:

```
KEYVALUE(STRING srcStr,STRING split1,STRING split2, STRING key)
KEYVALUE(STRING srcStr,STRING key) //split1 = ";"，split2 = ":"
```

Usage:

split ‘srcStr’ into ‘key-value’ pairs by split1 and separate ‘key-value’ pairs by split2. Return the value corresponding to key.

Parameter description:

-   srcStr: source string to be split.
-   key: specified to return the nth string. After the source string is split by ‘split1’ and ‘split2’, return the corresponding value according to the specification of the ‘key’ value.
-   split1, split2: strings used as delimiters by which ‘srcStr’ is split. If these two parameters are not specified in the expression, the default value of ‘split1’ is ’;’ and that of ‘split2’ is ’:’. If a string that has been split by split1 has multiple split2, the return result is not defined.

Return value:

-   String type.
-   If ‘split1’ or ‘split2’ is NULL, return NULL.
-   If ‘scrStr’ and ‘key’ are NULL or if there is no matched ‘key’, return NULL.
-   If multiple ‘key-value’ matches, return the value corresponding to the first matched key.

Example 1:

```
keyvalue('0:1\;1:2', 1) = '2'

```

**Note:** The source string is "0:1\\;1:2". Due to split1 and split2 are not specified, the default split1 is ";" and split2 is ":". 

After the split1 split, the key-value pair is 0:1\\,1:2.

After split2 split, it becomes:

```
0 1/  
1 2
```

Returns the value\(2\) of the key corresponding to 1.

Example 2:

```
keyvalue("\;decreaseStore:1\;xcard:1\;isB2C:1\;tf:21910\;cart:1\;shipping:2\;pf:0\;market:shoes\;instPayAmount:0\;","\;",":","tf") = "21910" value:21910.
```

**Note:** The source string is as follows:

```
“\;decreaseStore:1\;xcard:1\;isB2C:1\;tf:21910\;cart:1\;shipping:2\;pf:0\;market:shoes\;instPayAmount:0\;”
```

The key-value pairs derived from the split after splitting according to the split1 '\\;' are as follows:

```
decreaseStore:1，xcard:1，isB2C:1，tf:21910，cart:1，shipping:2，pf:0，market:shoes，instPayAmount:0 
```

After you split, follow the split2 ":", the results are as follows:

```
decreaseStore 1  
 xcard 1  
 isB2C 1  
 tf 21910  
 cart 1  
 shipping 2  
 pf 0  
 market shoes  
 instPayAmount 0
```

For the key parameter whose value is "tf", the return value of the corresponding value parameter is 21910.

## LENGTH {#section_ewt_jzz_vdb .section}

The command format is as follows:

```
bigint length(string str)
```

Usage:

Return the length of a string.

Parameter description:

-   str: String type. If the input is Bigint，Double，Decimal or Datetime, it is converted to String by implicit conversion. If the input is other types, an error is thrown.
-   Return value: Bigint type. If ‘str’ is NULL, return NULL. If ‘str’ is non UTF-8 coding format, return -1.

Examples:

```
length('hi! China') = 6
```

## LENGTHB {#section_o3y_pzz_vdb .section}

The command format is as follows:

```
bigint lengthb(string str)
```

Usage:

Return the length of ‘str’, which unit is byte.

Parameter description:

-   str: String type. If the input is Bigint，Double，Decimal or Datetime, it is converted to String by implicit conversion. If the input is other types, an error is thrown.
-   Return value: Bigint type. If ‘str’ is NULL, return NULL.

Examples:

```
lengthb('hi! 中国') = 10
```

## MD5 {#section_hbw_xzz_vdb .section}

The command format is as follows:

```
string md5(string value)
```

Usage:

Calculate the md5 value of input string.

Parameter description:

-   value: String type. If the input value is of the Bigint, Double, Decimal or Datetime type, it is implicitly converted to the String type before calculation. If the input value is of another type, an exception is thrown. If the input is NULL, return NULL.
-   Return value: String type.

## REGEXP\_EXTRACT {#section_ms1_lc1_wdb .section}

The command format is as follows:

```
string regexp_extract(string source, string pattern[, bigint occurrence])
```

Usage:

Split the string source according to pattern \(regular expression rules\), and return the characters of the occurrence\(nth\) group.

Parameter description:

-   source: String type, a string to be searched.
-   pattern: A string type constant. If pattern is a null string, an exception is indicated. If ‘group’ is not specified in pattern, an exception is also indicated.
-   Occurrence: A bigint type constant, must be greater than 0 or equal to 0. If it is other type or less than 0, an exception is indicated. If not specified, the default value is 1, which indicates returning the first group. If ‘occurrence’ is equal to 0, then return substrings that satisfy the entire ‘pattern’.
-   Return value: String type. Any input is NULL, return NULL.

Examples:

```
regexp_extract('foothebar', 'foo(. *?)( bar)', 1) = the
regexp_extract('foothebar', 'foo(. *?)( bar)', 2) = bar
regexp_extract('foothebar', 'foo(. *?)( bar)', 0) = foothebar
regexp_extract('8d99d8', '8d(\\d+)d8') = 99
-- If regular SQL is submitted on MaxCompute, two "\" must be used as the shift character.
regexp_extract('foothebar', 'foothebar')
-- The exception is thrown. ‘group’ is not specified in ‘pattern’.
```

## REGEXP\_INSTR {#section_jpn_5c1_wdb .section}

**Function definition:**

```
bigint regexp_instr(string source, string pattern[,
bigint start_position[, bigint nth_occurrence[, bigint return_option]])
```

Usage:

Return the start position/end position of the substring, which is matched with source from start\_position and nth\_occurrence with pattern. Any input parameter is null, return null.

Parameter description:

-   source: String type, to be searched.
-   pattern: A string type constant. If ‘pattern’ is null, an an exception is indicated.
-   start\_position: Bigint type constant, the start position of search. If it is not specified, default value is 1. If it is other type or a value which is less than or equal to 0, an exception is also indicated.
-   nth\_occurrence: A bigint type constant. If not specified, the default value is 1, which indicates the first appearance position in searching course.  It is less than or equal to 0 or other type, an exception is indicated.
-   return\_option: A bigint type constant. Its value is 0 or 1. If it is other type or an unallowed value, an exception is indicated.  0 indicates returning the start position of the matching. 1 indicates returning the end position of the matching.
-   Return value: Bigint type,  the start or end position of matched substring in source specified by return\_option.

Examples:

```
regexp_instr("i love www.taobao.com", "o[[:alpha:]]{1}", 3, 2) = 14
```

## REGEXP\_REPLACE {#section_k2w_2d1_wdb .section}

The command format is as follows:

```
string regexp_replace(string source, string pattern, string replace_string[, bigint occurrence])
```

Usage:

replace the substring in source which is matched ‘pattern’ for nth occurrence to be a specified string ‘replace\_string’ and then return.

Parameter description:

-   source: String type, a string to be replaced.
-   pattern: String type constant. The pattern to be matched. If it is null, an exception is indicated.
-   replace\_string: String type, the string after replacing matched pattern.
-   Occurrence: Bigint type constant, must be greater than or equal to 0, which indicates replacing nth matching to be replace\_string. If it is 0, it indicates all matched substrings have been replaced.  If it is other type or less than 0, an exception is indicated. It can be default, and the default is 0.
-   Return value: String type. When referencing a group which is not existent, do not replace the string. Returns NULL when the source, pattern, occurrence parameter is entered as null, returns NULL, replace\_string is null, but pattern does not match, if the replace\_string is null and the pattern is matched, returns the original string.

**Note:** When the group to be referenced does not exist, it is considered to be undefined.

Examples:

```
regexp_replace("123.456.7890", "([[:digit:]]{3})\\.([[:digit:]]{3})\\.([[:digit:]]{4})",
"(\\1)\\2-\\3", 0) = "(123)456-7890"
regexp_replace("abcd", "(.)", "\\1 ", 0) = "a b c d "
regexp_replace("abcd", "(.)", "\\1 ", 1) = "a bcd"
regexp_replace("abcd", "(.)", "\\2", 1) = "abcd"
-- Only a group is defined in pattern and the referenced second group is not existent.
-- Please avoid this. The result to reference nonexistent group is not defined.
regexp_replace("abcd", "(. *)(.)$", "\\2", 0) = "d"
regexp_replace("abcd", "a", "\\1", 0) = "bcd"
-- No group definition is in pattern, so '\1' references a nonexistent group,
 -- Please avoid this. The result to reference nonexistent group is not defined.
```

## REGEXP\_SUBSTR {#section_k5b_qd1_wdb .section}

The command format is as follows:

```
string regexp_substr(string source, string pattern[, bigint start_position[, bigint nth_occurrence]])
```

Usage:

Starting from start\_position, find the substring in source which matches specified pattern for the nth occurrence.

Parameter description:

-   source: String type, string to be searched.
-   pattern: A string type constant. The pattern to be matched. If it is null, an exception is indicated.
-   start\_position: A Bigint type constant, must be greater than 0. Other types or less than equal to 0 throw exceptions, do not specify a default of 1, indicates that the match begins with the first character of source. If it is not specified, default value is 1, which indicates matching from the first character of source.
-   nth\_occurrence: a Bigint type constant, must be greater than 0. If not specified, the default value is 1, which indicates returning the substring of first matching. If not specified, the default value is 1, which indicates returning the substring of first matching.
-   Return value: String type. Any input parameter is NULL, return NULL. If no matching record exists, return NULL.

Examples:

```
regexp_substr ("I love aliyun very much", "a[[:alpha:]]{5}") = "aliyun"
regexp_substr('I have 2 apples and 100 bucks!', '[[:blank:]][[:alnum:]]*', 1, 1) = " have"
regexp_substr('I have 2 apples and 100 bucks!', '[[:blank:]][[:alnum:]]*', 1, 2) = "2"
```

## REGEXP\_COUNT {#section_hxs_yd1_wdb .section}

The command format is as follows:

```
bigint regexp_count(string source, string pattern[, bigint start_position])
```

Usage:

Count the occurrence that the substring matches specified pattern starting from start\_position in source.

Parameter description:

-   Source: String type, the string to be searched. If it is other type, report the error.
-   Pattern: String type constant, the pattern to be matched. If it is null string or other data type, an exception is indicated.
-   start\_position: Bigint type constant, must be greater than 0. If it is other data type or a value which is less than or equal to 0, an exception is indicated. If it is not specified, default value is 1, which indicates matching from the first character of source.
-   Return value: Bigint type. If no matching exists, return 0. If any input parameter is null, return null.

Examples:

```
regexp_count('abababc', 'a.c') = 1
regexp_count('abcde', '[[:alpha:]]{2}', 3) = 1
```

## SPLIT\_PART {#section_ecy_k21_wdb .section}

The command format is as follows:

```
string split_part(string str, string delimiter, bigint start[, bigint end])
```

Usage:

Split the string str according to the separator and return the substring from nth start part to nth end part.

Parameter description:

-   str: String type, the string to be split. If it is Bigint, Double, Decimal or Datetime, it is converted to String in implicit conversion. If it is other data type, an an exception is reported.
-   separator: A string type constant, the separator used to split the string. It can be a character or a string. If it is other data type, an exception is caused.
-   start: A bigint type constant, must be greater than 0. If it is not a constant or other data type, an exception is indicated. It indicated the start number of return part \(start from 1\). If the end is not specified, return the part specified by ‘start’.
-   ‘end’: A bigint type constant, must be greater than or equal to ‘start’, otherwise an exception is indicated. It refers to the end number of return part. If it is not a constant or is other data type, an exception is also indicated.  It can be omitted, which indicates the last part.
-   Return value: String type. If any parameter is null, return null. If separator is empty string, return the source string str.

**Note:** 

-   If ‘delimiter’ does not exist in str, then specify ‘start’ to be 1, and return the entire str. If the input value is a blank string, the output value is a blank string.
-   If the start value is greater than the number of parts after splitting, for example, the splitting produces 6 parts but the ‘start’ value is greater than 6, then return an empty string.
-   If ‘end’ is greater than ‘part’ quantity, take it as the part quantity.

Examples:

```
split_part('a,b,c,d', ',', 1) = 'a'
split_part('a,b,c,d', ',', 1, 2) = 'a,b'
split_part('a,b,c,d', ',', 10) = ''
```

## SUBSTR {#section_nkj_1f1_wdb .section}

The command format is as follows:

```
string substr(string str, bigint start_position[, bigint length])
```

Usage:

Return the substring of ‘str’ from start\_position with the given length.

Parameter description:

-   ‘str’: String type. If the input is Bigint, Double, Decimal or Datetime, it is converted to String in implicit conversion. If it is other data type, an exception is reported.
-   The start\_position:Bigint type starts at 1. When start\_position is negative, the starting position is counted back from the end of the string, the last character is-1, and the previous number is in turn-2,-3 ..., Other types of throwing exceptions.
-   length: Bigint type, must be greater than 0. If it is other type or less than 0, an exception is indicated. This parameter indicates the length of the child string.
-   Return value: String type. If the input is NULL, return NULL.

**Note:** If the length is omitted, return the substring from start to end.

Examples:

```
substr("abc", 2) = "bc"
substr("abc", 2, 1) = "b"
substr("abc",-2,2)="bc"
substr("abc",-3)="abc"
```

## SUBSTRING {#section_s1h_3f1_wdb .section}

The command format is as follows:

```
string  substring(string|binary str, int start_position[, int length])
```

Usage:

Return the substring of ‘str’ from start\_position with the given length.

Parameter description:

-   str: String or Binary type, returns NULL or an error for another type.
-   ‘start\_position’: Int type, starting at 1. When start\_position is negative, the starting position is counted back from the end of the string, the last character is-1, and the previous number is in turn-2,-3 ..., Other types of throwing exceptions.
-   length: Bigint type, must be greater than 0. If it is other type or less than 0, an exception is indicated. This parameter indicates the length of the child string.
-   Return value: String type. If the input is NULL, return NULL.

**Note:** If the length is omitted, return the substring from start to end.

**For example**:

```
substring('abc', 2) = 'bc'
substring('abc', 2, 1) ='"b'
substring('abc',-2,2)='bc'
substring('abc',-3,2)='ab'
substring(BIN(2345),2,3)='001'
```

## TOLOWER {#section_nzz_lg1_wdb .section}

The command format is as follows:

```
string tolower(string source)
```

Usage:

Input the lowercase string corresponding to the English string source.

Parameter description:

-   Source: String type. If the input is Bigint, Double, Decimal or Datetime, it is converted to String in implicit conversion. If it is other data type, an exception is reported.
-   Return Value: String type.  If the input is NULL, return NULL.

Examples:

```
tolower("aBcd") = "abcd"
tolower("Haha Cd") = "haha cd"
```

## TOUPPER {#section_qvg_sg1_wdb .section}

The command format is as follows:

```
string toupper(string source)
```

Usage:

Output the uppercase string corresponding to the English string ‘source’.

Parameter description:

-   Source: String type. If the input is Bigint, Double, Decimal or Datetime, it is converted to String in implicit conversion. If it is other data type, an exception is reported.
-   Return Value: String type.  If the input is NULL, return NULL.

Examples:

```
toupper("aBcd") = "ABCD"
toupper("HahaCd") = "HAHACD"
```

## TO\_CHAR {#section_lgq_wg1_wdb .section}

The command format is as follows:

```
string to_char(boolean value)
string to_char(bigint value)
string to_char(double value)
string to_char(decimal value)
```

Usage:

Convert Boolean type, Bigint type or Double type to corresponding String type.

Parameter description:

-   Value: Boolean, Bigint or Double type is acceptable. If it is other data type, an exception is indicated. For formatted output of the datetime type, see another function TO\_CHAR that has the same name.
-   Return value: String type. If the input is NULL, return NULL.

Examples:

```
to_char(123) = '123'
to_char(true) = 'TRUE'
to_char(1.23) = '1.23'
to_char(null) = NULL
```

## TRIM {#section_mf1_3h1_wdb .section}

The command format is as follows:

```
string trim(string str)
```

Usage:

Remove left space and right space for the input string str.

Parameter description:

-   ‘str’: String type. If the input is Bigint, Double, Decimal or Datetime, it is converted to String in implicit conversion. If it is other data type, an exception is reported.
-   Return value: String type.  If the input is NULL, return NULL.

## LTRIM {#section_shc_d21_wdb .section}

The command format is as follows:

```
string ltrim(string str)
```

Usage:

Remove left space for the input string str.

Parameter description:

-   ‘str’: String type. If the input is Bigint, Double, Decimal or Datetime, it is converted to String in implicit conversion. If it is other data type, an exception is reported.
-   Return value: String type.  If the input is NULL, return NULL.

Examples:

```
select ltrim(' abc ') from dual;  
Returns: 
+-----+
| _c0 |
+-----+
| abc |
+-----+
```

## RTRIM {#section_gtk_rh1_wdb .section}

The command format is as follows:

```
string rtrim(string str)
```

Usage:

Remove right space for the input string str.

Parameter description:

-   ‘str’: String type. If the input is Bigint, Double, Decimal or Datetime, it is converted to String in implicit conversion. If it is other data type, an exception is reported.
-   Return value: String type.  If the input is NULL, return NULL.

Examples:

```
select rtrim('a abc ') from dual; 
Returns:  
+-----+
| _c0 |
+-----+
| a abc |
+-----+
```

## REVERSE {#section_or4_d31_wdb .section}

The command format is as follows:

```
STRING REVERSE(string str)
```

Usage:

Returns a reversed-order string.

Parameter description:

-   str: String type. If the input is Bigint, Double, Decimal or Datetime, it is converted to String in implicit conversion. If it is other data type, an exception is reported.
-   Return value: String type.  If the input is NULL, return NULL.

Examples:

```
select reverse('abcedfg') from dual;
Returns: 
+-----+
| _c0 |
+-----+
| gfdecba |-
+-----+
```

## SPACE {#section_a25_h31_wdb .section}

The command format is as follows:

```
STRING SPACE(bigint n)
```

Usage:

A space string function that returns a string of length n.

Parameter description:

-   n: Bigint type.  The length does not exceed 2 MB.  If it is NULL, an exception is thrown.
-   Return value: String type.

Examples:

```
select length(space(10)) from dual; ----Returns 10.   
select space(400000000000) from dual; ----Error, the length exceeds 2 MB.
```

## REPEAT {#section_gnq_m31_wdb .section}

The command format is as follows:

```
STRING REPEAT(string str, bigint n)
```

Usage:

Returns the str string that is repeated for n times.

Parameter description:

-   ‘str’: String type. If the input is Bigint, Double, Decimal or Datetime, it is converted to String in implicit conversion. If it is other data type, an exception is reported.
-   n: Bigint type. The length does not exceed 2 MB.  If it is NULL, an exception is thrown.
-   Return value: String type.

Examples:

```
select repeat('abc',5) from lxw_dual; 
Returns:abcabcabcabcabc
```

## ASCII {#section_hgm_s31_wdb .section}

The command format is as follows:

```
Bigint ASCII(string str)
```

Usage:

Returns the ascii of the first character of str.

Parameter description:

-   str: String type. If the input is Bigint, Double, Decimal or Datetime, it is converted to String in implicit conversion. If it is other data type, an exception is reported.
-   Return value: Bigint type.

Examples:

```
select ascii('abcde') from dual;
Returns:97
```

## Maxcomputerte2.0 Extension function { .section}

With the upgrade to MaxCompute 2.0, some mathematical functions have been added to the product. If a new function uses a new data type, you must add the following set statement before using the new functions SQL statement:

```
set odps.sql.type.system.odps2=true;
```

**Note:** Add set odps before the SQL statement that uses the function`set odps.sql.type.system.odps2 = true;`, and commit runs with SQL for normal use of the new data type.

The new expanded string function is described in detail below.

## CONCAT\_WS {#section_xnf_sj1_wdb .section}

The command format is as follows:

```
string concat_ws(string SEP, string a, string b...)
string concat_ws(string SEP, array)
```

**Note:** Add set odps before the SQL statement that uses the function`set odps.sql.type.system.odps2 = true;`, and commit runs with SQL for normal use of the new data type.

Usage:

Concatenates all strings in the parameters, connected by the specified delimiter.

Parameter description:

-   SEP: String-type delimiter. If not specified, an exception is returned.
-   a/b… String type. If Bigint, Decimal, Double or Datetime types are input, they are implicitly converted to String type before calculation. If the input is another type, an exception is reported.

Return value:

String type.  If no parameters exist or any parameter is null, the results all return null.

Examples:

```
concat_ws(':','name','hanmeimei')='name:hanmeimei'
concat_ws(':','avg',null,'34')=null
```

## LPAD {#section_mcj_zj1_wdb .section}

The command format is as follows:

```
string lpad(string a, int len, string b)
```

**Note:** Add set odps before the SQL statement that uses the function`set odps.sql.type.system.odps2 = true;`, and commit runs with SQL for normal use of the new data type.

Usage:

Uses string b to pad string a to the left to the place specified by len.

Parameter description:

-   len: Int-type integer.
-   a, b…: String type.

Return value:

String type.  If len is smaller than the number of places in a, a is truncated from the left to obtain a string with the number of places specified by len. If len is 0, a blank result is returned.

Examples:

```
lpad('abcdefgh',10,'12')='12abcdefgh'
lpad('abcdefgh',5,'12')='abcde'
lpad('abcdefgh',0,'12') Returns a blank result
```

## RPAD {#section_k1f_3k1_wdb .section}

The command format is as follows:

```
string rpad(string a, int len, string b)
```

**Note:** Add set odps before the SQL statement that uses the function`set odps.sql.type.system.odps2 = true;`, and commit runs with SQL for normal use of the new data type.

Usage:

Uses string b to pad string a to the right to the place specified in len.

**Note:** 

You need to add the set odps statement before the SQL statement that uses the function`set odps.sql.type.system.odps2 = true`, otherwise the error is reported.

Parameter description:

-   len: Int-type integer.
-   a/b…: String type.

Return value:

String type.  If len is smaller than the number of places in a, a is truncated from the left to obtain a string with the number of places specified by len. If len is 0, a blank result is returned.

Examples:

```
rpad('abcdefgh',10,'12')='abcdefgh12'
rpad('abcdefgh',5,'12')='abcde'
rpad('abcdefgh',0,'12') Returns a blank result
```

## REPLACE {#section_ln3_5k1_wdb .section}

The command format is as follows:

```
string replace(string a, string OLD, string NEW)
```

**Note:** Add set odps before the SQL statement that uses the function`set odps.sql.type.system.odps2 = true;`, and commit runs with SQL for normal use of the new data type.

Usage:

Uses string NEW to replace the portion of string a that completely matches string OLD and then returns string a.

Parameter description:

The parameters are all String type.

Return value:

String type. If the input is null, null is returned.

Examples:

```
replace('ababab','abab','12')='12ab'
replace('ababab','cdf','123')='ababab'
replace('123abab456ab',null,'abab')=null
```

## SOUNDEX {#section_z2z_1l1_wdb .section}

The command format is as follows:

```
string soundex(string a)
```

**Note:** Add set odps before the SQL statement that uses the function`set odps.sql.type.system.odps2 = true;`, and commit runs with SQL for normal use of the new data type.

Usage:

Converts a normal string to a soundex string.

Parameter description: a is of type String.

Return value: String type. If the input value is NULL, the return value is NULL.

Examples:

```
soundex('hello')='H400'
```

## SUBSTRING\_INDEX {#section_uw3_hl1_wdb .section}

The command format is as follows:

```
string substring_index(string a, string SEP, int count))
```

**Note:** Add set odps before the SQL statement that uses the function`set odps.sql.type.system.odps2 = true;` and commit runs with SQL for normal use of the new data type.

Usage:

Truncates string a to the portion in front of the delimiter specified by count. If count is positive, the portion to the left of the delimiter is used. If count is negative, the portion to the right is used.

Parameter description: a/sep belong to the string type, and count belongs to the int type.

Return value:

String type. If the input is null, null is returned.

Examples:

```
substring_index('https://help.aliyun.com', '.', 2)='https://help.aliyun'
substring_index('https://help.aliyun.com', '.', -2)='aliyun.com'
substring_index('https://help.aliyun.com', null, 2)=null
```

