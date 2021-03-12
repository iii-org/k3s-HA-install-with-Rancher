# k3s-install
⚠️ ⚠️ 此教學若要對外有有效憑證的話，僅對Cloudflare或是其他可以免本機端憑證儲存的雲端方式有效  
⚠️ ⚠️ 若要使用DNS-update或是http01 letsEncrypt等本機端有效憑證自動更新儲存請查詢`Cert-manager`或是`Rancher官網`  
⚠️ ⚠️ 此教學限制Rancher至舊版2.4.X，因此同時對K3s支援的版本有限制，因此建議僅用於開發，不可用於Production環境。  
⚠️ ⚠️ 最新版本的k3s不支援本教學，因此若已有最新版本的k3s請勿使用教學安裝(會無法安裝Rancher)  
⚠️ ⚠️ 不採用與Demo一樣舊版的1.18.5，主要是v1.19.1的k3s安裝可以寫config檔案來部屬，增加部屬上管理方便性(下方註解掉的方案)
⚠️ ⚠️ 目前卡在環境所需CPU不足供應叢集所需，因此死掉。

* High Availability  
[V] K3s  
[V] Rancher  

## Node
172.16.0.171 / 172.16.0.172 / 172.16.0.173

## (k8s前置環境準備工作)
### SetHost
172.16.0.171  dev2-171  
172.16.0.172  dev2-172  
172.16.0.173  dev2-173  
### disable swap
sudo swapoff -a
sudo rm /swap.img

## VM Manage
URL: https://172.16.0.251:8006/  
user: test-uesr

## DNS For HA
dev2.iiidevops.org  
```cmd
C:\Users\m0724>nslookup dev2.iiidevops.org
伺服器:  dnas1.iii.org.tw
Address:  140.92.66.74

未經授權的回答:
名稱:    dev2.iiidevops.org
Addresses:  172.16.0.172
          172.16.0.173
          172.16.0.171
```

⚠️ $K3S_CONFIG_FILE=/etc/rancher/k3s/config.yaml

## 每個節點建立config檔案
```sh
mkdir -p /etc/rancher/k3s && \
nano /etc/rancher/k3s/config.yaml
```

## 建立第一個Server節點(初始節點)
```sh
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="v1.19.1+k3s1" sh -s - server --cluster-init
curl -sfL https://get.k3s.io | sh -s - server --cluster-init
```
### 部屬文件
* [172.16.0.171的部屬文件](rke2-171-config.yaml)

## 其他的Server節點(湊成三個來建立HA)
```sh
curl -sfL https://get.k3s.io | INSTALL_K3S_VERSION="v1.19.1+k3s1" sh -s - server
curl -sfL https://get.k3s.io | sh -s - server
```
### 部屬文件
* [172.16.0.172的部屬文件](rke2-172-config.yaml)
* [172.16.0.173的部屬文件](rke2-173-config.yaml)

## Get Config For Helm(任意Server節點)
```
cp /etc/rancher/k3s/k3s.yaml ~/config && \
sudo snap install helm3 --classic && \
helm3 repo add rancher-stable https://releases.rancher.com/server-charts/stable && \
kubectl create namespace cattle-system && \
kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.0.4/cert-manager.crds.yaml && \
kubectl create namespace cert-manager && \
helm3 repo add jetstack https://charts.jetstack.io && \
helm3 install cert-manager jetstack/cert-manager --namespace cert-manager --version v1.0.4 --kubeconfig ~/config && \
helm3 install rancher rancher-stable/rancher   --namespace cattle-system --set hostname=rancher.iii.k8s.csie.nuu.edu.tw --kubeconfig ~/config
--kubeconfig ~/config && \
```

## Check Rancher 可用 Version
helm3 search repo rancher-stable/rancher --versions
```
rancher-stable/rancher  2.5.6           v2.5.6          Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.5.5           v2.5.5          Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.5.4           v2.5.4          Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.5.3           v2.5.3          Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.5.2           v2.5.2          Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.4.14          v2.4.14         Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.4.13          v2.4.13         Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.4.12          v2.4.12         Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.4.11          v2.4.11         Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.4.10          v2.4.10         Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.4.8           v2.4.8          Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.4.7           v2.4.7          Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.4.6           v2.4.6          Install Rancher Server to manage Kubernetes clu...
rancher-stable/rancher  2.4.5           v2.4.5          Install Rancher Server to manage Kubernetes clu...
.......
```

## Agent節點(目前iiiorg系統不考慮Agent角色)

## reference
* https://rancher.com/docs/k3s/latest/en/installation/ha-embedded/
* https://rancher.com/docs/rancher/v2.x/en/installation/resources/k8s-tutorials/ha-rke/
* https://rancher.com/docs/rke/latest/en/installation/
* https://rancher.com/docs/rancher/v2.x/en/installation/resources/k8s-tutorials/ha-rke/
