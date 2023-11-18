# kubernetes
kubernetes install with docker desktop &amp; wsl2

##### 啟動 WSL
1. 控制台>程式集>開啟或關閉Windows功能
2. 勾選"Windows 子系統Linux版"
3. 勾選"虛擬機器平台"

##### 確認/安裝 Ubuntu-20.04 in Windows
```js
wsl.exe -l -v
wsl.exe -install -d Ubuntu-20.04
wsl.exe --set-default-version 2
wsl.exe --set-version Ubuntu-20.04 2
wsl.exe --set-default Ubuntu-20.04
wsl -d Ubuntu-20.04 #開啟 Ubuntu-20.04
```

##### 更新 in Ubuntu
```js
sudo apt update
sudo apt upgrade -y
```

##### 命令提示完整性
```js
export PS1="\[\033[33m\]\D{%Y-%m-%d %I:%M%p}\[\033[00m\] \[\033[32m\]\u@$WSL_DISTRO_NAME\[\033[00m\]:\[\033[36m\]\w\[\033[00m\]$ "
```

##### 調整 bash 環境參數
```js
shopt -u progcomp
shopt -s no_empty_cmd_completion
```

##### 使用 vim 當成預設編輯器
```js
sudo update-alternatives --set editor /usr/bin/vim.basic
```

##### 安裝 NERDTree 檔案總管套件 (Vim)
```js
git clone https://github.com/preservim/nerdtree.git ~/.vim/pack/vendor/start/nerdtree
vim -u NONE -c "helptags ~/.vim/pack/vendor/start/nerdtree/doc" -c q
```

##### 新增 ~/.vimrc 檔案
```js
touch ~/.vimrc
```

##### 輸入以下內容至 ~/.vimrc 檔案
```js
set background=dark

syntax enable
"set number
set noruler
set ignorecase
set smartcase

"set cursorline
"set cursorcolumn

let &t_ti.="\e[1 q"
let &t_SI.="\e[5 q"
let &t_EI.="\e[1 q"
let &t_te.="\e[0 q"

map <A-Left> <Esc>:b#<CR>
map <A-Right> <Esc>:bn<CR>
map <C-W> <Esc>:q<CR>

"------------Plugin: nerdtree------------
map <C-p> :NERDTreeToggle<CR>
map <C-l> :tabn<CR>
map <C-h> :tabp<CR>

" Map Ctrl-Left & Ctrl-Right to switching tabs Ctrl-n to open new tab
map <C-Left> <Esc>:tabprev<CR>
map <C-Right> <Esc>:tabnext<CR>
map <C-n> <Esc>:tabnew<CR>
```
`使用 i 進入編輯，esc 離開編輯模式，:q 不儲存離開，:wq 儲存離開，:q! 強制離開`

##### 安裝 kubectx 與 kubens 工具
```js
sudo apt install pkg-config -y

wget https://github.com/ahmetb/kubectx/releases/download/v0.9.1/kubectx_v0.9.1_linux_x86_64.tar.gz
wget https://github.com/ahmetb/kubectx/releases/download/v0.9.1/kubens_v0.9.1_linux_x86_64.tar.gz

sudo tar zxvf kubectx_v0.9.1_linux_x86_64.tar.gz -C /usr/local/bin kubectx
sudo tar zxvf kubens_v0.9.1_linux_x86_64.tar.gz -C /usr/local/bin kubens

sudo git clone https://github.com/ahmetb/kubectx.git /etc/kubectx
COMPDIR=$(pkg-config --variable=completionsdir bash-completion)
sudo ln -sf /etc/kubectx/completion/kubens.bash $COMPDIR/kubens
sudo ln -sf /etc/kubectx/completion/kubectx.bash $COMPDIR/kubectx

rm kubectx_v0.9.1_linux_x86_64.tar.gz kubens_v0.9.1_linux_x86_64.tar.gz
```

##### 安裝 k9s 工具
```js
sudo wget https://github.com/derailed/k9s/releases/download/v0.21.7/k9s_Linux_x86_64.tar.gz
sudo tar -zxvf k9s_Linux_x86_64.tar.gz -C /usr/local/bin k9s

k9s info
```

##### Docker Desktop 配置
1. 務必切換到 Linux container 模式
2. General > 勾選 Use the WSL 2 based engine
3. Resources > WSL INTEGRATION > 啟用 Ubuntu-20.04

##### 檢查 docker 與 kubectl 版本
```js
docker version
```
`如果你無法成功執行 docker version 的話，請嘗試在 Windows PowerShell 中執行 Restart-Service LxssManager 命令重啟 LxssManager 服務，並 Restart Docker 應用程式。`

##### 檢查 kubectl 版本資訊
```js
kubectl version
kubectl version --client
```
`此時連不上 Kubernetes 是正常的！`

