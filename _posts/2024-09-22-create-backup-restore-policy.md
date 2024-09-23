---
title: "Como Criar sua Política de Backup e Restore"
date: 2024-09-22 00:00:00:0000
categories: [sql]
tags: [sql]
---

Atualmente, não ter uma política de rotina de backup é como dar um tiro no próprio pé. Uma política de backup não é apenas uma precaução: é uma necessidade estratégica para proteger o seu negócio. Aqui estão os principais motivos pelos quais toda organização deve implementar um:

- Os sistemas podem falhar devido a falhas de hardware, ataques cibernéticos ou erros humanos, levando à perda de dados valiosos. Uma rotina de backup bem estruturada garante que, caso ocorram tais problemas, você consiga recuperar seus dados rapidamente e minimizar os danos.

- Quando ocorre perda de dados, o tempo necessário para restaurá-los afeta diretamente as operações comerciais. Uma política de backup robusta reduz o tempo de inatividade, permitindo que sua empresa retome as operações com interrupções e atrasos mínimos.

- Seja enfrentando um desastre natural, um ataque de ransomware ou uma falha em todo o sistema, uma política de backup sólida é crucial para a recuperação de desastres. Ele garante que sua empresa possa restaurar dados críticos e continuar funcionando sem perdas catastróficas.

- Uma política de backup desempenha um papel crítico na manutenção da continuidade dos negócios. Mesmo diante de eventos inesperados, sua organização pode continuar operando com interrupções mínimas, preservando a confiança dos clientes e a estabilidade operacional.

Resumindo, uma política de rotina de backup é essencial para proteger o seu negócio.

## Como Escolher a sua Política
Antes de criar ou adotar qualquer política de backup e restauração, é crucial analisar vários fatores-chave para garantir que a política esteja alinhada às suas necessidades:

### Granularidade de Dados:
- **Tamanho do banco de dados e frequência de atualizações:** Quantas tabelas e com que frequência seu banco de dados é atualizado? Compreender a escala e o nível de atividade é essencial.
- **Tipo de processamento de dados:** Seu sistema está funcionando quase em tempo real, em tempo real ou em lote? Cada um requer uma abordagem de backup diferente.
- **Duração do processamento em Batch:** Quanto tempo leva o processamento em lote? Isso afetará seu agendamento de backup.
- **Sensibilidade dos dados:** Quão críticos são seus dados? Qual seria o impacto de exclusões acidentais ou perda de dados?
- **Tolerância à perda de dados:** Você pode se dar ao luxo de perder algum dado, mesmo que valha apenas alguns minutos? Caso contrário, você precisará de uma estratégia de backup altamente granular.
- **Acordo de nível de serviço (SLA):** Você tem algum compromisso de SLA para disponibilidade e recuperação de dados? Isso determinará a frequência e o tipo de backups necessários.

### Tempo de Backup:
Identificar momentos específicos em que seu banco de dados é menos afetado pelos backups é crucial. Por exemplo, executar backups FULL a cada hora pode parecer ideal, mas se o processamento em lote ocorrer de hora em hora, poderá impactar negativamente o desempenho. 

É essencial compreender os padrões operacionais do seu banco de dados e incorporar janelas de manutenção que permitam backups eficientes sem interromper as operações normais.

### Limpeza de Backup:
Estabeleça um cronograma para exclusão de backups para gerenciar o armazenamento com eficiência. Aqui está um exemplo:

- **Backup FULL:** Início da semana (domingo) — Reter backups por 8 semanas (Manter apenas os backups dos últimos dois meses).
- **Backup DIFF:** Fim de cada dia (exceto domingo) — Exclua todos os backups DIFF após a conclusão de um backup FULL (isso garante um novo início para backups DIFF).
- **Backup de LOG:** De hora em hora (exceto 12h) - Exclua todos os backups de LOG após a conclusão de um backup FULL (isso garante um novo início para backups de LOG).

A implementação dessa estratégia de limpeza pode variar em complexidade dependendo das janelas de tempo específicas, mas é essencial para manter um sistema de backup gerenciável.

### Backup para Blob Storage na Nuvem:
Configure uma conta de armazenamento de blobs e carregue regularmente seus backups primários. Manter backups apenas no seu servidor é arriscado – se o servidor falhar, você poderá perder todos os seus dados. O armazenamento em nuvem externo fornece uma camada adicional de segurança e garante que seus backups sejam preservados mesmo no caso de falha do servidor.

