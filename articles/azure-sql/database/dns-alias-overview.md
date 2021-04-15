---
title: Alias DNS
description: Le applicazioni possono connettersi a un alias per il nome del server per database SQL di Azure. Nel frattempo, è possibile modificare il database SQL a cui l'alias fa riferimento in qualsiasi momento, per semplificare i test e così via.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: genemi, jrasnick, vanto
ms.date: 06/26/2019
ms.openlocfilehash: 6ef268b349d5a21cdbadd55ffd2199a35f650e5b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376291"
---
# <a name="dns-alias-for-azure-sql-database"></a>Alias DNS per il database SQL di Azure
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Il database SQL di Azure ha un server DNS (Domain Name System). PowerShell e le API REST accettano chiamate [per creare e gestire alias DNS](#anchor-powershell-code-62x) per il nome logico del server [SQL.](logical-servers.md)

È possibile usare un *alias DNS* al posto del nome del server. I programmi client possono usare l'alias nelle stringhe di connessione. L'alias DNS fornisce un livello di conversione che consente di reindirizzare i programmi client a server diversi. Questo livello consente di evitare le difficoltà legate alla ricerca e alla modifica di tutti i client e delle relative stringhe di connessione.

Gli usi comuni per un alias DNS includono i casi seguenti:

- Creare un nome facile da ricordare per un server.
- Durante lo sviluppo iniziale, l'alias può fare riferimento a un server di test. Quando l'applicazione viene resa disponibile, è possibile modificare l'alias in modo che faccia riferimento al server di produzione. La transizione dal test alla produzione non richiede alcuna modifica alle configurazioni di diversi client che si connettono al server.
- Si supponga che l'unico database nell'applicazione sia spostato in un altro server. È possibile modificare l'alias senza dover modificare le configurazioni di diversi client.
- Durante un'interruzione a livello di area si usa il ripristino geografico per ripristinare il database in un server e in un'area diversi. È possibile modificare l'alias esistente in modo che punti al nuovo server in modo che l'applicazione client esistente possa riconnettirsi.

## <a name="domain-name-system-dns-of-the-internet"></a>DNS (Domain Name System) in Internet

Internet si basa su DNS. Il DNS converte i nomi descrittivi nel nome del server.

## <a name="scenarios-with-one-dns-alias"></a>Scenari con un alias DNS

Si supponga di dover passare il sistema a un nuovo server. In passato era necessario trovare e aggiornare ogni stringa di connessione in ogni programma client. Ora invece se le stringhe di connessione usano un alias DNS, è necessario aggiornare solo una proprietà dell'alias.

La funzionalità di alias DNS del database SQL di Azure può essere utile negli scenari seguenti:

### <a name="test-to-production"></a>Da ambiente di test ad ambiente di produzione

Quando si inizia a sviluppare programmi client, fare in modo che usino un alias DNS nelle stringhe di connessione. Si fa in modo che le proprietà dell'alias puntino a una versione di test del server.

Successivamente, quando il nuovo sistema diventa disponibile nell'ambiente di produzione, è possibile aggiornare le proprietà dell'alias in modo che puntino al server di produzione. Non è necessario apportare modifiche ai programmi client.

### <a name="cross-region-support"></a>Supporto per più aree

Un ripristino di emergenza potrebbe spostare il server in un'area geografica diversa. Per un sistema che usava un alias DNS, è possibile evitare di dover trovare e aggiornare tutte le stringhe di connessione per tutti i client. È invece possibile aggiornare un alias in modo che faccia riferimento al nuovo server che ora ospita il database SQL di Azure.

## <a name="properties-of-a-dns-alias"></a>Proprietà di un alias DNS

Le proprietà seguenti si applicano a ogni alias DNS per il server:

- *Nome univoco:* Ogni nome alias creato è univoco in tutti i server, così come i nomi dei server.
- *Server necessario:* è possibile creare un alias DNS solo se fa riferimento a un solo server, che deve esistere già. Un alias aggiornato deve sempre fare riferimento a un solo server esistente.
  - Quando si elimina un server, il sistema di Azure elimina anche tutti gli alias DNS che fanno riferimento al server.
- *Nessuna associazione alle aree:* gli alias DNS non sono associati a un'area. Gli alias DNS possono essere aggiornati per fare riferimento a un server che si trova in qualsiasi area geografica.
  - Quando si aggiorna un alias per fare riferimento a un altro server, tuttavia, entrambi i server devono trovarsi nella stessa *sottoscrizione* di Azure.
- *Autorizzazioni:* per gestire un alias DNS, l'utente deve avere autorizzazioni di *collaboratore server* o superiori. Per altre informazioni, vedere [Introduzione al controllo degli accessi in](../../role-based-access-control/overview.md)base al ruolo di Azure nella portale di Azure .

## <a name="manage-your-dns-aliases"></a>Gestire gli alias DNS

Per gestire a livello di codice gli alias DNS, sono disponibili le API REST e i cmdlet di PowerShell.

### <a name="rest-apis-for-managing-your-dns-aliases"></a>API REST per la gestione degli alias DNS

La documentazione per le API REST è disponibile nel sito Web seguente:

- [Azure SQL Database REST API](/rest/api/sql/) (API REST del database SQL di Azure)

Le API REST sono inoltre disponibili in GitHub nella pagina:

- [database SQL di Azure API REST alias DNS](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/sql/resource-manager/Microsoft.Sql/preview/2017-03-01-preview/serverDnsAliases.json)

<a name="anchor-powershell-code-62x"></a>

### <a name="powershell-for-managing-your-dns-aliases"></a>PowerShell per la gestione degli alias DNS

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager PowerShell è ancora supportato, ma tutto lo sviluppo futuro è per il modulo Az.Sql. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

Sono disponibili cmdlet di PowerShell per chiamare le API REST.

Un esempio di codice relativo ai cmdlet di PowerShell usati per gestire gli alias DNS è disponibile in:

- [PowerShell per l'alias DNS per il database SQL di Azure](dns-alias-powershell-create.md)

I cmdlet usati nell'esempio di codice sono i seguenti:

- [New-AzSqlServerDnsAlias:](/powershell/module/az.Sql/New-azSqlServerDnsAlias)crea un nuovo alias DNS nel database SQL di Azure del servizio. L'alias fa riferimento al server 1.
- [Get-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Get-azSqlServerDnsAlias)ottiene ed elenca tutti gli alias DNS assegnati al server 1.
- [Set-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Set-azSqlServerDnsAlias)modifica il nome del server a cui l'alias è configurato per fare riferimento, dal server 1 al server 2.
- [Remove-AzSqlServerDnsAlias:](/powershell/module/az.Sql/Remove-azSqlServerDnsAlias)rimuovere l'alias DNS dal server 2 usando il nome dell'alias.

## <a name="limitations"></a>Limitazioni

Attualmente l'alias DNS ha le limitazioni seguenti:

- *Ritardo fino a 2 minuti:* per l'aggiornamento o la rimozione di un alias DNS sono necessari fino a 2 minuti.
  - Indipendentemente da qualsiasi breve ritardo, l'alias smette immediatamente di reindirizzare le connessioni client al server legacy.
- *Ricerca DNS:* per il momento, l'unico modo autorevole per controllare a quale server fa riferimento un alias DNS specifico consiste nell'eseguire una [ricerca DNS](/windows-server/administration/windows-commands/nslookup).
- _Il controllo tabelle non è supportato:_ Non è possibile usare un alias DNS in un server in cui è abilitato il *controllo delle tabelle* in un database.
  - Il controllo tabelle è deprecato.
  - È consigliabile passare al [controllo BLOB](../../azure-sql/database/auditing-overview.md).

## <a name="related-resources"></a>Risorse correlate

- [Panoramica della continuità aziendale del database SQL di Azure](business-continuity-high-availability-disaster-recover-hadr-overview.md), incluso il ripristino di emergenza.
- [Informazioni di riferimento sulle API REST di Azure](/rest/api/azure/)
- [API alias dns del server](/rest/api/sql/2020-11-01-preview/serverdnsaliases)

## <a name="next-steps"></a>Passaggi successivi

- [PowerShell per l'alias DNS per il database SQL di Azure](dns-alias-powershell-create.md)
