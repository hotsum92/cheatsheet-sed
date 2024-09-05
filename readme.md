# cheatsheet-sed

## optons

| Option                   | Description                         |
| ------------------------ | ----------------------------------- |
| -n, --quiet              | not print the pattern space         |
| -e, --expression         | the commands to be executed         |
| -f, --file               | the file containing the script      |
| -i, --in-place           | make the changes in a file directly |
| -E,-r, --regexp-extended | use extended regular expressions    |

## commands

| Command      | Description                                                                |
| ------------ | -------------------------------------------------------------------------- |
| a            | write text to standard output before reading next line                     |
| b[label]     | branch to [label]                                                          |
| c            | delete patternspace and write text to standard output                      |
| d            | delete patternspace and start next cycle                                   |
| D            | delete first line in patternspace and start next cycle                     |
| e            | execute on the command line                                                |
| g            | replace patternspace with holdspace                                        |
| G            | append \n holdspace to patternspace                                        |
| h            | replace holdspace with patternspace                                        |
| H            | append \n patternspace to holdspace                                        |
| i            | write text to standard output                                              |
| l            | write patternspace in a visually unambiguous form                          |
| n            | write patternspace to standard output, replace patternspace with next line |
| N            | append next line to patternspace                                           |
| p            | write patternspace to standard output                                      |
| P            | write patternspace up to \n to standard output                             |
| q            | branch to end                                                              |
| Q            | quit immediately                                                           |
| r            | write the contents of a file to standard output before reading next line   |
| R            | write one line of a file to standard output before reading next line       |
| s/././[flag] | substitute                                                                 |
| t[label]     | branch on successful substitution                                          |
| T[label]     | branch on failed substitution                                              |
| y            | simple character replace                                                   |
| w            | write patterspace to file                                                  |
| W            | write first line of pattern space to file                                  |
| x            | exchange pattern and hold space                                            |
| =            | print line number                                                          |
| :[label]     | define label                                                               |

### examples

```
seq 1 | sed '1a2'                                   # 1\n2
seq 1 | sed '1i2'                                   # 2\n1
seq 2 | sed -e :loop -e 'N; $!b loop' -e 's/\n/ /g' # 1 2
seq 2 | sed '1c3'                                   # 3\n2
seq 2 | sed '1d'                                    # 2
seq 200 | sed ':a; N; 1,5ba; D'                     # 196\n197\n198\n199\n200
seq 3 | sed '/2/e echo 4'                           # 1\n4\n2\n3
seq 3 | sed -n '/2/h; g;p'                          # \n2\n2
seq 3 | sed 'G'                                     # 1\n\n2\n\n3\n\n
seq 3 | sed 'n;d'                                   # 1\n3
seq 3 | sed '/2/{x;p;x;}'                           # 1\n\n2\n3
echo -e 'a\tb' | sed 'l;d'                          # a\tb
seq 3 | sed = | sed 'N;s/\n/\t/'                    # 1\n1\n2\n2\n3\n3
seq 2 | sed -n '1!p'                                # 2
seq 2 | sed -n '1q'                                 #
seq 2 | sed -n 'N;P'                                # 1
seq 3 | sed 's/[13]/&/; t; s/./& O/'                # 1 O\n2 O\n3
seq 3 | sed 's/[13]/&/; T; s/./& O/'                # 1\n2 O\n3
echo 'baz foo' | sed '/baz/!s/foo/bar/g'            # baz foo
echo '[]' | sed 'y/[]/()/'                          # ()
seq 3 | sed '1h; 3x;'                               # 1\n2\n1
seq 3 | sed '=' | sed 'N;s/\n/\t/'                  # 1\t2\n3\t
echo puce | sed 's/scarlet\|ruby\|puce/red/g'       # red
seq 3 | sed '1!G;h;$!d'                             # 3\n1\n2
```

## BRE ERE

| name                              | BRE            | ERE            |
| --------------------------------- | -------------- | -------------- |
| Collation-related bracket symbols | [==] [::] [..] | [==] [::] [..] |
| Escaped characters                | \              | \              |
| Bracket expression                | []             | []             |
| Grouping                          | \(\) \n        | () \n          |
| Single-character duplication      | * \{m,n\}      | * + ? {m,n}    |
| Anchoring                         | ^ $            | ^ $            |

