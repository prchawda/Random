**Create a postgres instance in k8s ( on kind cluster )**

Important link : <https://kodekloud.com/blog/deploy-postgresql-kubernetes/>

Create a ns

List the storage class of your kindcluster ( if it is managed cloud cluster the just following the link above is enough )

Create a pvc:

apiVersion: v1

kind: PersistentVolumeClaim

metadata:

 name: test

 labels:

 # insert any desired labels to identify your claim

 app: test

spec:

 storageClassName: standard

 accessModes:

 - ReadWriteOnce

 resources:

 requests:

 # The amount of the volume's storage to request

 storage: 2Gi

Create postures secret using this 

kubectl create secret generic postgres-secret --from-literal=password=12345 -n postgress

Create statefulset, po, sec for postures with below yml

apiVersion: v1

kind: Service

metadata:

 name: postgres 

 labels:

 app: postgres

spec:

 selector:

 app: postgres

 ports:

 - port: 5432 

 clusterIP: None

---

apiVersion: apps/v1

kind: StatefulSet

metadata:

 name: postgres

spec:

 serviceName: postgres

 replicas: 1

 selector:

 matchLabels:

 app: postgres

 template:

 metadata:

 labels:

 app: postgres

 spec:

 containers:

 - name: postgres

 image: postgres:13

 env:

 - name: POSTGRES_USER 

 value: postgres

 - name: POSTGRES_PASSWORD

 valueFrom:

 secretKeyRef:

 name: postgres-secret

 key: password

 - name: PGDATA

 value: /var/lib/postgresql/data/pgdata

 ports:

 - containerPort: 5432

 name: postgres

 volumeMounts:

 - name: test

 mountPath: /var/lib/postgresql/data

 volumeClaimTemplates:

 - metadata:

 name: test

 spec:

 accessModes:

 - ReadWriteOnce

 resources:

 requests:

 storage: 2Gi
