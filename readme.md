## Datos previos - chequear instalaciones
``````
cat /etc/os-release
``````
VPS "Ubuntu 22.04.1 LTS" sobre la que estamos trabajando
``````
docker --version
``````
Docker version 24.0.4,
``````
kubectl version --output=json --client
``````
Client Version: v1.27.4
``````
Minikube version
``````
minikube version: v1.31.1
To check ports being used (8080 for example)
``````
sudo ss -tuln | grep 8080
``````


Finalmente clonamos repo de Cami - Grafana Example - y tomamos archivos de jenkins y docker-compose



## Levantar un container de jenkins, sonarqube y nexus


``````
minikube start

``````
(o se puede chequear si está corriendo con minikube status)

``````
docker compose up -d
`````` 
Habilitar un shell interactivo dentro de un contenedor Docker:

````` 
docker exec -it --user=root jenkinsdocker /bin/bash
`````` 

E instalar kubectl al interior de este:

Install kubectl from https://k8s-docs.netlify.app/en/docs/tasks/tools/install-kubectl/
````` 
curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
````` 
````` 
chmod +x ./kubectl
````` 
````` 
sudo mv ./kubectl /usr/local/bin/kubectl
````` 
````` 
kubectl version --client
````` 

## Acceder a Jenkins 
Desde la vps se pude chequear que jenkins está corriendo con este comando
````` 
ps aux | grep jenkins
````` 
Del que se obtiene esta línea
````` 
root 7 3.3 30.9 3621548 1244840 ? Sl 15:19 3:23 java -Duser.home=/var/jenkins_home -Djenkins.model.Jenkins.slaveAgentPort=50000 -Dhudson.lifecycle=hudson.lifecycle.ExitLifecycle -jar /usr/share/jenkins/jenkins.war
````` 
The output shows that there is a Java process (identified by java) running as PID 7, which corresponds to Jenkins. It is executing the Jenkins web application using the jenkins.war file located at /usr/share/jenkins/jenkins.war. The process is owned by the root user.

The presence of this process confirms that Jenkins is running on your system.

## Acceder desde el VPS

Ahora se puede acceder a Jenkins desde la ruta de Minikube, en este caso con la ip del VPS: 

http://45.236.129.205:8080/

## Este paso de instalación inicial ya está hecho en esta imagen, pero es esto:
Instalación inicial. Necesitamos poner la clave inicial: 
Ruta var/jenkins_home/secrets/initialAdminPassword 

Si este archivo no se ha creado en la ruta en cuestión, obtenerlo utilizando los siguientes comandos (dentro del contenedor, donde aún estamos): 
```console
cat var/jenkins_home/secrets/initialAdminPassword
```
Instalar plugins predefinidos -> Kubernetes plugin (integrates Jenkins with Kubernetes)

Salir del contenedor: 
```console
exit
```

## Conectar instalación de Docker a Minikube 

Visualizar redes presentes en minikube
```console
docker network ls
```

Nos interesa es la red de minikube.

Conectar el contenedor de jenkins a esta red.
```console
docker network connect minikube jenkinsdocker
```

Comprobamos que se ha agregado de manera correcta
```console
docker container inspect jenkinsdocker
```

## Desconectar Docker de Minikube
Cuando terminemos este ejercicio no olvides desconectar este contenedor a esa red, dado que si no inicia el minikube con su red y el contenedor conectado a esa red podria tener problemas al querer iniciar de manera independiente.

Comando para desconectarlo de la red

```console
docker network disconnect minikube jenkinsdocker
```

-----