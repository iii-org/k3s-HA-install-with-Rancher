# k8s-install
* High Availability  
[V] K3s  
[V] Rancher  

## Node
* 10.20.0.73
* 10.20.0.68
* 10.20.0.74

## DNS For HA
inter-iii.k8s.csie.nuu.edu.tw
```
C:\Users\m0724>nslookup inter-iii.k8s.csie.nuu.edu.tw
伺服器:  dnas1.iii.org.tw
Address:  140.92.66.74

未經授權的回答:
名稱:    inter-iii.k8s.csie.nuu.edu.tw
Addresses:  10.20.0.73
          10.20.0.68
          10.20.0.74
```

⚠️ $K3S_CONFIG_FILE=/etc/rancher/k3s/config.yaml

## 每個節點建立config檔案
```sh
mkdir -p /etc/rancher/k3s
```

## 建立第一個Server節點
10.20.0.68 
```sh
nano /etc/rancher/k3s/config.yaml
K3S_TOKEN=SECRET k3s server --cluster-init
```
```
K3S_TOKEN: "interiiik8scsienuuedutwbypfsense"
write-kubeconfig-mode: "0644"
tls-san:
  - "inter-iii.k8s.csie.nuu.edu.tw"
node-label:
  - "nodeid=68"
  - "org=iii"
```


## Set First Master
curl -sfL https://get.k3s.io | sh -

## reference
https://rancher.com/docs/k3s/latest/en/installation/ha-embedded/
