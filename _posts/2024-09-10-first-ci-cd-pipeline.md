---
title: "CI/CD com GitHub Actions no Azure Web App (Dev, QA and Prod)"
date: 2024-09-10 00:00:00:0000
categories: [iac]
tags: [iac]
---

# CI/CD com GitHub Actions no Azure Web App (Dev, QA and Prod)
O objetivo deste artigo é demonstrar como criar um pipeline de CI/CD completo usando GitHub Actions para implantar um site em aplicativos Web estáticos do Azure.

Configuraremos três ambientes distintos: **Desenvolvimento, Garantia de Qualidade (QA) e Produção.**

#### Desenvolvimento
O pipeline de desenvolvimento é acionado por qualquer push para o branch dev. Ele cria e implanta automaticamente o aplicativo no ambiente de desenvolvimento.

#### Garantia de qualidade (QA)
O pipeline de controle de qualidade é ativado quando uma solicitação pull para a ramificação de controle de qualidade é mesclada. Esse pipeline cria e implanta o aplicativo no ambiente de Garantia de Qualidade, garantindo que o aplicativo esteja pronto para teste.

#### Produção
O pipeline de produção é acionado quando uma solicitação pull para a ramificação principal é mesclada. Ele constrói e implanta o aplicativo no ambiente de produção, ao mesmo tempo que fecha o PR para manter a proteção e a estabilidade no branch principal.





---

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)
