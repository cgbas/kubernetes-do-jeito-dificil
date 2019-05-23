# Pré-requisitos

## Google Cloud Platform

Esse tutorial explora o [Google Cloud Platform](https://cloud.google.com/) para simplificar o provisionameto da arquitetura computacional necessária para subir um cluster de Kubernetes do zero. [Inscreva-se em] (https://cloud.google.com/free/) para $300 (dólares) em créditos.

[Custo estimado](https://cloud.google.com/products/calculator/#id=78df6ced-9c50-48f8-a670-bc5003f2ddaa) para executar esse tutorial: $0.22 (dólares) por hora ($5.39 (dólares) por dia).

> Os recursos computacionais para esse tutorial excedem o nível gratuito do Google Cloud Platform.

## SDK do Google Cloud Platform

### Instale a SDK do Google Cloud 

Siga a [documentação](https://cloud.google.com/sdk/)(em Inglês) do SDK do Google Cloud para instalar e configurar o utilitário de linha de comando `gcloud`.

Verifique que a versão do SDK do Google Cloud é 218.0.0 ou superior:

```
gcloud version
```

### Defina uma Zona Computacional e Região Padrões

Esse tutorial assume que uma zona e uma região computacional tenham sido configuradas.

Se você está utilizando a ferramenta de linha de comando `gcloud` pela primeira vez, `init` é a maneira mais fácil de fazer isso:

```
gcloud init
```

Ou então, defina uma Região Computacional padrão:

```
gcloud config set compute/region us-west1
```

Defina uma Zona Computacional padrão:

```
gcloud config set compute/zone us-west1-c
```

> Utilize o comando `gcloud compute zones list` para visualizar regiões e zonas adicionais.

## Executando comandos em paralelo com o tmux

[tmux](https://github.com/tmux/tmux/wiki) pode ser utilizado para executar comandos em várias instâncias computacionais ao mesmo tempo. Os laboratórios nesse tutorial podem requerir que os mesmos comandos sejam executados em várias instâncias, nesses casos considere utilizar o tmux e dividir uma janela em vários painéis via `sinchronize-panes` , para acelerar o processo de provisionamento.

> A utlização do tmux é opcional e não é necessária para concluir esse tutorial.

![screenshot do tmux ](imagens/tmux-screenshot.png)

> Habilite o `synchronize-panes`: `ctrl+b` e `shift :`. Então digite `set synchronize-panes on` no prompt. Para desabilitar a sincronização: `set synchronize-panes off`.

Próximo: [Instalando as Ferramentas Cliente](02-ferramentas-cliente.md)
