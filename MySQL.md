# MySQL


## kubctl exec -it mysql-8fd6bcc98-w74k2 -n wildwest -- mysql -u root --password=ThisIsCool webapp -e 'select * from users'

````

cat start.sql| kubectl exec -it mysql-8fd6bcc98-w74k2 bash -n wildwest
mysql -u root --password=ThisIsCool

boscp08@boscp08-dingo:~$ kubectl  exec -it mysql-8fd6bcc98-w74k2 -n wildwest -- mysql -u root --password=ThisIsCool webapp -e 'select * from users'
Warning: Using a password on the command line interface can be insecure.
+------+---------+---------+-----------------------+----------+
| ID   | fname   | lname   | job                   | register |
+------+---------+---------+-----------------------+----------+
|    1 | Kathryn | Janeway | Vice Admiral          | 0        |
|    2 | Steven  | of Nine | Chief Petty Officer   | 0        |
|    3 | B Elena | Torres  | Chief Engineer        | 0        |
|    4 | Nee     | Lix     | Advisor               | 0        |
|    5 | The     | Doctor  | Chief Medical Officer | 0        |
+------+---------+---------+-----------------------+----------+


````
##  go get "github.com/go-sql-driver/mysql

````
boscp08@boscp08-dingo:~/.../Go$ cat Dockerfile 
FROM golang:latest

RUN apt-get update
RUN apt-get install vim -y
RUN apt-get install nano -y
RUN go get "github.com/go-sql-driver/mysql"
````




## main.go
````
boscp08@boscp08-dingo:~/.../Go$ cat main.go
package main

import(
        _ "github.com/go-sql-driver/mysql"
        "database/sql"
        "fmt"
        "log"
)

func main() {
        cnn, err := sql.Open("mysql", "root:ThisIsCool@tcp(192.168.2.30:3306)/webapp")
        if err != nil {
                log.Fatal(err)
        }

        id := 1
        var name string

        if err := cnn.QueryRow("SELECT fname FROM users WHERE id = 1 LIMIT 1", id).Scan(&name); err != nil {
                log.Fatal(err)
        }

        fmt.Println(id, name)
}
````

````
boscp08@boscp08-dingo:~/.../Go$ go run main.go 
2020/04/24 18:09:33 Error 1044: Access denied for user 'root'@'192.168.0.0' to database 'webapp'
````
