**ATENÇÃO**: Essa é uma tradução livre, sem fins lucrativos, buscando apenas facilitar o acesso a material em Português sobre o Kubernetes diretamente de quem realmente entende do assunto, nada menos que **Kelsey Hightower**.

** Status em: 21/05/2019 **
** Previsão de conclusão: 30/05/2019 **

__Sessões revisadas__: 0
__Sessões pendentes__: README, 01, 02, 03, 06, 12, 14, 04, 05, 07, 08, 09, 10, 11, 13

# Kubernetes do Jeito Difícil

Esse tutorial irá acompanhá-lo na configuração do Kubernetes do Jeito Difícil. Esse guia não é para pessoas buscando um comando totalmente automatizado para colocar no ar um cluster de Kubernetes. Se você é um desses então confira [Google Container Engine](https://cloud.google.com/container-engine), ou então [Getting Started Guides](http://kubernetes.io/docs/getting-started-guides/) (em Inglês).

Kubernetes do Jeito Difícil está otimizado para o aprendizado, significando tomar o caminho mais longo para garantir que você entenda cada tarefa necessária para colocar no ar um cluster de Kubernetes.

> Os resultados desse tutorial não devem ser tidos como aptos à produção, e podem ter suporte limitado da comunidade, mas não deixe que isso o impeça de aprender!

## Público Alvo

O público alvo para esse tutorial é alguém planejando suportar um cluster de Kubernetes em produção e querendo entender como tudo se encaixa.

## Detalhes do Cluster

O Kubernetes do Jeito Difícil irá guiá-lo para colocar no ar um cluster Kubernetes de alta disponibilidade com encriptação fim-a-fim entre componentes e autenticação RBAC.

* [Kubernetes](https://github.com/kubernetes/kubernetes) 1.10.2
* [containerd Container Runtime](https://github.com/kubernetes-incubator/cri-containerd) 1.1.0
* [gVisor](https://github.com/google/gvisor) 08879266fef3a67fac1a77f1ea133c3ac75759dd
* [CNI Container Networking](https://github.com/containernetworking/cni) 0.6.0
* [etcd](https://github.com/coreos/etcd) 3.3.5

## Laboratórios

Esse tutorial parte do princípio que você tenha acesso ao [Google Cloud Platform (GCP)](https://cloud.google.com). Apesar do  GCP ser utilizado para requisitos básicos de infraestrutura, as lições aprendidas nesse tutorial podem ser aplicadas em outras plataformas.

* [Pré-requisitos](docs/01-pre-requisitos.md)
* [Instalando as Ferramentas Cliente](docs/02-ferramentas-cliente.md)
* [Provisionando Recursos Computacionais](docs/03-recursos-computacionais.md)
* [Provisionando a CA e Gerando Certificados TLS](docs/04-autoridade-certificadora.md)
* [Gerando Arquivos de Configuração do Kubernetes para Autenticação](docs/05-arquivos-configuracao-kubernetes.md)
* [Gerando a Configuração e a Chave de Encriptação de Dados](docs/06-chaves-encriptacao-dados.md)
* [Subindo o Cluster etcd](docs/07-subindo-etcd.md)
* [Subindo o Plano de Controle](docs/08-subindo-controladoras-kubernetes.md)
* [Subindo os Nós _Worker_ do Kubernetes](docs/09-subindo-workers-kubernetes.md)
* [Configurando o kubectl para Acesso Remoto](docs/10-configurando-kubectl.md)
* [Provisionando Rotas de Rede dos Pods](docs/11-rotas-de-rede-de-pod.md)
* [Implantando o Add-on de DNS do Cluster](docs/12-dns-addon.md)
* [Smoke Test (Teste de Fumaça)](docs/13-smoke-test.md)
* [Limpeza](docs/14-limpeza.md)
