# kind-create

## [kindクラスタ構築](https://kind.sigs.k8s.io/docs/user/quick-start/#:~:text=%E9%85%8D%E7%BD%AE%E3%81%97%E3%81%BE%E3%81%99%E3%80%82-,Linux%E3%81%AE%E5%A0%B4%E5%90%88,-%3A)

* kindインストール
```
$ export https_proxy=http://172.19.0.3:8080
$ [ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.25.0/kind-linux-amd64
$ [ $(uname -m) = aarch64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.25.0/kind-linux-arm64
$ chmod +x ./kind
$ sudo mv ./kind /usr/local/bin/kind
$ unset https_proxy
```

* yamlファイルの作成(各バージョンファイルの作成)

* cluster作成
```
# kind create cluster --config kind.yaml
```

### Dockerインストール(RHEL環境)

* 必要なパッケージインストールとリポジトリの登録
```
# yum install -y yum-utils
# yum-config-manager --add-repo https://download.docker.com/linux/rhel/docker-ce.repo
# ls /etc/yum.repos.d/docker-ce.repo
```

* インストールできるバージョンを確認して、Dockerをインストール
```
# yum list docker-ce --showduplicates | sort -r
# yum list docker-ce-cli --showduplicates | sort -r
# yum install -y docker-ce-3:26.1.0-1.el9 docker-ce-cli-1:26.1.0-1.el9 containerd.io docker-buildx-plugin docker-compose-plugin
# docker --version
```

* Dockerデーモンの起動
```
# systemctl enable --now docker
# systemctl status docker
```

* systemdに環境変数を設定する
```
# systemctl edit docker
---
[Service]
Environment = 'http_proxy=http://172.19.0.3:8080' 'https_proxy=http://172.19.0.3:8080' 'DOCKER_ENABLE_DEPRECATED_PULL_SCHEMA_1_IMAGE=1'
---
```

* Dockerクライアントを設定する
```
# mkdir ~/.docker
# vi ~/.docker/config.json
---
{
  "proxies": {
    "default": {
      "httpProxy": "http://172.19.0.3:8080",
      "httpsProxy": "http://172.19.0.3:8080"
    }
  }
}
```

* kubesprayユーザでも実行できるように変更
```
# cat /etc/group | grep docker
# sudo gpasswd -a kubespray docker
# cat /etc/group | grep docker
```

* Docker再起動
```
# export https_proxy=http://172.19.0.3:8080
# systemctl daemon-reload
# systemctl restart docker
# systemctl status docker
```

* 確認
```
# docker pull hello-world
# docker run hello-world
```
