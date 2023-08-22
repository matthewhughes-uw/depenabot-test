# dependabot test

Investigating the cause of dependabot making PRs with a `toolchain go1.21.0` in
`go.mod` on a Go 1.20 project (which then cause errors when we try to run).

The issue appears to be a Go 1.21+ module snuck in while dependabot was running
Go 1.20 still. To see this behaviour there is
`github.com/matthewhughes-uw/go-sample-package` where `v1.0.0` requires Go 1.20+
and `v1.1.0` requires Go 1.21+, so if we run (assuming `go` is at 1.20):

``` console
$ go get github.com/matthewhughes-uw/go-sample-package@v1.1.0
$ go mod tidy
```

Then there's no `toolchain` directive. We can bump more dependencies and see no
issue:

``` console
$ go get github.com/stretchr/testify@v1.8.4
$ go list -m -f '{{.GoVersion}}'
1.20
```

But the moment we run anything with Go 1.21 the directive is added:

``` console
$ go1.21.0 mod tidy
$ go1.21.0 list -m -f '{{.GoVersion}}'
1.21
```
