# MySQL

## kubectl exec -it mysql-8fd6bcc98-w74k2 -n wildwest  bash

### mysql -u root --password=ThisIsCool

## kubectl exec -it mysql-8fd6bcc98-w74k2 -n wildwest -- mysql -u root --password=ThisIsCool webapp -e 'select * from users'

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


#  Grant access by editing /etc/mysql/mysql.conf.d/mysqld.cnf 
Grant access by editing /etc/mysql/mysql.conf.d/mysqld.cnf ```` Disable the line with bind```
http://dev.mysql.com/doc/mysql/en/server-system-variables.html


```` Disable the line with bind````

````
[mysqld]    
bind-address = 0.0.0.0
````
## kubectl delete -f mysql.yaml
````
pi@k8s-head:~/mysql $ kubectl delete -f mysql.yaml
secret "mysql" deleted
deployment.apps "mysql" deleted
service "mysqlserver" deleted

````

````
pi@k8s-head:~/mysql $ cat mysql.yaml
#echo -n "passwd1" | base64
#
##echo -n "ThisIsCool" |base64
##VGhpc0lzQ29vbA==
#
#apiVersion: v1
#kind: Namespace
#metadata:
#  name: student34
#---
apiVersion: v1
kind: Secret
metadata:
  name: mysql
  namespace: wildwest
type: Opaque
data:
  password: VGhpc0lzQ29vbA==
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
  namespace: wildwest
  labels:
    app: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
        - image: mysql:5.6
          name: mysql
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql
                  key: password
          ports:
            - containerPort: 3306
              name: mysql
          volumeMounts:
            - name: mysql-persistent-storage
              mountPath: /var/lib/mysql
      volumes:
        - name: mysql-persistent-storage
          persistentVolumeClaim:
          emptyDir: {}
          #claimName: mysql-server
      nodeSelector:
         processortype: amd

---
apiVersion: v1
kind: Service
metadata:
  name: mysqlserver
  namespace: wildwest
  labels:
    k8s-app: mysql-server
spec:
  selector:
    app: mysql
  ports:
  - port: 3306
    targetPort: 3306
    protocol: TCP
    name: http
  externalIPs:
    - 192.168.2.30

````