## Possíveis Políticas de Backup e Restore

### Batch Processing
- **Backup FULL:** Início da semana (domingo)
- **Backup DIFF:** Fim de cada dia (00h, exceto domingo)
- **Backup de LOG:** De hora em hora (exceto 00h)

**Análise:**
Esta política é sólida para cenários de processamento em lote onde as alterações de dados são relativamente moderadas. Ao remover o backup LOG das 12h, você reduz o potencial de sobreposição com manutenção ou outras operações críticas que podem ocorrer no início de um novo dia.

**Sugestão de melhoria:** 
Considere se alguma operação significativa acontece por volta da meia-noite que possa se beneficiar de um backup de LOG. Caso contrário, esta estrutura será eficaz para manter pontos de recuperação consistentes sem sobrecarga desnecessária.

### Near Real Time
- **Backup FULL:** Duas vezes por semana (domingo e quarta-feira)
- **Backup DIFF:** Três vezes por dia (às 00h, 06h e 18h)
- **Backup de LOG:** De hora em hora (exceto 00h, 06h e 18h)

**Análise:**
Esta política fornece uma abordagem equilibrada para sistemas que necessitam de recursos de backup e recuperação quase em tempo real. O backup FULL adicional na quarta-feira reduz a dependência do backup de domingo, reduzindo potencialmente os tempos de recuperação se ocorrer uma falha no final da semana.

**Sugestão de melhoria** 
Fique atento aos horários escolhidos para os backups DIFF (00h, 06h, 18h), pois coincidem com os backups de LOG excluídos. Certifique-se de que esses horários estejam alinhados com os períodos de baixa atividade para evitar impactos no desempenho.

### Real Time
- **Backup FULL:** Fim do dia (01h)
- **Backup DIFF:** A cada 3 horas
- **Backup de LOG:** A cada 15 a 30 minutos

**Análise:**
Esta política foi projetada para ambientes altamente dinâmicos onde a perda mínima de dados é crítica. Os backups DIFF frequentes a cada 3 horas, combinados com backups LOG a cada 15 a 30 minutos, garantem que os pontos de recuperação de dados sejam bem espaçados, minimizando a potencial perda de dados.

**Sugestão de melhoria:** 
Dependendo da carga, talvez você queira padronizar o intervalo de backup do LOG para 15 ou 30 minutos de forma consistente, em vez de um intervalo. Isso simplificará o monitoramento e o ajuste de desempenho. Além disso, confirme se o horário de backup FULL à 01h não entra em conflito com o pico de uso do sistema ou com as janelas de manutenção.

## Parte 02 da Rotina de Backup Automatizado
Anteriormente neste artigo, apresentei a ideia de enviar seus backups para o Azure Blob Storage. Nesta seção, orientarei você nas etapas para conseguir isso. 

