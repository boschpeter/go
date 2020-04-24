# go version go1.11 linux/amd64




[install-go-1-11-on-ubuntu-18-04-16-04-lts-8c098c503c5f](https://medium.com/better-programming/install-go-1-11-on-ubuntu-18-04-16-04-lts-8c098c503c5f)

 
|step| go version go1.11 linux/amd64
|---|----------------------------------------------|
|1|`sudo apt-get update`|
|2|`sudo apt-get -y upgrade`|
|3|`cd /tmp`|
|4|`wget https://dl.google.com/go/go1.11.linux-amd64.tar.gz`|
|5|`sudo tar -xvf go1.11.linux-amd64.tar.gz`|
|6|`sudo mv go /usr/local/bin`|
|7|`export GOROOT=/usr/local/bin/go`|
|8|`export GOPATH=$HOME/go`|
|9|`export PATH=$GOPATH/bin:$GOROOT/bin:$PATH`|
|10|`source ~/.profile`|
|11|`go version`|

nano .bashrc
````
GOROOT=/usr/local/bin/go
GOPATH=$HOME/go
export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
````


https://qiita.com/osk_kamui/items/1539ade3c23f58b89f80

## go get github.com/go-sql-driver/mysql

````
package main

import(
        _ "github.com/go-sql-driver/mysql"
        "database/sql"
        "fmt"
        "log"
)

func main() {
        cnn, err := sql.Open("mysql", "docker:docker@tcp(db:3306)/test_db")
        if err != nil {
                log.Fatal(err)
        }

        id := 1
        var name string

        if err := cnn.QueryRow("SELECT name FROM test_tb WHERE id = ? LIMIT 1", id).Scan(&name); err != nil {
                log.Fatal(err)
        }

        fmt.Println(id, name)
}



````