## need to escape

```
BRE .[\*^$
ERE .[\*^$()+?{|
```

## oneliners

### replace first occurrence

```
echo 'foo foo' | sed 's/foo/bar/'
```

### replace last occurrence

```
echo 'foo foo' | sed 's/\(.*\)foo/\1bar/'
echo 'foo foo foo' | sed -E 's/(.*) (foo)/\1 bar/'
```

### insert block upper

```
sed '/line3/e cat insert.txt' sample.txt
```

### insert block lower

```
sed '/line3/r insert.txt' sample.txt
```

### whitespace to one space

```
echo '   a    b ' |
sed -e 's/^[ \t]*//' -e 's/[ \t]*$//' -e 's/[ \t][ \t]*/ /g'
# a b
```


### replace line endings with spaces

```
echo -e '1\n2' |
sed -e :loop -e 'N; $!b loop' -e 's/\n/ /g'
# 1 2
```

### number each non-empty line

```
echo -e '1\n\n3\n\n5' |
sed '/./=' | sed '/./N; s/\n/ /'
# 1 1\n\n3 3\n\n5 5
```

### convert DOS/Windows newlines (CRLF) to Unix newlines (LF)

```
sed 's/.$//'
sed 's/^M$//'
sed 's/\x0D$//'
```

### convert Unix newlines (LF) to DOS/Windows newlines (CRLF)

```
sed "s/$/`echo -e \\\r`/"
sed 's/$/\r/'
```

### Digit grouping

```
echo 1234567 |
sed -e :a -e 's/\(.*[0-9]\)\([0-9]\{3\}\)/\1,\2/;ta'
# 1,234,567

echo 1234567 |
sed -E ':l; s/^([0-9]+)([0-9]{4})/\1,\2/; t l;';
# 123,4567
```

### camelCase(PascalCase) to snake_case

```
echo 'camelCase' |
sed -E 's/(.)([A-Z])/\1_\2/g' | tr '[A-Z]' '[a-z]'
# camel_case
```

### snake_case to camelCase

```
echo 'snake_case' |
sed -r 's/(_)(.)/\u\2/g'
# snakeCase
```

### PascalCase to snake_case

```
echo 'PascalCase' |
sed -r 's/(\b|_)(.)/\u\2/g'
# pascal_case
```

## restore backup files

```
ls *.bak | sed 's/\(.*\).bak/mv & \1/' | bash
```

## sanitize a string for use in sed

```
echo '. [ \ * ^ $' | sed 's/[][\\.*^$]/\\&/g'
echo '. [ \ * ^ $ ( ) + ? { |' | sed 's/[.\\*^$(){}+?|]/\\&/g'

OUTER_STRING='hello $( string | is [ a ${ crazy } mess ] like wow; end'
INNER_STRING=' $( string | is [ a ${ crazy } mess ] like wow; en'
CLEAN_SED_STRING="$(echo "$INNER_STRING" | sed 's:[]\[^$.*/]:\\&:g')"
echo "$OUTER_STRING" | sed "s/$CLEAN_SED_STRING/ worl/"
```

##### extract text inside tag

```
echo "<h1>test</h1>" |
grep -o '<h1>.*</h1>' | sed 's#<h1>\(.*\)</h1>#\1#'
```

##### extract multi text inside tag

```
echo """
<head>
<title>
A
B
C
</title>
</head>
""" | sed -n '/<title>/,/<\/title>/p'
```

##### extract field

```
echo 'XXX 2024-10-23 XXX 2024-10-23 XXX' | sed -n 's/.*\(....-..-..\).*\(....-..-..\).*/\1 \2/p'
```

##### template

```
_1=A
_2=B
template='{ "value": "\1", "value": "\2" }'
sed -e 's/\\1/'"$_1"'/' -e 's/\\2/'"$_2"'/' <<< "$template"
```

##### print certain line

```
seq 20000 |
sed -n '10000,10005p;'
```

#### remote escape newline

```
echo '1 \
2
3 \
4 \
5' |
sed -e :a -e '/\\$/N; s/\\\n//; ta'
```

### connect comma

```
echo '1
,2
,3
4
,5' |
sed -e :a -e '$!N;s/\n,/,/;ta' -e 'P;D'
```
