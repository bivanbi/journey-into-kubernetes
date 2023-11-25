# Minikube Commands

## Cluster Operation
```commandline
minikube start
minikube stop
minikube delete
```

## Start Dashboard
* Open in browser window automatically
   ```shell
    minikube dashboard
    ```
* Print URL to console
    ```shell
    minikube dashbord -url
    ```

## Addons
```shell
minikube addons list

minikube addons enable <addon name>
minikube addons disable <addon name>
```

## Interacting with Minikube Cluster
* Open Service in Browser Window
  ```shell
  minikube service <service name>
  ```
* Run kubectl
  ```shell
  minikube kubectl -- <kubectl command>
  ```
