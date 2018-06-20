# Escape Characters {#concept_oyx_gsf_vdb .concept}

In MaxCompute SQL, a string constant can be set off by single \(‘\) or double quotation marks \(“\). The string set off by single quotation marks can contain double quotation marks or the string set off by double quotation marks can contain single quotation marks. Otherwise, you must use an escape character to indicate it. 

The following expressions are acceptable:

```
"I'm a happy manong."
'I\'m a happy manong.'
```

In MaxCompute SQL, ‘\\’ is a kind of escape character used to express the special character in a string or express its followed characters as characters themselves.  To read a string constant, if ‘\\’ is followed by three effective 8 hexadecimal digits  and corresponding range is from 001 to 177, the system converts it to corresponding characters according to an ASCII value. 

The following table lists some special escape characters:

|Escape|Character|
|:-----|:--------|
|\\b|backspace|
|\\t|tab|
|\\n|newline|
|\\r|carriage-return|
|\\’|single quotation mark|
|\\”|double quotation marks|
|\\ \\|Backslash|
|\\;|Semicolon|
|\\Z|control-Z|
|\\0 or \\00|Terminator|

```
select length('a\tb') from dual;
```

The result is 3,  which indicates that three characters are in the string. The ‘\\t’ is considered as one character.  Other following characters are expressed as themselves.

```
select 'a\ab',length('a\ab') from dual;
```

The result: ‘aab’, 3.  ‘\\a’ is expressed as general ‘a’.

