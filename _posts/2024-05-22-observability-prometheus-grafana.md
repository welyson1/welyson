---
title: "Monitoria e Observabilidade com Prometheus e Grafana"
date: 2024-05-22 00:00:00:0000
categories: [Observability]
tags: [observability] [IaC]
---

# Prometheus
É uma ferramenta de monitoria, muito utilizado em ambientes dinâmicos, como containers (k8s, swarms, e etc...)

Pode ser utilizado em outros ambientes **bare metal**, mas o forte dele é em infraestrutura de microserviços e containers.

Você utilizar ele de diversas maneiras:
1. Como um http server que salva e expõe as métricas, assim você pode consultar usando outro sistema de observabilidade.
2. Como um sistema de observabilidade completo, criando dashboards e gerando alertas em cima dessas métricas

## Arquitetura Prometheus
Um outro ponto importante é que o Prometheus faz o pull das métricas, diferente de outras aplicações de monitoria, realizando esse pull, deixa o processo mais leve e menos danoso para o servidor/ambiente monitorado.
- ![architecture](/_images/observability-prometheus-grafana/architecture_prometheus.png)

**Alguns aspectos importantes são:**
- **Targets:** Qualquer ambiente que podemos recolher as métricas, como: linux server, http server, databases, containers, microserviços e qualquer ambiente.
- **Units:** Qualquer medida que podemos analisar do ambiente: CPU, memória, latência, espaço em disco, etc...
- **Metrics:** Todas as units que foram recolhidas e armazenadas no Prometheus.

O **Retrieval** é o agente que envia essas métricas, ele recolhe essas informações atráves de uma URL de conexão com seu ambiente. Para isso vamos precisar de um library que expõe a sua aplicação com o recurso `/metrics`
- **Alguns serviços já possuem esse `/metrics` expostos.**

### Arquitetura Prometheus Simplificada
- ![architecture_prometheus](/_images/observability-prometheus-grafana/architecture_prometheus_grafana.png)
- **Fonte:** https://michelleamesquita.medium.com/monitoramento-com-grafana-prometheus-fc2227e67433

O nosso Prometheus utiliza o retrieval para realizar o pull das métricas de nossos ambientes e adiciona essas métricas em um Storage (Time Series Database). Com o Grafana nós conseguimos consultar essas métricas e criar dashboards personalizados de monitoria e observabilidade.

Podemos também usar a própria UI do Prometheus ou consultar via API.

# Grafana
É uma ferramenta Open Source para visualização de dados, ela é muito popular para visualização de monitoria e observabilidade.

Você pode criar alertas, queries e diversas visualizações consultando as `/metrics`.

Existem vários dashboards já pré-configurados que foram disponibilizados pela comunidade, vamos utilizar um deles:
- Nós vamos utilizar os dashboard do repositório Grafana Dashboards, segue link do GitHub: [acesse aqui](https://github.com/rfrail3/grafana-dashboards/tree/master/prometheus)

- Podemos importar um dashboard para o grafana usando usando o tema.json do repositório acima: [acesse aqui](https://raw.githubusercontent.com/rfrail3/grafana-dashboards/master/prometheus/node-exporter-full.json)


# Subindo um Serviço de Observabilidade e Monitoria com Prometheus e Grafana

Eu adicionei todos os arquivos que utilizei em um repositório no meu GitHub. Você pode clonar e iniciar com os comandos abaixo.

## Resumo dos Arquivos:
- **Vagrantfile**: Configuração para criar e provisionar a VM com Vagrant.
- **provision.sh**: Script de provisionamento para instalar e configurar Docker na VM.
- **prometheus.yml**: Arquivo de configuração do Prometheus para definir as fontes de scrape.
- **node_exporter-1.3.0.linux-amd64.tar.gz**: Binário do Node Exporter para monitorar a máquina.


## Subindo a VM
Para iniciar a VM, use o comando:
```sh
vagrant up
```

## Acessando a VM
Sincronize e adicione os arquivos na pasta do servidor:
```sh
vagrant rsync
```

## Configuração do Node Exporter
- **Node Exporter:** 
1. Extraia os arquivos do tar:
```sh
tar -xvf node_exporter-1.3.0.linux-amd64.tar.gz
```

2. Mova os arquivos para o diretório `/opt/`:
```sh
tar -xvf node_exporter-1.3.0.linux-amd64.tar.gz
```

3. Navegue para a pasta do node_exporter:
```sh
cd /opt/node_exporter-1.3.0.linux-amd64
```

4. Execute o node exporter em background (para não travar o cmd):
```sh
nohup ./node_exporter &
```

5. Verifique os logs do nohup:
```sh
tail -f nohup.out`
```

### Testando a Conexão do Node Exporter (Opcional)
Para instalar telnet, se necessário, e testar a conexão do node exporter:
```sh
yum install telnet -y
telnet 192.168.1.6 9100
```

##  Configuração do Prometheus
Suba o Prometheus via Docker:
```sh
docker run -d -p 9090:9090 -v /vagrant/prometheus.yml:/etc/prometheus/prometheus.yml prom/prometheus
```

### Acessando as Interfaces
- Acesse a UI do Prometheus em: `http://192.168.1.6:9090`
- Acesse o node exporter em: `http://192.168.1.6:9100/metrics`

## Configuração do Grafana
1. Suba a imagem do Grafana:
```sh
docker run -d -p 3000:3000 --name grafana grafana/grafana:latest
```

2. Verifique se o Docker e o Prometheus estão no ar:
```sh
docker ps
```

3. Acesse a interface do Grafana:
    - URL: `http://192.168.1.3:3000`
    - Login padrão: `admin`
    - Senha padrão: `admin`

4. Configure a fonte de dados no Grafana:
    - Vá em **Data Sources** -> **Add data source** -> **Prometheus**
    - Em **Connection** -> **URL**, adicione: `http://192.168.1.6:9090`
    - Clique em `Save & Test`

5. Importando um Dashboard pré-configurado para o Node Exporter:
    - Acesse os dashboard nesse [repositório] e o `.json` que utilizei [aqui].
    - Copie o conteúdo do arquivo `.json` do dashboard.
    - No Grafana, vá em **Dashboards** ->  **Create Dashboard** -> **Import**.
    - Cole o conteúdo do `.json` no campo **Import**.
    - Selecione a fonte de dados **Prometheus** configurada anteriormente.
    - Clique em **Import**.

6. Seu Dashboard estará disponível e configurado.
    - ![dashboard](/_images/observability-prometheus-grafana/dashboard-grafana.png)














