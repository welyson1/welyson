---
title: "Configuração DNS & Conceitos"
date: 2024-07-23 00:00:00:0000
categories: [Redes]
tags: [redes]
---

# Configuração DNS & Conceitos
Você não precisa saber tudo sobre redes...

Porém, existem alguns conceitos que são obrigatórios na área de tech e que um dia vão puxar o seu pé a noite.

Configurar DNS é um deles! 

A ideia aqui não é apenas explicar o que é DNS, mas também os conceitos que existem em torno dele. Esses conceitos estão relacionados e são fundamentais para o funcionamento de diversas áreas da empresa.

Nesse artigo vamos falar sobre:
- O que é DNS?
- Para que Servem as Entradas DNS?
- O Que é DKIM e SPF?
- O que é DMARC?


## O que é DNS?
O DNS (Domain Name System) é um sistema que traduz nomes de domínio legíveis por humanos (como `www.exemplo.com`) em endereços IP legíveis por máquinas (como `192.0.2.1`). Isso é essencial para a navegação na internet, pois os computadores utilizam endereços IP para se comunicar entre si.


### Como funciona o DNS?
1. **Resolução de Nome:** Quando você digita um endereço web no navegador, a primeira etapa é resolver esse nome de domínio em um endereço IP.
2. **Consulta Recursiva:** O navegador consulta um resolver DNS, geralmente fornecido pelo seu provedor de internet. Se o resolver não souber a resposta, ele consulta os servidores DNS autoritativos em uma série de passos, começando pelos servidores raiz.
3. **Servidores Raiz:** Existem 13 conjuntos de servidores raiz ao redor do mundo, que conhecem os servidores autoritativos para os domínios de nível superior (TLDs), como .com, .org, .net, etc.
4. **Servidores TLD:** Os servidores TLD conhecem os servidores DNS autoritativos para domínios específicos, como exemplo.com.
5. **Servidores Autoritativos:** Finalmente, os servidores autoritativos para o domínio fornecem o endereço IP correspondente ao nome de domínio solicitado.

### O que são Zonas DNS?
Uma zona DNS é uma **porção do espaço de nomes de domínio que é gerenciada por uma entidade específica.** A zona contém todos os registros DNS para os domínios dentro dessa porção.

### Como as Zonas DNS são Organizadas?
1. **Zona Raiz:** O nível mais alto na hierarquia DNS. Todos os domínios de nível superior (TLDs) são subordinados à zona raiz.
2. **Domínios de Nível Superior (TLD):** Estes incluem .com, .org, .net, e domínios específicos de países como .br (Brasil), .uk (Reino Unido).
3. **Domínios de Segundo Nível:** Subordinados aos TLDs, como exemplo.com.
4. **Subdomínios:** Subordinados aos domínios de segundo nível, como blog.exemplo.com.

### Delegação de Zonas
A delegação ocorre **quando uma zona DNS é dividida em subzonas, e a autoridade sobre essas subzonas é delegada a outras entidades.** Por exemplo, a zona com é gerenciada pela Verisign, que delega a autoridade sobre `exemplo.com` ao proprietário desse domínio.

## Para que Servem as Entradas DNS?
As entradas DNS, ou registros DNS, **servem para fornecer informações específicas sobre domínios e suas configurações, permitindo que os dispositivos na internet encontrem e se comuniquem com outros dispositivos de forma eficiente.**

### Tipos de Registros DNS e Suas Funções

- **A (Address Record):**
	- Função: Mapeia um nome de domínio para um endereço IPv4.
	- Exemplo: `example.com -> 192.0.2.1`
	- Uso: Permite que os navegadores convertam um nome de domínio em um endereço IP, possibilitando a localização do servidor correto.

- **AAAA (IPv6 Address Record):**
	- Função: Mapeia um nome de domínio para um endereço IPv6.
	- Exemplo: `example.com -> 2001:0db8:85a3:0000:0000:8a2e:0370:7334`
	- Uso: Semelhante ao registro A, mas para endereços IPv6.

- **CNAME (Canonical Name Record):**
	- Função: Cria um alias para outro nome de domínio.
	- Exemplo: `www.example.com -> example.com`
	- Uso: Redireciona um domínio para outro domínio, útil para subdomínios que compartilham o mesmo conteúdo.

- **MX (Mail Exchange Record):**
	- Função: Especifica servidores de correio para um domínio.
	- Exemplo: `example.com -> mail.example.com (priority 10)`
	- Uso: Direciona os emails para os servidores responsáveis pelo recebimento de emails para o domínio.

- **NS (Name Server Record):**
	- Função: Especifica os servidores DNS autoritativos para a zona.
	- Exemplo: `example.com -> ns1.example.com, ns2.example.com`
	- Uso: Informa quais servidores DNS contêm as informações oficiais para o domínio.

- **PTR (Pointer Record):**
	- Função: Mapeia um endereço IP para um nome de domínio (usado em buscas reversas).
	- Exemplo: `192.0.2.1 -> example.com`
	- Uso: Utilizado principalmente em verificações de emails para confirmar que um endereço IP corresponde a um nome de domínio.

