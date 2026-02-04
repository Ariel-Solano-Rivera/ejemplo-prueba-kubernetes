# Práctica Kubernetes con Minikube (driver Docker) — Paso a paso (solo comandos)

## 0) Iniciar Minikube y verificar Kubernetes

> Abre **PowerShell** (Windows) y ejecuta:

```powershell
minikube start --driver=docker
```

**¿Qué hace?**  
Inicia un clúster Kubernetes local usando Docker como motor (driver).

Verifica que el nodo esté listo:

```powershell
kubectl get nodes
```

✅ Debe salir el nodo en estado **Ready**.

---

## 1) Crear el Deployment (imagen nginxdemos/hello:latest) con 1 pod

Crear el deployment:

```powershell
kubectl create deployment nginx-demo --image=nginxdemos/hello:latest
```

**¿Qué hace?**  
Crea un **Deployment** llamado `nginx-demo` que levanta pods con la imagen indicada.

Verificar deployment y pods:

```powershell
kubectl get deployments
kubectl get pods
```

✅ Debes ver el deployment creado y **1 pod** en **Running** (al inicio).

---

## 2) Exponer el Deployment como Service

### 2.1 Crear Service tipo ClusterIP (inicialmente)

```powershell
kubectl expose deployment nginx-demo --port=80 --target-port=80 --type=ClusterIP
```

**¿Qué hace?**  
Crea un **Service ClusterIP**, accesible **solo dentro** del clúster.

Verificar:

```powershell
kubectl get services
```


---

### 2.2 Confirmar que responde dentro del clúster (port-forward)

```powershell
kubectl port-forward service/nginx-demo 8080:80
```

**¿Qué hace?**  
“Puentea” el puerto del Service (80) a tu PC (8080) para probar desde tu navegador.

Abre:

- http://localhost:8080

✅ Deberías ver la página de `nginxdemos/hello`.


---

### 2.3 Modificar el Service a NodePort (PowerShell recomendado: edit)

> En PowerShell, el `kubectl patch` suele fallar por comillas/JSON.  
> La forma más segura es **editar** el Service.

```powershell
kubectl edit service nginx-demo
```

Se abrirá un editor. Busca:

```yaml
spec:
  type: ClusterIP
```

Y cambia a:

```yaml
spec:
  type: NodePort
```

Guarda y cierra el editor.

Verifica:

```powershell
kubectl get services
```

✅ Debe salir algo como `80:30xxx/TCP` (ese `30xxx` es el NodePort).

---

### 2.4 Validar acceso desde fuera del clúster

```powershell
minikube service nginx-demo
```

**¿Qué hace?**  
Abre el navegador con la URL del servicio (NodePort) lista para usar.

---

## 3) Escalado del Deployment a 4 réplicas

```powershell
kubectl scale deployment nginx-demo --replicas=4
```

**¿Qué hace?**  
Aumenta la cantidad de pods del Deployment a **4**.

Verifica:

```powershell
kubectl get pods
```

✅ Deben aparecer **4 pods** en **Running**.


---

## 4) Actualización del Deployment (cambiar a nginx:alpine)

### 4.1 Ver el nombre real del contenedor (IMPORTANTE)

> Para `kubectl set image`, necesitas el **nombre del contenedor** (no el del deployment).

```powershell
kubectl get deployment nginx-demo -o jsonpath="{.spec.template.spec.containers[*].name}"
```

✅ En tu caso, esto te devolvió: `hello`


---

### 4.2 Cambiar imagen a nginx:alpine

Como tu contenedor se llama `hello`, el comando correcto es:

```powershell
kubectl set image deployment/nginx-demo hello=nginx:alpine
```

**¿Qué hace?**  
Actualiza la imagen del contenedor `hello` a `nginx:alpine` dentro del Deployment.

---

### 4.3 Observar el proceso de actualización (rolling update)

```powershell
kubectl rollout status deployment nginx-demo
```

✅ Debe decir algo como: `successfully rolled out`


---

### 4.4 (Opcional recomendado) Confirmar la imagen final

```powershell
kubectl get deployment nginx-demo -o jsonpath="{.spec.template.spec.containers[*].image}"
```

✅ Debe salir: `nginx:alpine`


---

### 4.5 Verificar diferencias por HTTP (navegador)

```powershell
minikube service nginx-demo
```

**¿Qué debes notar?**  
- `nginxdemos/hello` → página con info del contenedor / demo
- `nginx:alpine` → página por defecto de NGINX (simple)

📸 **Captura sugerida:** la nueva página (nginx alpine)

---


## (Opcional) Limpieza de recursos al final

Si quieres borrar todo:

```powershell
kubectl delete service nginx-demo
kubectl delete deployment nginx-demo
```

---

---

## 📌 Apartado extra: Comandos útiles en Minikube y Kubernetes

Este apartado contiene comandos adicionales que pueden servir para la administración, monitoreo y pruebas de aplicaciones desplegadas en Kubernetes usando Minikube.

---

### 🔹 Comandos básicos de Minikube

```powershell
minikube start --driver=docker

minikube dashboard

kubectl get nodes

kubectl create deployment miwev --image=nginx

kubectl get pods

kubectl get deployments

kubectl scale deployment miwev --replicas=3

kubectl expose deployment miwev --type=NodePort --port=80

minikube service miwev --url

kubectl delete pod miwev-69f785cf66-7glbc

kubectl logs miwev-69f785cf66-vdzbc

kubectl delete deployment web2

--------balacneo de carga --------

kubectl create deployment web2 --image=paulbouwer/hello-kubernetes:1.10

kubectl scale deployment web2 --replicas=3

kubectl get pods

kubectl get deployments

kubectl expose deployment web2 --type=NodePort --port=8080

minikube service web2 --url

kubectl rollout status deployment/web2

kubectl get pods -w

kubectl set image deployment/web2 hello-kubernetes=paulbouwer/hello-kubernetes:1.5



kubectl get pods --show-labels

kubectl describe pod web2-79c5b8c6bc-zmdrw 





```