##### 安裝 KinD 工具
```js
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/
```

##### 建立單一節點的 K8S 叢集
```js
kind create cluster --name myk8s
```

##### 建立完成後，可以透過以下命令查詢目前已經透過 kind 建立幾套叢集
```js
kind get clusters
```

##### 驗證安裝結果
```js
kubectl cluster-info
```

##### 如果你有超過一套叢集被建立，則必須指定 context name 才能正確取得叢集資訊
```js
kubectl cluster-info --context kind-kind
kubectl cluster-info --context kind-kind-2
```

##### 取得節點清單
```js
kubectl get nodes
```

##### 取得所有命名空間下的所有物件清單
```js
kubectl get all --all-namespaces
```

##### 可以透過以下命令查詢 Client 與 Server 版本
```js
kubectl version
```

##### 當預設 Client 與 Server 版本不同時，建議你安裝跟 Server 相同版本的 Client 工具
```js
curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.27.3/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
```

##### 再檢查一次 kubectl 版本
```js
kubectl version --short
```

##### 建議設定 kubectl 的 bash 自動完成設定
```js
kubectl completion bash | sudo tee /etc/bash_completion.d/kubectl > /dev/null

echo 'alias k=kubectl' >>~/.profile
echo 'complete -F __start_kubectl k' >>~/.profile
```

##### 刪除 K8S 叢集
```js
kind delete cluster --name myk8s
```

##### 建立一個有 1 個 control-plane nodes 與 2 個 worker nodes 的叢集
```js
# Create a config file for a 3 nodes(1 master, 2 worker) cluster
cat << EOF > kind-3nodes.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
- role: control-plane
- role: worker
- role: worker
  extraPortMappings:
  - containerPort: 30080
    hostPort: 80
  - containerPort: 30443
    hostPort: 443
EOF
```

##### 命令載入設定檔並建立叢集
```js
kind create cluster --name myk8s --config ./kind-3nodes.yaml
```

##### 查詢每個 Node 的狀態 
```js
kubectl get nodes -o wide
```

##### 建立 Pod 與 Service 物件所需的 YAML 檔
```js
cat <<EOF > mynginx.yaml
apiVersion: v1
kind: Service
metadata:
  name: web-nginx
  labels:
    app.kubernetes.io/name: nginx
spec:
  type: NodePort
  ports:
    - name: http
      port: 80
      targetPort: http
      nodePort: 30080
  selector:
    app.kubernetes.io/name: nginx
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-nginx
  labels:
    app.kubernetes.io/name: nginx
spec:
  selector:
    matchLabels:
      app.kubernetes.io/name: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app.kubernetes.io/name: nginx
    spec:
      containers:
        - name: nginx
          image: docker.io/bitnami/nginx:1.19.1-debian-10-r23
          ports:
            - name: http
              containerPort: 8080
EOF
```

##### 套用 YAML 設定建立 Deployment 與 Service 物件
```js
kubectl apply -f mynginx.yaml
```

##### 查詢所有物件
```js
kubectl get all -o wide
```

`開啟瀏覽器並打開 http://localhost 網頁
如果這時你看見 Nginx 預設首頁畫面，就代表網路設定一切正常！`


##### 刪除 YAML 中定義的所有物件
```js
kubectl delete -f mynginx.yaml
```

##### 安裝 Kubernetes Dashboard
```js
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
kubectl get all -n kubernetes-dashboard
```

##### 設定 Dashboard 權限
1. 建立 k8s 服務帳戶 (ServiceAccount)
```js
kubectl apply -f - <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
EOF
```

2. 替上述 ServiceAccount 建立 ClusterRoleBinding 權限
```js
kubectl apply -f - <<EOF
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF
```

3. 取得Token
```js
kubectl -n kubernetes-dashboard create token admin-user
```

4. 啟動 Proxy 連接介面
```js
kubectl proxy
```

5. 連接到 [Kubernetes Dashboard](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/) 登入頁面並輸入 Token

##### 背景啟動 Proxy
```js
kubectl proxy &
```

##### 背景啟動 Proxy, 其的執行緒 pid
```js
ps -ef | head -1 && ps -ef | grep kubectl
```

##### kill 執行緒 pid
```js
kill -9 <pid>
```

##### 參考資源:
[The Will Will Web](https://blog.miniasp.com/post/2020/08/21/Install-Kubernetes-cluster-in-WSL-2-Docker-on-Windows-using-kind?full=1)
[Docs.Docker](https://docs.docker.com/desktop/wsl/)
[KinD](https://kind.sigs.k8s.io/docs/user/quick-start/#installation)
[kubernetes.io](
https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)
[Kubernetes Dashboard](https://github.com/kubernetes/dashboard/blob/master/docs/user/access-control/creating-sample-user.md)