- **TXT (Text Record):**
	- Função: Contém informação arbitrária em formato de texto.
	- Exemplo: `example.com -> "v=spf1 include:_spf.example.com ~all"`
	- Uso: Utilizado para diversas verificações de segurança, como SPF (Sender Policy Framework), que ajuda a prevenir falsificação de emails.

- **SRV (Service Locator Record):**
	- Função: Define a localização de servidores para serviços específicos.
	- Exemplo: `_sip._tcp.example.com -> sipserver.example.com:5060`
	- Uso: Utilizado para localizar serviços como VoIP, especificando o protocolo, prioridade, peso, porta e alvo.

- **CAA (Certification Authority Authorization Record):**
	- Função: Especifica quais autoridades de certificação estão autorizadas a emitir certificados para o domínio.
	- Exemplo: `example.com -> 0 issue "letsencrypt.org"`
	- Uso: Ajuda a prevenir emissão não autorizada de certificados SSL/TLS.

- **SOA (Start of Authority Record):**
	- Função: Contém informações administrativas sobre a zona DNS.
	- Exemplo: Inclui o nome do servidor primário, email do administrador, número de série, intervalos de atualização, etc.
	- Uso: Utilizado para a gestão de zonas DNS, definindo parâmetros como tempos de atualização e contatos administrativos.

### Por Que as Entradas DNS São Importantes?

- **Resolução de Nomes:** Facilitam a tradução de nomes de domínio em endereços IP, permitindo que usuários acessem sites facilmente.
- **Entrega de Email:** Direcionam emails para os servidores corretos, garantindo a entrega eficiente de mensagens.
- **Segurança:** Ajudam na verificação de autenticidade e segurança, como impedir a falsificação de emails e garantir que certificados SSL/TLS sejam emitidos corretamente.
- **Gerenciamento de Serviços:** Facilitam a localização de serviços específicos, como servidores de VoIP ou serviços web.

