Muhammad Ilham Zikri - 2206083533
Adpro - A
# kubernetes

### Reflections
1. ***Compare the application logs before and after you exposed it as a Service. Try to open the app several times while the proxy into the Service is running. What do you see in the logs? Does the number of logs increase each time you open the app?***
Dari log dapat dipahami bahwa sebelum meng*expose* aplikasinya sebagai service, aplikasinya diakses secara langsung di dalam pod. Log menunjukkan pesan-pesan awal (start server HTTP on port 8080) dan setiap permintaan masuk (GET /). Setiap kali aplikasi di access di dalam pod, dibuat entri log untuk request tersebut. Setelah menjadikan aplikasi sebagai service menggunakan `minikube service hello-node`, log tetap menunjukkan pesan-pesan awal dan permintaan masuk seperti sebelumnya namun sekarang aplikasi diakses melalui service, yang meneruskan traffic ke pod. Mengakses aplikasi melalui service memungkinkan akses eksternal ke aplikasi, sementara mengaksesnya di dalam pod lebih bersifat internal dalam cluster Kubernetes.
![image](https://github.com/ilhamzik/kubernetes/assets/124953758/2bbe7475-44c4-4bb0-a6f8-7ffbf267641b)

2. ***Notice that there are two versions of kubectl get invocation during this tutorial section. The first does not have any option, while the latter has -n option with value set to `kube-system`. What is the purpose of the -n option and why did the output not list the pods/services that you explicitly created?***
   Opsi -n dalam perintah kubectl get digunakan untuk menentukan namespace di mana resource akan ditampilkan. Jika tidak menyertakan opsi `-n`, kubectl get akan menampilkan resource dari namespace default. Pada pemanggilan yang kemudian, kubectl get digunakan dengan opsi `-n kube-system`, yang memberitahukannya untuk menampilkan resource dari namespace kube-system secara khusus. Namespace ini berisi system component dan infrastructure dari Kubernetes itu sendiri, seperti layanan inti sistem seperti DNS, server API, dan dashboard Kubernetes.

## Rolling Update & Kubernetes Manifest File

1. ***What is the difference between Rolling Update and Recreate deployment strategy?***
Perbedaan antara strategi Rolling Update dan Recreate Deployment terletak pada bagaimana mereka mengelola pembaruan aplikasi dan downtime yang terjadi selama proses tersebut. Dalam Recreate Deployment, terjadi downtime karena semua pod dari versi aplikasi sebelumnya dihentikan sebelum pod baru dengan versi terbaru dibuat. Ini berarti bahwa selama periode antara penghapusan pod lama dan pembuatan pod baru, aplikasi tidak tersedia. Di sisi lain, Rolling Update menghindari downtime dengan melakukan pembaruan secara bertahap. Pod baru dengan versi terbaru dari aplikasi dibuat satu per satu, sementara pod lama tetap berjalan. Dengan demikian, Rolling Update memastikan bahwa aplikasi tetap online selama proses pembaruan tanpa waktu tidak tersedianya.

2. ***Try deploying the Spring Petclinic REST using Recreate deployment strategy and document your attempt.***
Untuk mengapply Recreate Deployment Strategy, saya mengapply manifest file dengan strategy type berbeda dan menggunakan manifest file service yang sama dengan tutorial sebelumnya.
![image](https://github.com/ilhamzik/kubernetes-tutorial/assets/124953758/3528fd90-4e6d-4a81-b882-f80f3df7bb2e)
![image](https://github.com/ilhamzik/kubernetes-tutorial/assets/124953758/dc6060ff-b4e3-49ef-a928-547b07e4f315)
![image](https://github.com/ilhamzik/kubernetes-tutorial/assets/124953758/44291f12-d311-4e25-9a60-f4e641701a6c)




4. ***Prepare different manifest files for executing Recreate deployment strategy.***

    Manifest filenya yang saya namakan `deployment.yaml` yang baru saya buat berdasarkan `deployment.yaml` pada tutorial.
    `deployment.yaml`:
    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
    annotations:
        deployment.kubernetes.io/revision: "4"
    creationTimestamp: "2024-05-14T06:09:33Z"
    generation: 5
    labels:
        app: spring-petclinic-rest
    name: spring-petclinic-rest
    namespace: default
    resourceVersion: "11611"
    uid: 14543249-9677-49c0-9d47-ddcb76cd4558
    spec:
    progressDeadlineSeconds: 600
    replicas: 4
    revisionHistoryLimit: 10
    selector:
        matchLabels:
        app: spring-petclinic-rest
    strategy:
        type: Recreate
    template:
        metadata:
        creationTimestamp: null
        labels:
            app: spring-petclinic-rest
        spec:
        containers:
        - image: docker.io/springcommunity/spring-petclinic-rest:3.2.1
            imagePullPolicy: IfNotPresent
            name: spring-petclinic-rest
            resources: {}
            terminationMessagePath: /dev/termination-log
            terminationMessagePolicy: File
        dnsPolicy: ClusterFirst
        restartPolicy: Always
        schedulerName: default-scheduler
        securityContext: {}
        terminationGracePeriodSeconds: 30
    status:
    availableReplicas: 4
    conditions:
    - lastTransitionTime: "2024-05-14T06:17:13Z"
        lastUpdateTime: "2024-05-14T06:17:13Z"
        message: Deployment has minimum availability.
        reason: MinimumReplicasAvailable
        status: "True"
        type: Available
    - lastTransitionTime: "2024-05-14T06:09:33Z"
        lastUpdateTime: "2024-05-14T06:42:25Z"
        message: ReplicaSet "spring-petclinic-rest-54f476f68" has successfully progressed.
        reason: NewReplicaSetAvailable
        status: "True"
        type: Progressing
    observedGeneration: 5
    readyReplicas: 4
    replicas: 4
    updatedReplicas: 4
    ```
5. ***What do you think are the benefits of using Kubernetes manifest files? Recall your experience in deploying the app manually and compare it to your experience when deploying the same app by applying the manifest files (i.e., invoking `kubectl apply -f` command) to the cluster.***
Menggunakan manifest files membuat proses deployment lebih terstandarisasi dan terdokumentasi secara deklaratif, mengurangi risiko kesalahan manusia karena tidak perlu mengingat prosedur secara spesifik. Manifest files juga memfasilitasi pengelolaan konfigurasi aplikasi secara efisien, menghilangkan kekhawatiran akan setup manual yang memakan waktu bagi pengguna.

6. ***(Optional) Do the same tutorial steps, but on a managed Kubernetes cluster (e.g., GCP). You need to provision a Kubernetes cluster on Google Cloud Platform. Then, re-run the tutorial steps (Hello Minikube and Rolling Update) on the remote cluster. Document your attempt and highlight the differences and any issues you encountered.***
