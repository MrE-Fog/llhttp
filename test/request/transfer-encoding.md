Transfer-Encoding header
========================

## `chunked`

### Parsing and setting flag

<!-- meta={"type": "request"} -->
```http
PUT /url HTTP/1.1
Transfer-Encoding: chunked


```

```log
off=0 message begin
off=4 len=4 span[url]="/url"
off=19 len=17 span[header_field]="Transfer-Encoding"
off=38 len=7 span[header_value]="chunked"
off=49 headers complete method=4 v=1/1 flags=8 content_length=0
```

### Parse chunks with lowercase size

<!-- meta={"type": "request"} -->
```http
PUT /url HTTP/1.1
Transfer-Encoding: chunked

a
0123456789
0


```

```log
off=0 message begin
off=4 len=4 span[url]="/url"
off=19 len=17 span[header_field]="Transfer-Encoding"
off=38 len=7 span[header_value]="chunked"
off=49 headers complete method=4 v=1/1 flags=8 content_length=0
off=52 chunk header len=10
off=52 len=10 span[body]="0123456789"
off=64 chunk complete
off=67 chunk header len=0
off=69 chunk complete
off=69 message complete
```

### Parse chunks with uppercase size

<!-- meta={"type": "request"} -->
```http
PUT /url HTTP/1.1
Transfer-Encoding: chunked

A
0123456789
0


```

```log
off=0 message begin
off=4 len=4 span[url]="/url"
off=19 len=17 span[header_field]="Transfer-Encoding"
off=38 len=7 span[header_value]="chunked"
off=49 headers complete method=4 v=1/1 flags=8 content_length=0
off=52 chunk header len=10
off=52 len=10 span[body]="0123456789"
off=64 chunk complete
off=67 chunk header len=0
off=69 chunk complete
off=69 message complete
```

### POST with `Transfer-Encoding: chunked`

<!-- meta={"type": "request"} -->
```http
POST /post_chunked_all_your_base HTTP/1.1
Transfer-Encoding: chunked

1e
all your base are belong to us
0


```

```log
off=0 message begin
off=5 len=27 span[url]="/post_chunked_all_your_base"
off=43 len=17 span[header_field]="Transfer-Encoding"
off=62 len=7 span[header_value]="chunked"
off=73 headers complete method=3 v=1/1 flags=8 content_length=0
off=77 chunk header len=30
off=77 len=30 span[body]="all your base are belong to us"
off=109 chunk complete
off=112 chunk header len=0
off=114 chunk complete
off=114 message complete
```

### Two chunks and triple zero prefixed end chunk

<!-- meta={"type": "request"} -->
```http
POST /two_chunks_mult_zero_end HTTP/1.1
Transfer-Encoding: chunked

5
hello
6
 world
000


```

```log
off=0 message begin
off=5 len=25 span[url]="/two_chunks_mult_zero_end"
off=41 len=17 span[header_field]="Transfer-Encoding"
off=60 len=7 span[header_value]="chunked"
off=71 headers complete method=3 v=1/1 flags=8 content_length=0
off=74 chunk header len=5
off=74 len=5 span[body]="hello"
off=81 chunk complete
off=84 chunk header len=6
off=84 len=6 span[body]=" world"
off=92 chunk complete
off=97 chunk header len=0
off=99 chunk complete
off=99 message complete
```

### Trailing headers

<!-- meta={"type": "request"} -->
```http
POST /chunked_w_trailing_headers HTTP/1.1
Transfer-Encoding: chunked

5
hello
6
 world
0
Vary: *
Content-Type: text/plain


```

```log
off=0 message begin
off=5 len=27 span[url]="/chunked_w_trailing_headers"
off=43 len=17 span[header_field]="Transfer-Encoding"
off=62 len=7 span[header_value]="chunked"
off=73 headers complete method=3 v=1/1 flags=8 content_length=0
off=76 chunk header len=5
off=76 len=5 span[body]="hello"
off=83 chunk complete
off=86 chunk header len=6
off=86 len=6 span[body]=" world"
off=94 chunk complete
off=97 chunk header len=0
off=97 len=4 span[header_field]="Vary"
off=103 len=1 span[header_value]="*"
off=106 len=12 span[header_field]="Content-Type"
off=120 len=10 span[header_value]="text/plain"
off=134 chunk complete
off=134 message complete
```

### Parameters after chunk length

<!-- meta={"type": "request"} -->
```http
POST /chunked_w_unicorns_after_length HTTP/1.1
Transfer-Encoding: chunked

5; ilovew3;somuchlove=aretheseparametersfor
hello
6; blahblah; blah
 world
0

```

```log
off=0 message begin
off=5 len=32 span[url]="/chunked_w_unicorns_after_length"
off=48 len=17 span[header_field]="Transfer-Encoding"
off=67 len=7 span[header_value]="chunked"
off=78 headers complete method=3 v=1/1 flags=8 content_length=0
off=123 chunk header len=5
off=123 len=5 span[body]="hello"
off=130 chunk complete
off=149 chunk header len=6
off=149 len=6 span[body]=" world"
off=157 chunk complete
off=160 chunk header len=0
```

## Ignoring `pigeons`

`flags` should not be changed.

<!-- meta={"type": "request"} -->
```http
PUT /url HTTP/1.1
Transfer-Encoding: pigeons


```

```log
off=0 message begin
off=4 len=4 span[url]="/url"
off=19 len=17 span[header_field]="Transfer-Encoding"
off=38 len=7 span[header_value]="pigeons"
off=49 headers complete method=4 v=1/1 flags=0 content_length=0
off=49 message complete
```

## POST with `Transfer-Encoding: identity`

<!-- meta={"type": "request"} -->
```http
POST /post_identity_body_world?q=search#hey HTTP/1.1
Accept: */*
Transfer-Encoding: identity
Content-Length: 5

World
```

```log
off=0 message begin
off=5 len=38 span[url]="/post_identity_body_world?q=search#hey"
off=54 len=6 span[header_field]="Accept"
off=62 len=3 span[header_value]="*/*"
off=67 len=17 span[header_field]="Transfer-Encoding"
off=86 len=8 span[header_value]="identity"
off=96 len=14 span[header_field]="Content-Length"
off=112 len=1 span[header_value]="5"
off=117 headers complete method=3 v=1/1 flags=20 content_length=5
off=117 len=5 span[body]="World"
off=122 message complete
```

## Missing last-chunk

<!-- meta={"type": "request"} -->
```http
PUT /url HTTP/1.1
Transfer-Encoding: chunked

3
foo


```

```log
off=0 message begin
off=4 len=4 span[url]="/url"
off=19 len=17 span[header_field]="Transfer-Encoding"
off=38 len=7 span[header_value]="chunked"
off=49 headers complete method=4 v=1/1 flags=8 content_length=0
off=52 chunk header len=3
off=52 len=3 span[body]="foo"
off=57 chunk complete
off=57 error code=12 reason="Invalid character in chunk size"
```