# Azure Container Service demo (AKS)
---
## Đăng nhập Azure dùng Azure Pass
Bạn vui lòng đăng ký với GAB2018 organizer để có Azure Pass, sau đó đăng nhập vào trang Azure Portal.

## Cài đặt trước các phần mềm
- Azure CLI
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
```
$ az aks get-credentials --name kcluster --resource-group gab2018
```

## Bước 2 - Tạo ứng dụng
## Bước 3 - Deploy lên AKS
## Bước 4 - Sale
## Bước 5 - Monitor