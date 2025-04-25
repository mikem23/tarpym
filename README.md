# tarpym
convert rpms to and from exploded contents

This is **proof-of-concept** code and should not be used for real work.

# usage

```
$ ./tarpym --help
usage: tarpym [-h] [-x] [-c] [-u] [-f FILENAME] [-d DIRECTORY] [--no-payload] [--cpio-payload] [--rpmbuild-payload]

convert rpms to and from exploded contents

options:
  -h, --help            show this help message and exit
  -x, --extract         Extract rpm file to directory
  -c, --create          Create rpm file from directory
  -u, --update          Update extracted header data from payload contents
  -f FILENAME, --filename FILENAME
                        RPM filename
  -d DIRECTORY, --directory DIRECTORY
                        Directory of RPM contents
  --no-payload          skip payload, for debugging
  --cpio-payload        use cpio to construct the payload
  --rpmbuild-payload    use rpmbuild to construct the payload
```

There a three available actions:

1. extracting an rpm to a directory
2. creating a new rpm from an extracted directory
3. updating extracted header data based on the payload directory

The extracted headers are stored in human-friendly json with related headers
conveniently grouped.


# examples

Simple header modification:
```
$ tarpym -x -d test -f koji-1.34.1-1.noarch.rpm
1562 blocks
Expoded koji-1.34.1-1.noarch.rpm to test
$ vim test/header.json  # edit release value
$ tarpym -c -d test -f test.rpm
1562 blocks
Got 57 index entries
Warning: ignoring region tag in input data
Got 6 index entries
$ rpm -Kv test.rpm
test.rpm:
    Header SHA256 digest: OK
    Header SHA1 digest: OK
    Payload SHA256 digest: OK
    MD5 digest: OK
$ rpm -qp test.rpm
koji-1.34.1-1.BUMP.noarch
```

Altering the payload:
```
$ tarpym -x -f fake-1.1-37.noarch.rpm  -d fake
1 block
Expoded fake-1.1-37.noarch.rpm to fake
$ echo 'HELLO WORLD' > fake/payload/usr/bin/nosuchfile
$ tarpym -u -d fake
Adjust size for /usr/bin//nosuchfile to 12
Adjust checksum for /usr/bin//nosuchfile to 2949725604dd9eef82100f8ff39fcced9d3682700ee2fb5c4205e3e584defee6
$ tarpym -c  -f fake4.rpm  -d fake
Got 57 index entries
Warning: ignoring region tag in input data
Got 6 index entries
$ rpm -Kv fake4.rpm
fake4.rpm:
    Header SHA256 digest: OK
    Header SHA1 digest: OK
    Payload SHA256 digest: OK
    MD5 digest: OK
$ rpm2archive fake4.rpm
$ tar -O -xzvf  fake4.rpm.tgz
./usr/bin/nosuchfile
HELLO WORLD!
```

# limitations

This is **proof-of-concept** code and should not be used for real work.

This tool works for simple cases, but has not been broadly tested and will
definitely fail in some cases.

The update action does not handle all cases.

By default, we use rpmbuild to construct the payload. If the version of rpm
on your system is different from the version that constructed the original
rpm, you may get a payload that the original version cannot read.

The alternate `--cpio-payload` option is experimental and rpm does not like
the results.
