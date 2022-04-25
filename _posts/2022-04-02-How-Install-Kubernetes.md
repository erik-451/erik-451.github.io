---
layout: post
title:  "How to install Kubernetes"
author: erik
categories: [ Devops ]
image: https://user-images.githubusercontent.com/47476901/165136193-93dbbe6a-ff9e-488e-93f1-1e014f08abf6.jpg
beforetoc: ""
toc: false
tags: [ Devops ]
---
Kubernetes es una plataforma portable y extensible de código abierto para administrar cargas de trabajo y servicios. Kubernetes facilita la automatización y la configuración declarativa.

# Table of Contents
1. [Requisitos entorno](#Requisitos)
2. [Configurar los logins SSH a las maquinas](#configmaquinasssh)
3. [Descargar k0sctl](#descargarK0sctl)
4. [Instalar kubernetes en las maquinas usando k0sctl](#instalarkuberneteshosts)<br>
5. [Cliente Kubernetes](#ClienteKubernetes)

---


## Requisitos entorno  <a name="Requisitos"></a>
- Minimum 2 machines

Mis maquinas virtuales
![VM](https://user-images.githubusercontent.com/47476901/165136286-5466bfdf-0ae2-4cbf-b358-dedf31eb9406.png)

Uso WSL como maquina para la instalacion.
- Minimo "4 GB RAM" preferiblemente 8-16GB para mayor fluidez

![wsl](https://user-images.githubusercontent.com/47476901/165136298-8ff550a2-ee8d-4383-8ab4-62792dfec457.png)

### Configurar los logins SSH a las maquinas <a name="configmaquinasssh"></a>
Usar  `ssh-copy-id root@192.168.20.134` para no tener que poner contraseñas en el login de ssh y facilitar la instalacion.

### Descargar k0sctl <a name="descargarK0sctl"></a>
Nos descargamos el software y creamos un fichero de configuracion
Y lo mandamos a un fichero donde lo configuraremos a nuestra manera. 
```bash
k0sctl init > k0sctl.yaml`
```
En mi caso es: **k0sctl.yaml**
```bash
apiVersion: k0sctl.k0sproject.io/v1beta1
kind: Cluster
metadata:
  name: k0s-cluster
spec:
  hosts:
  - ssh:
      address: 192.168.20.134
      user: root
      port: 22
      keyPath: /home/erik/.ssh/id_rsa
    role: controller+worker
    privateInterface: ens33
  - ssh:
      address: 192.168.20.135
      user: root
      port: 22
      keyPath: /home/erik/.ssh/id_rsa
    role: worker
    privateInterface: ens33
  k0s:
    version: 1.23.5+k0s.0
```

### Instalar kubernetes en las maquinas usando k0sctl <a name="instalarkuberneteshosts"></a>
Ya tenemos todo listo, corremos el programa que nos configura la instalacion automatizando el proceso

```bash
k0sctl apply --config k0sctl.yaml
```

![Installing](https://user-images.githubusercontent.com/47476901/165136327-31092a45-200b-4694-a202-f449beabad63.png)


### Instalar cliente de kubernetes en nuestra maquina <a name="instalarkuberneteshosts"></a>
```bash
sudo apt install kubernetes-client
```
Mandamos la configuracion de kubernetes a un fichero llamado kubeconfig 
```bash
k0sctl kubeconfig > kubeconfig
```

Para que se use esta configuracion indicamos con la variable KUBECONFIG donde se ubica
```bash
export KUBECONFIG=/opt/kubernetes/kubeconfig
```
### Cliente Kubernetes <a name="ClienteKubernetes"></a>
**Listar los nodos activos usamos:**
```bash
kubectl get nodes
```
Comprobamos que ya estan activos
![list-nodes](https://user-images.githubusercontent.com/47476901/165136351-c17db351-0422-4309-a240-de7f94005476.png)


**Listar informacion del cluster al que estamos conectados:**

```bash
kubectl cluster-info
```
![cluster-info](https://user-images.githubusercontent.com/47476901/165136364-97c99f98-ae7e-480a-8a42-fe86a51335b9.png)


**Listar informacion detallada de todos los nodos:**
Podemos ver informacion como el tipo de sistema operativo, el uso cpu, ram, la configuracion del nodo....

```bash
kubectl describe node
```

**Especificar descripcion de un nodo:**

```bash
kubectl describe node kubernetes-1 
```
![node-info](https://user-images.githubusercontent.com/47476901/165136388-0ca6419e-1d8b-4324-9ea2-28f0e9f6ba17.png)


**Breve resumen del hardware:**
```bash
kubectl top node
```
![hardware-info](https://user-images.githubusercontent.com/47476901/165136407-41c99a3d-127c-477b-8936-871793002979.png)
