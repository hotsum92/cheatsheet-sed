# cheatsheet-sed

## optons

| Option           | Description                         |
| ---------------- | ----------------------------------- |
| -n, --quiet      | not print the pattern space         |
| -e, --expression | the commands to be executed         |
| -f, --file       | the file containing the script      |
| -i, --in-place   | make the changes in a file directly |

## commands

| Command  | Description                         |
| -------- | ----------------------------------- |
| a        | write text to standard output before reading next line |
| b[label] | branch to [label]                                      |
| c        | delete patternspace and write text to standard output  |
| d        | delete patternspace and start next cycle               |
| D        | delete first line in patternspace and start next cycle |
| e        | execute on the command line         |
| g        | replace patternspace with holdspace |
| G        | append \n holdspace to patternspace |
| h        | replace holdspace with patternspace |
| H        | append \n patternspace to holdspace |
| i        | write text to standard output       |
| l        | write patternspace in a visually unambiguous form                          |
| n        | write patternspace to standard output, replace patternspace with next line |
| N        | append next line to patternspace               |
| p        | write patternspace to standard output          |
| P        | write patternspace up to \n to standard output |
| q        | branch to end                                  |
| Q        | quit immediately                    |
| r        | write the contents of a file to standard output before reading next line |
| R        | write one line of a file to standard output before reading next line     |
| s/././f  | substitute                          |
| t[label] | test for substitution               |
| T        | test for no substitution            |
| y        | simple character replace            |
| w        | write to file                       |
| W        | write line to file                  |
| z        | clear line (zap)                    |
| x        | exchange pattern and hold space     |
| =        | print line number                   |
| :[label] | define label                        |

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
seq 3 | sed 's/[12]/?/; t; s/./!/'                     # 1\n?\n3
```

## oneliners

### replace line endings with spaces

```
echo -e '1\n2' | sed -e :loop -e 'N; $!b loop' -e 's/\n/ /g' # 1 2
```

