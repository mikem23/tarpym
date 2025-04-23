# tarpym
convert rpms to and from exploded contents

This is **proof-of-concept** code and should not be used for real work.

# example
```
$ ./tarpym -x -d test -f koji-1.34.1-1.noarch.rpm
1562 blocks
Expoded koji-1.34.1-1.noarch.rpm to test
$ vim test/header.json  # edit release value
$ ./tarpym -c -d test -f test.rpm
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

# limitations

This is **proof-of-concept** code and should not be used for real work.

We use cpio to write the payload, so we cannot write the modified format that
rpm uses for files > 4GB.

We omit the uncompressed payload checksum (payloaddigestalt).

We unconditionally use zstd to compress the payload.

We do not recalculate file checksums in the payload.

Some parts of rpm to not like our payload. E.g.
```
$ rpm2archive test.rpm >/dev/null
Segmentation fault (core dumped)
```
