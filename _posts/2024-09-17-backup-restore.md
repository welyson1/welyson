---
title: "Backup & Restore"
date: 2024-09-17 00:00:00:0000
categories: [sql]
tags: [sql]
---

# Backup & Restore
A política que deve ser discutida no trabalho é a de Backup e Restore.

É o tipo de conversa difícil que precisa acontecer. Pode parecer que não é uma prioridade, mas quando a bomba explodir…

Venho enfrentando o desafio de estruturar uma política de backup para alguns sistemas.

Aprendemos sempre muito com os desafios do trabalho, e aprendemos ainda mais quando ensinamos outras pessoas.

A ideia é compartilhar exatamente o que você precisa saber antes de começar a discutir uma política de Backup e Restore.

Falarei sobre os tipos de Backup e como configurar seu banco de dados para esses tipos de backup.

## Recovery Model
No SQL Server, o Recovery Model é uma propriedade crítica do banco de dados que afeta como as transações são registradas, gerenciadas e como o banco de dados pode ser recuperado em caso de falha.

Existem três modelos principais de recuperação: Simple, Full e Bulk-Logged.

Cada um tem suas próprias características e casos de uso para estratégias de backup e restauração.

### Simple Recovery Model:
- **Backup:** No modelo de recuperação Simples, apenas são permitidos backups completos do banco de dados. Os backups do log de transações não são suportados porque o log de transações é automaticamente truncado (limpo) após cada ponto de verificação.

- **Restore:** Com a recuperação simples, você só pode restaurar a partir do último backup completo do banco de dados. A recuperação pontual não é possível porque os backups do log de transações não são feitos.

Ideal para sistemas onde a recuperação de dados para um momento específico não é crítica (por exemplo, bancos de dados de desenvolvimento, sistemas com dados menos críticos).

**Exemplo de configuração do modelo de recuperação Simples:**

```sql
ALTER DATABASE your_db SET RECOVERY SIMPLE;
```

### Full Recovery Model:
- **Backup:** No modelo de recuperação completa, há suporte para backups completos de banco de dados e backups de log de transações. Isso permite a recuperação pontual.

- **Restore:** Você pode restaurar a partir do último backup completo do banco de dados ou aplicar backups de log de transações para recuperar até um momento específico.

Ideal para sistemas onde a integridade dos dados e a perda mínima de dados são críticas, como sistemas financeiros ou transacionais.

**Exemplo de configuração do modelo de recuperação completa:**

```sql
ALTER DATABASE your_db SET RECOVERY FULL;
```

### Bulk-Logged Recovery Model:
- **Backup:** Semelhante ao modelo de recuperação completa, mas fornece registro mais eficiente para determinadas operações em massa, como inserções em massa. No entanto, a recuperação pontual é limitada.

- **Restore:** Semelhante ao modelo de recuperação completa, mas há restrições quanto aos tipos de operações que podem ser recuperadas usando o log de transações.

**Exemplo de configuração do modelo de recuperação Bulk-Logged:**

```sql
ALTER DATABASE your_db SET RECOVERY BULK_LOGGED;
```

Nos modelos de recuperação Full e Bulk-Logged, os ***backups regulares do log de transações são cruciais para evitar que o log de transações cresça indefinidamente.***

## Tipos de Backup
### Full Backup: 
Um backup completo captura todo o banco de dados, incluindo todos os dados e objetos de esquema. É bom porque fornece uma cópia completa do banco de dados, simplificando o processo de restauração.

Precisamos ter cuidado porque backups completos podem consumir tempo e recursos, especialmente para bancos de dados grandes.

Normalmente usado como backup de linha de base e em combinação com outros tipos de backup.

**Exemplo:**

```sql
BACKUP DATABASE your_db
TO DISK = 'C:\Backup\FullBackup.bak'
WITH INIT;
```

### Differential Backup:
Um backup diferencial captura apenas as alterações feitas desde o último backup completo. É mais rápido que um backup completo e requer menos espaço de armazenamento em comparação com backups completos.

O modelo de restauração envolve o último backup completo e o último backup diferencial, que pode demorar mais que outros modelos.

Melhor para bancos de dados onde as alterações ocorrem regularmente, mas os backups completos são executados com menos frequência.

**Exemplo:**

```sql
BACKUP DATABASE your_db
TO DISK = 'C:\Backup\DifferentialBackup.bak'
WITH DIFFERENTIAL, INIT;
```

### Transaction Log Backup:
Captura as alterações feitas no banco de dados desde o último log de transações ou backup completo.

Permite a recuperação pontual, reduzindo a perda de dados em caso de falha.

Requer gerenciamento cuidadoso de backups de log de transações para evitar o crescimento de arquivos de log — uma boa dica é ter mais de um backup de log por dia.

Crucial para bancos de dados onde a perda mínima de dados é aceitável, como sistemas financeiros ou críticos.

**Exemplo:**

```sql
BACKUP LOG your_db
TO DISK = 'C:\Backup\LogBackup.trn'
WITH INIT;
```

