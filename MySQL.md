#### Execute the following to get the administrator credentials:

 ```shell
  echo Username: root
  MYSQL_ROOT_PASSWORD=$(kubectl get secret --namespace default mysql-1634466937 -o jsonpath="{.data.mysql-root-password}" | base64 --decode)
  ```

#### To connect to your database:

  1. Run a pod that you can use as a client:

      ```shell
      kubectl run mysql-1634466937-client --rm --tty -i --restart='Never' --image  docker.io/bitnami/mysql:8.0.26-debian-10-r60 --namespace default --command -- bash
      ```

  2. To connect to primary service (read/write):

      ```shell
      mysql -h mysql-1634466937.default.svc.cluster.local -uroot -p"$MYSQL_ROOT_PASSWORD"
      ```



#### To upgrade this helm chart:

  1. Obtain the password as described on the 'Administrator credentials' section and set the 'root.password' parameter as shown below:

      ```shell
      ROOT_PASSWORD=$(kubectl get secret --namespace default mysql-1634466937 -o jsonpath="{.data.mysql-root-password}" | base64 --decode)
      helm upgrade --namespace default mysql-1634466937 bitnami/mysql --set auth.rootPassword=$ROOT_PASSWORD```



  