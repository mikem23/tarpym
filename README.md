# tarpym
convert rpms to and from exploded contents

# example
```
$ ./tarpym -x -d foo -f ~/koji-1.34.1-1.noarch.rpm
1562 blocks
Expoded /home/mikem/koji-1.34.1-1.noarch.rpm to foo
$ ./tarpym -c -d foo -f test.rpm
Got 58 index entries
Got 58 index entries
Got 7 index entries
Got 7 index entries
$ rpm -qp test.rpm
koji-1.34.1-1.noarch
```

currently the `--create` option omits the payload
