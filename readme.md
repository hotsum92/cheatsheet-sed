# cheatsheet-sed

## commands

| Command  | Description                       |
| -------- | --------------------------------- |
| a        | append                            |
| b        | branch                            |
| c        | change text                       |
| d        | delete lines                      |
| D        | delete first line in patternspace |
| e        | execute on the command line       |
| g        | copy from holdspace               |
| G        | append from hold space            |
| h        | copy to hold space                |
| H        | append to hold space              |
| i        | insert                            |
| l        | print substitute line feeds       |
| n        | next input line                   |
| N        | append next input line            |
| p        | print                             |
| P        | print first line                  |
| q        | quit                              |
| Q        | quit immediately                  |
| r        | read file                         |
| R        | read line from file               |
| s        | substitute                        |
| t        | test for substitution             |
| T        | test for no substitution          |
| y        | simple character replace          |
| w        | write to file                     |
| W        | write line to file                |
| z        | clear line (zap)                  |
| =        | print line number                 |
| :[label] | define label                      |

### examples

```
seq 1 | sed '1a2'                                   # 1\n2
seq 2 | sed -e :loop -e 'N; $!b loop' -e 's/\n/ /g' # 1 2
seq 2 | sed '1c3'                                   # 3\n2
seq 2 | sed '1d'                                    # 2
seq 200 | sed ':a; N; 1,5ba; D'                     # Like `tail -5`
seq 3 | sed '/2/e echo 4'                           # 1\n4\n2\n3
```

## oneliners

### replace line endings with spaces

```
echo -e '1\n2' | sed -e :loop -e 'N; $!b loop' -e 's/\n/ /g' # 1 2
```

