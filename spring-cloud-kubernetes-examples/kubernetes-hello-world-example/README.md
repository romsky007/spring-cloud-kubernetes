# Hello World Example

This Spring Boot application exposes an endpoint that we can call to receive a `Hello World` message as response. The application is configured using the 
@RestController and the method returning the message is annotated with `@RequestMapping("/")` 

The uberjar of the Spring Boot application is packaged within a Docker image using the [Fabric8 Maven plugin](maven.fabric8.io) and next deployed top of the Kubernetes management platform as a pod 
using the replication controller created by the plugin.

To play with the example, it is required to have access to a Kubernetes Management Platform which is available on GCE. If you don't have an account on GCE,
you can install locally Kubernetes & Docker using `[Minikube](https://kubernetes.io/docs/getting-started-guides/minikube/)` within a Virtual Machine
managed by a hypervisor (Xhyve, Virtualbox or KVM) if your machine is not a native Unix operating system.

The script to install `minikube` is 
```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.17.1/minikube-darwin-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
```
  
When the client is installed on your machine, you can start kubernetes using this command:
```
minikube start
```

Next, you can play with this Spring Boot application in the cloud using the following maven command to deploy it:
```
mvn clean package fabric8:deploy -Pkubernetes
```  
   
When the application has been deployed, you can access its service or endpoint url using this command:
```   
minikube service kubernetes-hello-world --url
```
  
And next you can curl the endpoint using the url returned by the previous command

``` 
curl http://IP_OR_HOSTNAME/
```
     
## Integration test

To deploy the resources required on Kubernetes/OpenShift and to test if the service deployed can answer to requests, we will use the [Arquillian Kubernetes Cube](https://github.com/arquillian/arquillian-cube/blob/master/docs/kubernetes.adoc) framework. 
This testing framework uses the Kubernetes Java API to communicate with the platform, deploy the resources (pod, service, deployment, ...) generated by the Fabric8 maven plugin.
When the service becomes available, the method defines within thr Junit test will call the endpoint to verify that it receives as response the string
`Hello World`

Remark : If you run the integration test against OpenShift, create first the namespace/project `it` that arquillian will use to deploy the resources using the oc client 
`oc new-project it`.

When you are logged to the OpenShift platform, execute the following maven command to run the Integration Test against OpenShift   

``` 
mvn clean install -Pintegration
```