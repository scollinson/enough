Just Enough
=======

Sometimes I have a bunch of software components that need to talk to each
other over some kind of IP connection, and then I figure there might be a
potential issue with transport security. In an ideal world, TLS would be
perfect for this, and I could just do something reasonably easy and turn on
TLS for everything. Sadly, in this world, all 'normal' TLS deployments use PKI
based on the 'standard' system root CAs, which is ..uh.. bad, and have to
support a million options and cipher suites, which is insane. Added to that, I
would have to work out which algorithms and strengths and whatever else I
actually want. Finally, the command-line tools to even generate self-signed
stuff and then roll out a set of signed client certs are all nightmarish
incantations based on openssl or something, which, as far as I'm concerned, is
Satan's Commode.

So yeah. I wrote this, which is just enough.

## Documentation

If you want to use `import "github.com/bnagy/enough"` in your own code, you
can get godoc at: http://godoc.org/github.com/bnagy/enough/enough, but the
code is very tiny.

To run the standalone tlspark tool:
```
ben$ ./tlspark --help
Usage of ./tlspark:
  -clients=1: Number of client cert / keys to generate
  -name="": A short, shared service name eg 'WidgetCluser' (required)
  ```

## Installation

You should follow the [instructions](https://golang.org/doc/install) to
install Go, if you haven't already done so. Then:

```bash
$ go get github.com/bnagy/enough
```

That should build the tlspark command automatically. If not:
```bash
$ cd $GOPATH/src/github.com/bnagy/enough && go build
```

NOTE: Because Go binaries are statically linked, you can build tlspark and
then just ship binary copies around to systems where you need it - no need to
care about library versions, go installations or anything else. You can also
cross compile for, say, Windows from any box where you have Go installed, and
then copy the .exe. Go's distribution model is pretty cool sometimes.

## Concept

The `tlspark` binary will generate two .pem files for every member of your new
park. That will be one CA, one Server and as many clients as you asked for. I
don't tell you how to move the files or import them into your software,
because "it depends." The clients will need the CA cert to verify the server,
and the server needs it to verify the clients.

Once you've run `tlspark` its job is done. It doesn't run as a service or
offer any kind of API or anything. It just makes your certs. Your park is
going to use static, manually distributed certs.

Here are some suggestions:

* Allow only TLS 1.2
* Allow only TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 ( 0xc02b )
* Turn off stuff like session ticket support
* Clients _usually_ verify the server by default, but you should TURN ON client verification at the server end

## Infrequently Asked Questions

__Why ECDHE?__

[Thomas Porninis smarter than I am](http://security.stackexchange.com/a/27888)

__Why GCM?__

Long story short, it's FAST, authenticated and patent free.

__Why only AES-128?__

GCM uses 128-bit blocks.

__Why SHA256?__

You're new around here, aren't you?

__Why EC? Aren't you worried about those NIST curves?__

A bit, but I prioritised performance over paranoia.

If you think you're worth someone burning the biggest crypto backdoor of the
last 10 years on you you should probably write something yourself.

## Contributing

Fork and send a pull request to contribute code. I won't accept any code that
adds options. If you find that I have done something insane with my TLS, open
an Issue.

## License

BSD style, see LICENSE file for details.