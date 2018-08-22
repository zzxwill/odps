# Regular expression {#concept_ihp_ntf_vdb .concept}

The regular expressions in MaxCompute SQL use the PCRE standard, matched by characters. The meta character to be supported is as follows:

|Metacharacter|Description|
|:------------|:----------|
|^|Top of line \(TOL\)|
|$|End of line|
|.|Any character|
|\*|Matches for zero or multiple times|
|+|Matches for once or multiple times|
|?|Matches for zero time or once|
|?|Matches modifier. When this character follows any other constraints \(\*, +,? \{n\}, \{n, \{n, m\},\},  the match mode is non greedy.  Non greedy mode matches strings as little as possible,  while the default greedy mode matches strings as more as possible.|
|A | B|A or B|
|\(abc\)\*|Matches ‘abc’ for zero or multiple times|
|\{n\} or \{m, n\}|Matching times|
|\[ab\]|Matches any character in the brackets. In the example, it is to match a or b.|
|\[a-d\]|Matches any character in a, b, c, and d.|
|\[^ab\]|^ indicats ‘non’, to match any character which is not a and b.|
|\[::\]|See POSIX character group in next table.|
|\\|Escape character|
|\\n|N is a digit from 1 to 9 and is backward referenced.|
|\\d|digits|
|\\ D|Non-number|

POSIX character group:

|POSIX Character Group|Description|Range|
|:--------------------|:----------|:----|
|\[\[:alnum:\]\]|letter and digit characters|\[a-zA-Z0-9\]|
|\[\[:alpha:\]\]|letter|\[a-zA-Z\]|
|\[\[:ascii:\]\]|ASCII character|\[\\x00-\\x7F\]|
|\[\[:blank:\]\]|Space character and tabs|\[ \\t\]|
|\[\[:cntrl:\]\]|Control character|\[\\x00-\\x1F\\x7F\]|
|\[\[:digit:\]\]|Digit character|\[0-9\]|
|\[\[:graph:\]\]|Characters except white space characters|\[\\x21-\\x7E\]|
|\[\[:lower:\]\]|Lowercase characters|\[a-z\]|
|\[\[:print:\]\]|\[:graph:\] and white space characters|\[\\x20-\\x7E\]|
|\[\[:punct:\]\]|punctuation|\[\]\[!” \[\]\[!”\#$%&’\(\)\*+,./:;<=\>? @\\^\_\`\{|\}~-\]|
|\[\[:space:\]\]|White space characters|\[ \\t\\r\\n\\v\\f\]|
|\[\[:upper:\]\]|Uppercase characters|\[A-Z\]|
|\[\[:xdigit:\]\]|hexadecimal character|\[A-Fa-f0-9\]|

Because the system uses a backslash \(\) as an escape character, all “\\” which appear in the regular expression pattern perform two escapes. For example, the regular expression needs to match the string “a+b”. The “+” is a special character in regular expressions and must be expressed by escape. The expression in a regular engine is “a\\+b”, because the system needs to explain a layer of escape, the expression which can match this string is “a\\\\+b”. 

Suppose that the table test\_dual is:

```
select 'a+b' rlike 'a\\\+b' from test_dual;

| _c1 |

| true |

```

In extreme cases, to match the character “ \\”,  because “ \\” is a special character in a regular engine, it needs to be expressed by “\\”, while the system does an escape for it again, it is written as “\\\\”.

```
select 'a\\b', 'a\\b' rlike 'a\\\\b' from test_dual;

| _c0 | _c1 |

| a\b | true |

```

**Note:** To write  `a\\b` in MaxCompute SQL, and the output result is `a\b`.

If TAB exists in a string, when the system reads these two characters `\t`, they are already saved as one character by the system. Therefore, in regular expression, it is a general character.

```
select 'a\tb', 'a\tb' rlike 'a\tb' from test_dual;| _c0 | _c1 |

| a b | true |

```

