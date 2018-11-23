# LIKE usage {#concept_gdj_btf_vdb .concept}

In LIKE  matching, ‘%’ indicates matching any multiple characters. The ‘\_’ indicates matching a single character. To match ‘%’ or ‘\_’ itself, you must escape it. The ‘\\\\%’ matches the character ‘%’ and  ‘\\\\\_’ matches the character ‘\_’.

```
    'abcd' like 'ab%' -- true
    'abcd' like 'ab_' -- false
    'ab_cde' like 'ab\\_c%'; -- true
```

**Note:** MaxCompute SQL only supports the UTF-8 character set. If the data is encoded in another format, it is possible that the calculation result is not correct.

