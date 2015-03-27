fscache 
==========

[![GoDoc](https://godoc.org/github.com/djherbis/fscache?status.svg)](https://godoc.org/github.com/djherbis/fscache)
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg)](LICENSE.txt)
[![Build Status](https://travis-ci.org/djherbis/fscache.svg?branch=master)](https://travis-ci.org/djherbis/fscache)
[![Coverage Status](https://coveralls.io/repos/djherbis/fscache/badge.svg?branch=master)](https://coveralls.io/r/djherbis/fscache?branch=master)

Usage
------------
Streaming File Cache for #golang

```go
package main

import (
	"io"
	"log"
	"os"

	"github.com/djherbis/fscache"
)

func main() {

	// create the cache, keys expire after 1 hour.
	c, err := fscache.New("./cache", 0755, 1)
	if err != nil {
		log.Fatal(err.Error())
	}
	
	// wipe the cache when done
	defer c.Clean()

	// Get() and it's streams can be called concurrently but just for example:
	for i := 0; i < 3; i++ {
		r, w, err := c.Get("stream")
		if err != nil {
			log.Fatal(err.Error())
		}

		if w != nil { // a new stream, write to it.
			go func(){
				w.Write([]byte("hello world\n"))
				w.Close()
			}()
		}

		// the stream has started, read from it
		io.Copy(os.Stdout, r)
		r.Close()
	}
}
```

Installation
------------
```sh
go get github.com/djherbis/fscache
```
