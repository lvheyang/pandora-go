# Pandora Golang API client

This is based on https://github.com/dougEfresh/logzio-go. Thanks to @dougEfresh

Sends logs to [pandora 2.0](https://portal.qiniu.com/express) over HTTP. It is a low level lib that can to be integrated with other logging libs.


## Prerequisites
go 1.x

## Installation
```shell
$ go get -u github.com/lvheyang/pandora-go
```

## Quick Start

```go
package main

import (
	"github.com/lvheyang/pandora-go"
	"os"
	"time"
)

func main() {
	l, err := pandora.New(
		"fake-token",
		pandora.SetDebug(os.Stderr),
		pandora.SetUrl("http://pandora-express-rc.qiniu.io/api/v1/data"),
		pandora.SetDrainDuration(time.Second*10),
		pandora.SetTempDirectory("myQueue"),
		pandora.SetDrainDiskThreshold(99)) // token is required
	if err != nil {
		panic(err)
	}
	msg := pandora.PandoraReqBody{
		Raw:         "{\"a\":\"lllllll\"}", //必填，原始日志
		SourceType:  "json", //必填，来源类型
		Repo:        "default", //必填，仓库
		Host:        "", //选填，Host地址
		Origin:      "", //选填，来源
		Timestamp:   0,  //选填，事件时间
		CollectTime: 0,  //选填，收集时间
	}

	err = l.SendData(msg)
	if err != nil {
		panic(err)
	}

	l.Stop() //logs are buffered on disk. Stop will drain the buffer
}

```

## Usage

- Set url mode:
    `pandora.New(token, SetUrl(ts.URL))`

- Set drain duration (flush logs on disk):
    `pandora.New(token, SetDrainDuration(time.Hour))`

- Set debug mode:
    `pandora.New(token, SetDebug(os.Stderr))`

- Set queue dir:
    `pandora.New(token, SetSetTempDirectory(os.Stderr))`

- Set the sender to check if it crosses the maximum allowed disk usage:
    `pandora.New(token, SetCheckDiskSpace(true))`

- Set disk queue threshold, once the threshold is crossed the sender will not enqueue the received logs:
    `pandora.New(token, SetDrainDiskThreshold(99))`

## Disk queue

Pandora go client uses [goleveldb](https://github.com/syndtr/goleveldb) and [goqueue](github.com/beeker1121/goque) as a persistent storage.
Every 5 seconds logs are sent to logz.io (if any are available)

## Tests

```shell
$ go test -v

```


See [travis.yaml](.travis.yml) for running benchmark tests


## Contributing
 All PRs are welcome

## Authors

* **Douglas Chimento**  - [dougEfresh][me]
* **Ido Halevi**  - [idohalevi](https://github.com/idohalevi)


## License

This project is licensed under the Apache License - see the [LICENSE](LICENSE) file for details