***Veja como funciona:***
1. Backup completo à 1h.
2. Backup do log de transações às 2h (captura alterações de 1h às 2h).
3. Backup diferencial às 3h (captura todas as alterações desde 1h).
4. Backup do log de transações às 4h (captura alterações das 2h às 4h).

![types-backups](/assets/images/2024-09-17-backup-restore/types-backups.webp)


***Pontos Chaves:***
- **Differential Backup:** Os backups diferenciais rastreiam as alterações desde o último backup completo, tornando-as cumulativas. Cada backup diferencial é maior que o anterior, pois inclui todas as alterações desde o último backup completo.
- **Transaction Log Backup:** Os backups do log de transações rastreiam as alterações de forma incremental desde o último backup do log de transações, o que permite uma recuperação pontual precisa e backups incrementais muito menores.

### Copy-Only Backup:
Um backup independente que não afeta a sequência normal de backup. Isso não afeta a cadeia de backup diferencial nem afeta os backups de log.

Útil para criar backups ad-hoc sem interromper a estratégia de backup existente. Não participa da sequência de restauração para backups diferenciais ou de log.

Útil para criar backups únicos para fins específicos sem afetar a sequência regular de backup.

**Exemplo:**

```sql
BACKUP DATABASE your_db
TO DISK = 'C:\Backup\CopyOnlyBackup.bak'
WITH COPY_ONLY, INIT;
```

### Snapshot Backup:
Uma cópia pontual de todo o banco de dados, capturada sem interromper a operação do banco de dados.

Adequado para criar cópias para testes, relatórios ou outros fins não relacionados à produção, costuma acontecer em replicações e migração de banco de dados.

> Os backups de instantâneos normalmente são feitos no nível de armazenamento, usando comandos específicos do sistema de armazenamento. O próprio SQL Server não oferece suporte direto a backups de snapshots por meio de comandos Transact-SQL. Consulte a documentação do seu sistema de armazenamento para recursos de snapshot — Microsoft

## NORECOVERY e RECOVERY
No SQL Server, as opções NORECOVERY e RECOVERY são usadas durante o processo de restauração, especificamente no contexto de restauração de backups. Estas opções determinam o estado do banco de dados após uma operação de restauração.

### NORECOVERY:
Quando você usa a opção NORECOVERY, significa que você está restaurando um backup, mas não colocando o banco de dados online imediatamente.

Essa opção geralmente é usada quando você tem vários backups para aplicar (como backups completos e backups de log de transações subsequentes) e deseja aplicá-los sequencialmente antes de disponibilizar o banco de dados para os usuários.

Esta opção deixa o banco de dados em estado de “restauração”, permitindo a aplicação de restaurações adicionais.

**Exemplo:**

```sql
-- Restaure um backup completo com NORECOVERY
RESTORE DATABASE your_db FROM DISK = 'C:\Backup\FullBackup.bak' WITH NORECOVERY;

-- Restaure backups de log de transações com NORECOVERY
RESTORE LOG your_db FROM DISK = 'C:\Backup\LogBackup_1.trn' WITH NORECOVERY;
RESTORE LOG your_db FROM DISK = 'C:\Backup\LogBackup_2.trn' WITH NORECOVERY;

-- Finalmente, coloque o banco de dados online
RESTORE DATABASE your_db WITH RECOVERY;
```

### RECOVERY:
Quando você usa a opção RECOVERY, significa que você está colocando o banco de dados online após a operação de restauração. Esta é a etapa final do processo de restauração.

Depois de usar RECOVERY, os usuários podem se conectar ao banco de dados e ele se torna operacional.

Esta opção normalmente é usada quando você aplica todos os backups necessários e deseja que o banco de dados esteja disponível para operações normais.

**Exemplo:**

```sql
-- Restaure um backup completo com RECOVERY
RESTORE DATABASE your_db FROM DISK = 'C:\Backup\FullBackup.bak' WITH RECOVERY;
```

Em resumo, a sequência geralmente envolve o uso de NORECOVERY para restaurações intermediárias (backup completo e backups de log subsequentes) e, em seguida, o uso de RECOVERY para a restauração final para disponibilizar o banco de dados. É uma abordagem comum ao executar a recuperação pontual ou ao aplicar uma cadeia de backups para restaurar um banco de dados para um estado específico.


#### Dica: Use DBCC CHECKDB
Após a restauração, execute DBCC CHECKDB para verificar a integridade do banco de dados.

**Exemplo:**

```sql
USE your_db;
DBCC CHECKDB ([your_db]);
```

Se **DBCC CHECKDB** relatar erros, avalie a gravidade e tome as medidas apropriadas. As opções podem incluir a restauração de um backup diferente, a correção manual de problemas específicos ou a busca de assistência do suporte da Microsoft.

Isso que você precisa saber para começar a pensar em uma solução completa de Backup & Restore!

---

*Obrigado por ler até aqui!*

[*Fazendo o que precisa ser feito.*](https://linktr.ee/lorenzo_uriel)
