mac os make install in `Canto` not working

```
vendor/github.com/zondax/hid/hid_enabled.go:38:11: fatal error: 'hidapi/mac/hid.c' file not found
        #include "hidapi/mac/hid.c"
                 ^~~~~~~~~~~~~~~~~~
1 error generated.
make: *** [install] Error 2

```

`brew install hidapi` does not solve the issue.