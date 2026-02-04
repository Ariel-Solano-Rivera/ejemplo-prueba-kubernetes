Entrega alternativa: MongoDB + Productor (Python) + Mongo Express

- Namespace: monitoreo
- BD: MongoDB (mongo:7) con PVC
- Seguridad: credenciales en Secret (usuario/clave obligatorios)
- Productor: python:3.12-alpine insertando JSON cada 3 segundos en:
  - BD: monitoreo
  - Colección: lecturas
- Cliente web: mongo-express (NodePort 30082)

Archivos:
- k8s/manifests/00-namespace.yml
- k8s/manifests/01-secreto-mongodb.yml
- k8s/manifests/02-mongodb-persistente.yml
- k8s/manifests/03-productor.yml
- k8s/manifests/04-cliente-web.yml

Ejecuta los pasos en COMANDOS.txt
