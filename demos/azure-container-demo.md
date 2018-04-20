# Azure Container Service demo (AKS)
---
## Đăng nhập Azure dùng Azure Pass
Bạn vui lòng đăng ký với GAB2018 organizer để có Azure Pass, sau đó đăng nhập vào trang Azure Portal.

## Cài đặt trước các phần mềm
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Docker](https://docs.docker.com/install/)
- [dotnet SDK 2.0 (hoặc 2.1 preview)](https://www.microsoft.com/net/download)
- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## Bước 1 - Tạo AKS cluster
### Đăng nhập vào Azure
```
$ az login
```
Copy URL được sinh ra và mở URL lên browser, sau đó nhập vào code (cũng được sinh ra từ console), sau khi đăng nhập xong thì đóng của sổ trình duyệt lại

### Tạo resource group
Hầu hết tất cả service trên Azure sẽ cần một resource group, do đó, cần phải tạo nó trước.
```bash
$ az group create --name gab2018 --location eastus
```

### Tạo Azure Kubernetes Service (AKS Cluster)
```
$ az aks create --name kcluster --resource-group gab2018 --node-count 1 --generate-ssh-keys
```

Chờ khoảng 5 phút chúng ta sẽ có được một cluster với tên ```kcluster``` với 1 node.

Để kiểm tra cluster đã tạo xong chưa, dùng lệnh:

```
$ az aks get list --output table
```

### Kết nối đến cluster
Bạn phải cài **kubectl** để có thể thao tác đến Kubernetes cluster, nếu đã cài rồi thì bỏ qua step này, không thì bạn cần chạy lệnh:

```
$ az aks install-cli
```
Sau đó thì có thể kết nối đến cluster:

```
$ az aks get-credentials --name kcluster --resource-group gab2018
```

Kiểm tra xem clusters có bao nhiêu node. Nếu bạn để ý ở trên với tham số **--node-count 1**, nghĩa là cluster hiện tại chỉ có một workder node. Microsoft sẽ tính tiền worker node, không tính tiền manager node.

```
$ kubectl get nodes
```

Để kiểm tra chắc chắn là **kubectl** đang kết nối đến cluster của mình, chạy lệnh sau để kiểm tra, bạn sẽ thấy kubectl đang kết nối đến master **kcluster-azurebootcam**:
```bash
$ kubectl cluster-info
Kubernetes master is running at https://kcluster-azbootcamp-d44916-ce790490.hcp.eastus.azmk8s.io:443
```

## Bước 2 - Tạo ứng dụng

### Tạo ứng dụng webapi trên dotnet core
Trong ví dụ này, chúng ta sẽ tạo ra một ứng dụng hello-world bằng dotnet core, sau đó sẽ deploy lên AKS cluster. Chú ý là bạn cần phải cài dotnet sdk trước để có thể chạy dotnet core application.
 
Tạo ứng dụng dụng aspnet core (webapi), sau đó chạy ứng dụng lên.
```
$ mkdir gab2018 && cd gab2018
$ dotnet new webapi
$ dotnet run
```

Bạn có thể dùng browser để xem thử ở địa chỉ http://localhost:5000/api/values.

### Build Docker image
Chúng ta cần cài đặt Docker để có thể sử dụng docker cli. Để build image, chúng ta sẽ cần tạo một **Dockerfile** để khai báo các lệnh build. 
Nội dung file **Dockerfile** như sau, bạn lưu ý là phải để ở thư mục gốc của ứng dụng webapi.

```bash
FROM microsoft/aspnetcore:2.0.6
RUN apt-get update
# RUN apt-get -y install curl unzip

# RUN curl -sSL https://aka.ms/getvsdbgsh | bash /dev/stdin -v latest -l ~/vsdbg
# RUN apt-get update && apt-get install -y procps

COPY ./publish /app
WORKDIR /app

ENTRYPOINT ["dotnet", "gab2018.dll"]

```

Sau khi đã tạo Dockerfile, chạy tiếp ệnh bên dưới sẽ build image có với tên là **webapi**, và sau đó sẽ tạo một container trên image này bằng lệnh **docker run** bên dưới.

```
$ docker build -t gab2018 .
$ docker run --name gab2018 gab2018
```

### Push image to Azure Container Registry
Tạo Azure Container Registry (ACR), sau đó đăng nhập vào ACR:
```
$ az acr create --resource-group gab2018 --name kregistry --sku Basic
$ az acr login --name kregistry
```
Tagging trước khi push lên ACR:
```
$ docker tag gab2018 kregistry.azurecr.io/gab2018
```
Push image lên ACR:
```
$ docker push kregistry.azurecr.io/gab2018
```

Kiểm tra lại xem image đã lên ACR chưa:
```
$ az acr repository list --name kregistry --output table
```

## Bước 3 - Deploy lên AKS

### Kết nối AKS & ACR
Để deploy ứng dụng lên AKS, đầu tiên, chúng ta cần build image, sau đó push image lên Azure Container Registry, từ đó AKS có thể tạo các ứng dụng từ image này.

Để AKS có thể kết nối đến Azure Container Registry, chúng ta phải gán quyền vào bằng lệnh:
```
$ CLIENT_ID=$(az aks show --name kcluster --resource-group gab2018 --query servicePrincipalProfile.clientId --output tsv)
$ ACR_ID=$(az acr show --name kregistry --resource-group gab2018 --query id --output tsv)
$ az role assignment create --assignee $CLIENT_ID --role Reader --scope $ACR_ID
```

### Tạo ứng dụng trên AKS bằng cách mô tả trong file .yaml
Đến đây, chúng ta đã có image sẵn sàng để dùng. Để tạo một service / deployment trên AKS, chúng ta cần một file **gab2018.yaml** để mô tả các service / deployment. Trong phạm vi của lab này sẽ không giải thích chi tiết làm thế nào để viết các mô tả này, bạn tham khảo thêm ở [đây](https://kubernetes.io/docs/tutorials/)

Bạn tạo file tên **webapi.yaml** ở thư mục gốc của ứng dụng với nội dung như sau:
```
$ kubectl create -f gab2018.yaml
```
Về cơ bản, AKS sẽ pull image từ image từ ACR để tạo container (chứa trong các pod) và sau đó sẽ expose ra các public ip / public port 8080 (do chúng ta đã mô tả trong file .yaml). Để liệt kê các services đã được tạo, dùng lệnh sau:

```
$ kubectl get services
```

Với địa chỉ public IP và public port, chúng ta có thể browse đến API này qua browsers với url http://[publicip]:[publicport]/api/values

## Bước 4 - Scale
Trong bài này sẽ không nói chi tiết về cơ chế hoạt động của **ReplicaSets**, **Services**, **Deployments**, ... mà giả sử rằng chúng ta đã biết cơ bản về nó.

Để scale một deployment, dùng lệnh sau:

```
$ kubectl scale --replicas=5 deployments gab2018
```

Sau đó, chúng ta dùng Curl để check xem load balancer có hoạt động tốt không:

```
$ curl http://[ip]:[port]/api/servers
```

## Bước 5 - Monitor
Dùng lệnh sau để enable Dashboard của Kubernetes, từ đó, chúng ta có thể dễ dàng theo dõi các ứng dụng đang chạy trên Azure AKS.

```
$ az aks browse --resource-group gab2018 --name kcluster
```

Chúc các bạn thành công!!!
