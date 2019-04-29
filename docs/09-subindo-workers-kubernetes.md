# Subindo os Nós Worker do Kubernetes

Nesse lab você irá subir três nós _worker_ do Kubernetes. Os seguintes componentes serão instalados em cada nó:[runc](https://github.com/opencontainers/runc), [plugins de rede para contêiner](https://github.com/containernetworking/cni), [cri-containerd](https://github.com/kubernetes-incubator/cri-containerd), [kubelet](https://kubernetes.io/docs/admin/kubelet) e [kube-proxy](https://kubernetes.io/docs/concepts/cluster-administration/proxies).

## Pré-requisitos

Os comandos nesse lab devem ser executados em cada instância de controladora:  `worker-0`, `worker-1`, e `worker-2`. Conecte em cada controladora utilizando o comando `gcloud`. Exemplo:

```
gcloud compute ssh worker-0
```

## Provisionando um Nó _Worker_ do Kubernetes

Instale as dependências de SO:

```
sudo apt-get -y install socat
```

> O binário socat habilita o suporte ao comando `kubectl port-forward`.

### Faça o Download e Instale os Binários do _Worker_

```
wget -q --show-progress --https-only --timestamping \
  https://github.com/containernetworking/plugins/releases/download/v0.6.0/cni-plugins-amd64-v0.6.0.tgz \
  https://github.com/kubernetes-incubator/cri-containerd/releases/download/v1.0.0-alpha.0/cri-containerd-1.0.0-alpha.0.tar.gz \
  https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubectl \
  https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kube-proxy \
  https://storage.googleapis.com/kubernetes-release/release/v1.8.0/bin/linux/amd64/kubelet
```

Crie os diretórios de instalação:

```
sudo mkdir -p \
  /etc/cni/net.d \
  /opt/cni/bin \
  /var/lib/kubelet \
  /var/lib/kube-proxy \
  /var/lib/kubernetes \
  /var/run/kubernetes
```

Instale os binários do _worker_:

```
sudo tar -xvf cni-plugins-amd64-v0.6.0.tgz -C /opt/cni/bin/
```

```
sudo tar -xvf cri-containerd-1.0.0-alpha.0.tar.gz -C /
```

```
chmod +x kubectl kube-proxy kubelet
```

```
sudo mv kubectl kube-proxy kubelet /usr/local/bin/
```

### Configure a rede CNI

Recupere a faixa CIDR do Pod para a instância computacional corrente:

```
POD_CIDR=$(curl -s -H "Metadata-Flavor: Google" \
  http://metadata.google.internal/computeMetadata/v1/instance/attributes/pod-cidr)
```

Crie o arquivo de configuração de rede `bridge`:

```
cat > 10-bridge.conf <<EOF
{
    "cniVersion": "0.3.1",
    "name": "bridge",
    "type": "bridge",
    "bridge": "cnio0",
    "isGateway": true,
    "ipMasq": true,
    "ipam": {
        "type": "host-local",
        "ranges": [
          [{"subnet": "${POD_CIDR}"}]
        ],
        "routes": [{"dst": "0.0.0.0/0"}]
    }
}
EOF
```

Crie o arquivo de configuração de rede `loopback`:

```
cat > 99-loopback.conf <<EOF
{
    "cniVersion": "0.3.1",
    "type": "loopback"
}
EOF
```

Mova os arquivos de configuração para o diretório de configuração do CNI:

```
sudo mv 10-bridge.conf 99-loopback.conf /etc/cni/net.d/
```

### Configure o Kubelet

```
sudo mv ${HOSTNAME}-key.pem ${HOSTNAME}.pem /var/lib/kubelet/
```

```
sudo mv ${HOSTNAME}.kubeconfig /var/lib/kubelet/kubeconfig
```

```
sudo mv ca.pem /var/lib/kubernetes/
```

Crie o arquivo _unit_ `kubelet.service` do systemd:

```
cat > kubelet.service <<EOF
[Unit]
Description=Kubernetes Kubelet
Documentation=https://github.com/GoogleCloudPlatform/kubernetes
After=cri-containerd.service
Requires=cri-containerd.service

[Service]
ExecStart=/usr/local/bin/kubelet \\
  --allow-privileged=true \\
  --anonymous-auth=false \\
  --authorization-mode=Webhook \\
  --client-ca-file=/var/lib/kubernetes/ca.pem \\
  --cluster-dns=10.32.0.10 \\
  --cluster-domain=cluster.local \\
  --container-runtime=remote \\
  --container-runtime-endpoint=unix:///var/run/cri-containerd.sock \\
  --image-pull-progress-deadline=2m \\
  --kubeconfig=/var/lib/kubelet/kubeconfig \\
  --network-plugin=cni \\
  --pod-cidr=${POD_CIDR} \\
  --register-node=true \\
  --require-kubeconfig \\
  --runtime-request-timeout=15m \\
  --tls-cert-file=/var/lib/kubelet/${HOSTNAME}.pem \\
  --tls-private-key-file=/var/lib/kubelet/${HOSTNAME}-key.pem \\
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

### Configure o Proxy do Kubernetes

```
sudo mv kube-proxy.kubeconfig /var/lib/kube-proxy/kubeconfig
```

Crie o arquivo _unit_ `kube-proxy.service` do systemd:

```
cat > kube-proxy.service <<EOF
[Unit]
Description=Kubernetes Kube Proxy
Documentation=https://github.com/GoogleCloudPlatform/kubernetes

[Service]
ExecStart=/usr/local/bin/kube-proxy \\
  --cluster-cidr=10.200.0.0/16 \\
  --kubeconfig=/var/lib/kube-proxy/kubeconfig \\
  --proxy-mode=iptables \\
  --v=2
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
```

### Inicie os Serviços do _Worker_

```
sudo mv kubelet.service kube-proxy.service /etc/systemd/system/
```

```
sudo systemctl daemon-reload
```

```
sudo systemctl enable containerd cri-containerd kubelet kube-proxy
```

```
sudo systemctl start containerd cri-containerd kubelet kube-proxy
```

> Lembre-se de executar os comandos acima em cada um dos nós de _worker_: `worker-0`, `worker-1` e `worker-2`.

## Verificação

Conecte em um dos nós de controladora:

```
gcloud compute ssh controller-0
```

Liste os nós de Kubernetes registrados:

```
kubectl get nodes
```

> saída

```
NAME       STATUS    ROLES     AGE       VERSION
worker-0   Ready     <none>    1m        v1.8.0
worker-1   Ready     <none>    1m        v1.8.0
worker-2   Ready     <none>    1m        v1.8.0
```

Próximo: [Configurando o kubectl para Acesso Remoto](10-configurando-kubectl.md)
