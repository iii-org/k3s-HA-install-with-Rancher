# k8s-install
* High Availability  
[V] K3s  
[V] Rancher  

## Node
172.16.0.171 / 172.16.0.172 / 172.16.0.173

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
mkdir -p /etc/rancher/k3s
nano /etc/rancher/k3s/config.yaml
```

## 建立第一個Server節點(初始節點)
```sh
curl -sfL https://get.k3s.io | sh -s - server --cluster-init
```
### 部屬文件
* [10.20.0.68的部屬文件](rke2-68-config.yaml)

## 其他的Server節點(湊成三個來建立HA)
```sh
curl -sfL https://get.k3s.io | sh -s - server
```
### 部屬文件
* [10.20.0.73的部屬文件](rke2-73-config.yam)
* [10.20.0.74的部屬文件](rke2-74-config.yam)

## Get Config For Helm
```
cp /etc/rancher/k3s/k3s.yaml ~/config
sudo snap install helm3 --classic
helm3 repo add rancher-stable https://releases.rancher.com/server-charts/stable
kubectl create namespace cattle-system
kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.0.4/cert-manager.crds.yaml
kubectl create namespace cert-manager
helm3 repo add jetstack https://charts.jetstack.io
helm3 install cert-manager jetstack/cert-manager   --namespace cert-manager   --version v1.0.4 --kubeconfig ~/config

--kubeconfig ~/config
```

## 安裝憑證管理系統(支援cloudflare、http01、自簽等)
`helm3 install cert-manager jetstack/cert-manager   --namespace cert-manager   --version v1.0.4 --kubeconfig /home/localadmin/config`
`helm3 install rancher rancher-stable/rancher   --namespace cattle-system --set hostname=dev2.iiidevops.org --kubeconfig /home/localadmin/config`

## Agent節點(目前不屬不考慮Agent角色)

## reference
* https://rancher.com/docs/k3s/latest/en/installation/ha-embedded/
* https://rancher.com/docs/rancher/v2.x/en/installation/resources/k8s-tutorials/ha-rke/
* https://rancher.com/docs/rke/latest/en/installation/
* https://rancher.com/docs/rancher/v2.x/en/installation/resources/k8s-tutorials/ha-rke/