### Onde e como configurar essas entradas DNS?
As entradas DNS **devem ser configuradas no painel gerenciador DNS do domínio.** A forma de realizar essa configuração pode variar de provedor para provedor, mas normalmente os responsáveis técnicos pelo domínio sabem como proceder.
Deixo abaixo materiais de apoio de alguns dos principais provedores:
- [Locaweb](https://www.locaweb.com.br/ajuda/wiki/como-acessar-a-zona-de-dns-na-registro-br-registro-de-dominio/)
- [Hostgator](https://suporte.hostgator.com.br/hc/pt-br/articles/30813120385427-Como-criar-ou-alterar-um-registro-A-MX-TXT-CNAME-e-outros-na-Zona-DNS)
- [Microsoft](https://learn.microsoft.com/pt-br/windows-server/networking/technologies/ipam/add-a-dns-resource-record)
- [Google Cloud](https://cloud.google.com/dns/docs/records?hl=pt-br)
- [Hostinger](https://www.hostinger.com.br/tutoriais/como-usar-editor-de-zona-dns-hostinger)

## O Que é DKIM e SPF?
### DKIM (DomainKeys Identified Mail)
DKIM é um método de autenticação de email que permite que o destinatário verifique se um email alegadamente proveniente de um domínio específico foi realmente autorizado pelo proprietário desse domínio. Isso é feito adicionando uma assinatura digital ao cabeçalho da mensagem.


#### Como Funciona o DKIM:
1. **Assinatura Digital:** Quando um email é enviado, o servidor de envio do domínio adiciona uma assinatura digital exclusiva ao cabeçalho da mensagem. Esta assinatura é gerada utilizando uma chave privada, que é mantida em segredo pelo proprietário do domínio.
2. **Publicação da Chave Pública:** O domínio publica uma chave pública no seu registro DNS, geralmente em um registro TXT. Esta chave pública é utilizada pelos servidores de recebimento para verificar a assinatura digital.
3. **Verificação da Assinatura:** Quando o email é recebido, o servidor de recebimento usa a chave pública publicada no DNS para verificar a assinatura digital no cabeçalho da mensagem. Se a assinatura corresponder, o email é considerado autêntico.


#### Benefícios do DKIM:
- **Autenticidade:** Garante que o email não foi alterado desde que foi assinado.
- **Reputação:** Ajuda a construir uma boa reputação de envio, reduzindo a probabilidade de que os emails legítimos sejam marcados como spam.
- **Proteção Contra Spoofing:** Dificulta a falsificação de emails, pois os spammers não têm acesso à chave privada necessária para criar uma assinatura válida.

### SPF (Sender Policy Framework)
SPF é um protocolo que permite que o proprietário de um domínio especifique quais servidores de email estão autorizados a enviar emails em nome desse domínio. Isso é feito adicionando um registro TXT ao DNS do domínio.


#### Como Funciona o SPF:
1. **Registro SPF no DNS:** O proprietário do domínio cria um registro SPF no DNS, especificando os servidores de email autorizados a enviar emails para o domínio.
2. **Envio de Email:** Quando um email é enviado, o servidor de recebimento verifica o registro SPF do domínio do remetente para ver se o servidor de envio está autorizado.
3. **Verificação SPF:** O servidor de recebimento compara o endereço IP do servidor de envio com a lista de servidores autorizados no registro SPF. Se o servidor estiver autorizado, o email é considerado válido; caso contrário, pode ser marcado como spam ou rejeitado.

#### Estrutura de um Registro SPF:
Um registro SPF é um registro TXT no DNS que pode ter esta aparência:
```
v=spf1 ip4:192.0.2.0/24 include:example.com -all
```
- `v=spf1`: Indica a versão do SPF.
- `ip4:192.0.2.0/24`: Autoriza a faixa de endereços IP 192.0.2.0 a 192.0.2.255 a enviar emails.
- `include:example.com`: Autoriza os servidores de envio listados no registro SPF de example.com.
- `-all`: Indica que emails de servidores não listados no SPF devem ser rejeitados.


#### Benefícios do SPF:

- **Autorização de Envio:** Especifica claramente quais servidores estão autorizados a enviar emails em nome do domínio, ajudando a prevenir a falsificação de emails.
- **Redução de Spam:** Reduz a probabilidade de que emails falsificados sejam entregues aos destinatários, ajudando a manter a integridade da comunicação por email.
- **Facilidade de Implementação:** Configuração simples através de registros DNS.


## O Que é DMARC?
DMARC (Domain-Based Message Authentication, Reporting, and Conformance) é uma **política de autenticação de email que constrói sobre SPF e DKIM para fornecer uma forma mais robusta de autenticação.** Ele permite que os domínios **publiquem políticas que especificam a ação a ser tomada quando um email falha nas verificações de autenticação SPF ou DKIM.** Além disso, DMARC fornece relatórios sobre a conformidade dessas políticas.

### Componentes de um Registro DMARC
Um registro DMARC é um registro TXT no DNS e tem a seguinte estrutura básica:
```
_dmarc.seudominio.com TXT "v=DMARC1; p=policy; rua=mailto:rua@example.com; ruf=mailto:ruf@example.com; pct=100"

```

Aqui estão os principais componentes:
1. `v=DMARC1`: Indica a versão do DMARC.
2. `p=policy`: Especifica a política a ser aplicada. Os valores possíveis são:
	- `none`: Não tomar nenhuma ação, apenas monitorar.
	- `quarantine`: Colocar emails que falham na autenticação em quarentena (por exemplo, na pasta de spam).
	- `reject`: Rejeitar emails que falham na autenticação.
3. `rua=mailto@example.com`: Especifica o endereço de email para onde enviar relatórios agregados.
4. `ruf=mailto@example.com`: Especifica o endereço de email para onde enviar relatórios forenses (detalhados).
5. `pct=100`: Especifica a porcentagem de emails a que a política se aplica. Pode ser ajustado para valores menores durante a implementação inicial.


### Exemplo de Registro DMARC
Vamos considerar um exemplo prático de registro DMARC:
```
_dmarc.geosiga.com.br TXT "v=DMARC1; p=reject; rua=mailto:dmarc-reports@geosiga.com.br; ruf=mailto:dmarc-forensic@geosiga.com.br; pct=100"

```

#### Explicação do Exemplo

- `v=DMARC1`: Indica que este é um registro DMARC.
- `p=reject`: Emails que falham nas verificações SPF e DKIM devem ser rejeitados.
- `rua=mailto@geosiga.com.br`: Relatórios agregados são enviados para este endereço.
- `ruf=mailto@geosiga.com.br`: Relatórios forenses (mais detalhados) são enviados para este endereço.
- `pct=100`: Aplica a política a 100% dos emails.


### Configuração de um Registro DMARC
Para configurar um registro DMARC, você precisa adicionar um registro TXT ao seu DNS com o nome `_dmarc.seudominio.com` e o valor conforme a política desejada. Aqui está como você faria isso:
1. Acesse seu painel de gerenciamento DNS.
2. Adicione um novo registro TXT com as seguintes informações:
	- Nome: `_dmarc.geosiga.com.br`
	- Tipo: `TXT`
	- Valor:
```
"v=DMARC1; p=reject; rua=mailto:dmarc-reports@geosiga.com.br; ruf=mailto:dmarc-forensic@geosiga.com.br; pct=100"
```

### Benefícios do DMARC

- **Proteção Contra Falsificação:** Ajuda a prevenir spoofing e phishing, assegurando que apenas emails legítimos sejam entregues.
- **Relatórios:** Fornece informações sobre quem está enviando emails em nome do seu domínio, permitindo monitoramento e ajuste das políticas.
- **Reputação:** Melhora a reputação do domínio, garantindo que emails legítimos sejam entregues e reduzindo a probabilidade de que emails falsificados atinjam os destinatários.


### Arquitetura DMARC
![dmarc_architecture](/assets/images/2024-07-23-dns-config-and-concepts/dmarc_authentication.png)
- *Fonte: https://www.proofpoint.com/us/threat-reference/dmarc*
---

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)