Você pode pensar nisso como uma continuação do meu artigo anterior, onde expliquei como criar automaticamente uma rotina de backup usando SQL Server e SQL Agent, você pode [ler aqui](https://medium.com/@lorenzouriel/automated-backup-routine-464217ae4f4a).

## Etapa 1: Configurar uma Conta Blob Storage
A primeira etapa é criar uma conta Blob Storage no Azure. Isso servirá como destino para os backups do seu banco de dados.

![(blob-config)(im)](/assets/images/2024-09-22-create-backup-restore-policy/blob-config.png)

#### Crie Contêineres para Backups
Para melhor organização, crie contêineres separados para cada tipo de backup (Full, Diff e Log). Isso tornará mais fácil gerenciar seus backups.

![(blob-config)(im)](/assets/images/2024-09-22-create-backup-restore-policy/container-config.png)

#### Gerar um Token SAS
Para carregar com segurança os seus backups para o Azure, você precisará de um token SAS (Assinatura de Acesso Compartilhado) com permissões de leitura e gravação. Este token permite-lhe transferir com segurança os seus ficheiros de cópia de segurança para o Blob Storage.

![(blob-config)(im)](/assets/images/2024-09-22-create-backup-restore-policy/sas-token.png)

## Etapa 2: Envio de Backups para Blob Usando `AzCopy`
#### Instale o `AzCopy`
`AzCopy` é um utilitário de linha de comando projetado para simplificar as transferências de dados de e para o Armazenamento de Blobs do Azure. Siga o guia de instalação oficial do seu sistema operacional:

- Você pode instalar aqui: https://learn.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10?tabs=dnf


#### Verifique a Instalação
Depois de instalado, navegue até o diretório de instalação `AzCopy` e verifique a instalação executando:

```bash
cd "C:\Program Files\azcopy"
azcopy --version
```
- ***Dica:** Adicione o caminho da pasta às variáveis ​​de ambiente.*

#### Carregar Backups para Blob Storage
Após a criação do backup, você poderá usar o `AzCopy` para carregá-lo no contêiner do Blob Storage. Execute o seguinte comando, substituindo o caminho do arquivo e a URL do SAS com seus próprios parâmetros:

```bash
azcopy copy "C:\Backup\MyBackupExample.bak" "https://your.blob.core.windows.net/fullbckps?sp=rw...."
```

O retorno será:

```
C:\Program Files\azcopy>azcopy copy "C:\Backup\MyBackupExample.bak" "https://your.blob.core.windows.net/fullbckps?sp=rw...."
INFO: Scanning...
INFO: Any empty folders will not be processed, because source and/or destination doesn't have full folder support

Job 701d8ab4-57fd-4e4d-523b-dcd0cc1ae89a has started
Log file is located at: C:\Users\.azcopy\701d8ab4-57fd-4e4d-523b-dcd0cc1ae89a.log

100.0 %, 1 Done, 0 Failed, 0 Pending, 0 Skipped, 1 Total, 2-sec Throughput (Mb/s): 1.2826


Job 701d8ab4-57fd-4e4d-523b-dcd0cc1ae89a summary
Elapsed Time (Minutes): 0.1003
Number of File Transfers: 1
Number of Folder Property Transfers: 0
Number of Symlink Transfers: 0
Total Number of Transfers: 1
Number of File Transfers Completed: 1
Number of Folder Transfers Completed: 0
Number of File Transfers Failed: 0
Number of Folder Transfers Failed: 0
Number of File Transfers Skipped: 0
Number of Folder Transfers Skipped: 0
Total Number of Bytes Transferred: 1238528
Final Job Status: Completed
```

Depois disso, você pode verificar seu armazenamento de Blobs do Azure:
![azure-blob-bckp](/assets/images/2024-09-22-create-backup-restore-policy/azure-blob-bckp.png)

Você pode integrar esse comando diretamente nos scripts de backup do banco de dados para automatizar o processo de transferência para o Azure.

## Etapa 3: Execute o AzCopy em um Job do SQL Server
Você pode criar um job do SQL Server Agent que executa um script do PowerShell, que por sua vez executa o comando `AzCopy`.

#### 1. Crie um Script do PowerShell que Execute o Comando `AzCopy`:

```bash
# AzCopy PowerShell script (save as azcopy.ps1)
$source = "C:\Backup\MyBackupExample.bak"
$destination = "https://your.blob.core.windows.net/fullbckps?sp=rw...."
azcopy copy $source $destination
```

#### 2. Crie um Job do SQL Server Agent para Chamar o Script do PowerShell.

```bash
EXEC msdb.dbo.sp_add_job @job_name = 'AzCopy Job'
EXEC msdb.dbo.sp_add_jobstep @job_name = 'AzCopy Job', 
    @step_name = 'Run AzCopy', 
    @subsystem = 'PowerShell', 
    @command = 'C:\path\to\azcopy.ps1'
```

Ou você pode simplesmente configurar diretamente no SSMS:
- ![enviar para blob](/assets/images/2024-09-22-create-backup-restore-policy/send-to-blob.png)

Você pode habilitar `xp_cmdshell` e executar o comando `AzCopy` diretamente do SQL Server, embora isso seja menos seguro e deva ser usado com cautela.
```sql
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```

Execute o comando `AzCopy` via `xp_cmdshell`:
```sql
EXEC xp_cmdshell 'azcopy copy "C:\Backup\MyBackupExample.bak" "https://your.blob.core.windows.net/fullbckps?sp=rw...."';
```

Dessa forma, o `AzCopy` pode fazer parte dos fluxos de trabalho de backup do SQL Server, como parte da arquitetura abaixo.

- ![policy](/assets/images/2024-09-22-create-backup-restore-policy/policy.png)

Espero que este artigo possa ajudá-lo a criar sua primeira política de Backup e Restauração!

--

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)
