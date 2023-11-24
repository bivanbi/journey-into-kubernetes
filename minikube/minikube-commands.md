# Minikube Commands

## Cluster Operation
```commandline
minikube start
minikube stop
minikube delete
```

## Start Dashboard
* Open in browser window automatically
   ```commandline
    minikube dashboard
    ```
* Print URL to console
    ```commandline
    minikube dashbord -url
    ```

## Addons
```commandline
minikube addons list

minikube addons enable <addon name>
minikube addons disable <addon name>
```

## Interacting with Minikube Cluster
* Open Service in Browser Window
  ```commandline
  minikube service <service name>
  ```
* Run kubectl
  ```commandline
  minikube kubectl -- <kubectl command>
  ```
