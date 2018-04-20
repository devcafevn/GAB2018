# Azure Container Service demo (AKS)
---
## Đăng nhập Azure dùng Azure Pass
Bạn vui lòng đăng ký với GAB2018 organizer để có Azure Pass, sau đó đăng nhập vào trang Azure Portal.

## Cài đặt trước các phần mềm
- Azure CLI
- Docker
- dotnet SDK 2.0 (hoặc 2.1 preview)
- kubectl

## Bước 1 - Tạo AKS cluster
### Đăng nhập vào Azure
```
$ az login
```
Copy URL được sinh ra và mở URL lên browser, sau đó nhập vào code (cũng được sinh ra từ console), sau khi đăng nhập xong thì đóng của sổ trình duyệt lại

### Tạo resource group
Hầu hết tất cả service trên Azure sẽ cần một resource group, do đó, cần phải tạo nó trước.
```bash
$ az create --group gab2018 --location eastus
```

### Tạo Azure Kubernetes Service (AKS Cluster)
```
$ az aks create --name kcluster --resource-group gab2018 --node-count 1 --generate-ssk-keys
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

Để kiểm tra chắc chắn là **kubectl** đang kết nối đến cluster của mình, chạy lệnh sau để kiểm tra, bạn sẽ thấy kubectl đang kết nối đến master **kcluster-azurebootcam**:
```bash
$ kubectl cluster-info
Kubernetes master is running at https://kcluster-azbootcamp-d44916-ce790490.hcp.eastus.azmk8s.io:443
```

## Bước 2 - Tạo ứng dụng
Trong ví dụ này, chúng ta sẽ tạo ra một ứng dụng hello-world bằng dotnet core, sau đó sẽ deploy lên AKS cluster. Chú ý là bạn cần phải cài dotnet sdk trước để có thể chạy dotnet core application.
 
Tạo ứng dụng dụng aspnet core (webapi), sau đó chạy ứng dụng lên.
```
$ mkdir gab2018 && cd gab2018
$ dotnet new webapi
$ dotnet run
```
## Bước 3 - Deploy lên AKS
Để deploy ứng dụng lên AKS, đầu tiên, chúng ta cần build image, sau đó push image lên Azure Container Registry, từ đó AKS có thể tạo các ứng dụng từ image này.

**Build image**
Chúng ta cần cài đặt Docker để có thể sử dụng docker cli. Lệnh bên dưới sẽ build image có với tên là **webapi**, và sau đó sẽ tạo một container trên image này bằng lệnh **docker run**.

```
$ docker build -t webapi .
$ docker run --name webapi webapi
```

## Bước 4 - Scale
## Bước 5 - Monitor
