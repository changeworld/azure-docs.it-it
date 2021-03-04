---
title: Fornire le credenziali del server per individuare le applicazioni, le dipendenze e i database e le istanze di SQL Server
description: Informazioni su come fornire le credenziali del server in Gestione configurazione Appliance
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: 2359855ce3949eb022a03f6e8e2dbc05f98907db
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054773"
---
# <a name="provide-server-credentials-to-discover-applications-dependencies-and-sql-server-instances-and-databases"></a>Fornire le credenziali del server per individuare le applicazioni, le dipendenze e i database e le istanze di SQL Server

Seguire questo articolo per informazioni su come aggiungere più credenziali del server in Gestione configurazione Appliance per eseguire l'inventario software (individuare le applicazioni installate), l'analisi delle dipendenze senza agenti e individuare SQL Server istanze e database.

> [!Note]
> L'individuazione e la valutazione di SQL Server istanze e database in esecuzione nell'ambiente VMware sono ora in anteprima. Per provare questa funzionalità, usare [**questo collegamento**](https://aka.ms/AzureMigrate/SQL) per creare un progetto nell'area **Australia orientale** . Se si dispone già di un progetto in Australia orientale e si vuole provare questa funzionalità, assicurarsi di aver completato questi [**prerequisiti**](how-to-discover-sql-existing-project.md) nel portale.

Il [Azure migrate Appliance](migrate-appliance.md) è un'appliance semplice usata da Azure migrate: server assessment per individuare i server locali in esecuzione nell'ambiente VMware e inviare i metadati di configurazione e prestazioni del server ad Azure. Il dispositivo può essere usato anche per eseguire l'inventario software, l'analisi delle dipendenze senza agenti e l'individuazione di SQL Server istanze e database.

Se si desidera utilizzare queste funzionalità, è possibile fornire le credenziali del server attenendosi alla procedura descritta di seguito. Il dispositivo tenterà di eseguire automaticamente il mapping delle credenziali ai server per eseguire le funzionalità di individuazione.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>Aggiungere le credenziali per i server in esecuzione nell'ambiente VMware

### <a name="types-of-server-credentials-supported"></a>Tipi di credenziali del server supportate

È possibile aggiungere più credenziali del server in Gestione configurazione appliance che possono essere di dominio, non di dominio (Windows o Linux) o SQL Server credenziali di autenticazione.

I tipi di credenziali del server supportati sono elencati nella tabella seguente:

Tipo di credenziali | Descrizione
--- | ---
**Credenziali di dominio** | È possibile aggiungere le **credenziali di dominio** selezionando l'opzione dall'elenco a discesa nel campo modale **Aggiungi credenziali** . <br/><br/> Per fornire le credenziali di dominio, è necessario specificare il **nome di dominio** che deve essere specificato nel formato FQDN (ad esempio prod.Corp.contoso.com). <br/><br/> È anche necessario specificare un nome descrittivo per le credenziali, il nome utente e la password. <br/><br/> Le credenziali di dominio aggiunte verranno convalidate automaticamente per l'autenticità rispetto all'Active Directory del dominio. Ciò consente di evitare i blocchi degli account quando l'appliance tenta di eseguire il mapping delle credenziali di dominio rispetto ai server individuati. <br/><br/> L'appliance non tenterà di eseguire il mapping delle credenziali di dominio che non hanno superato la convalida. È necessario disporre almeno di una credenziale di dominio convalidata correttamente o almeno una credenziale non di dominio per procedere con l'inventario software.<br/><br/>Le credenziali di dominio mappate automaticamente ai server Windows verranno usate per eseguire l'inventario software e possono essere usate anche per individuare SQL Server istanze e _i database, se è stata configurata la modalità di autenticazione di Windows in SQL Server_.<br/> [Altre](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authentication-in-sql-server) informazioni sui tipi di modalità di autenticazione supportati in SQL Server.
**Credenziali non di dominio (Windows/Linux)** | È possibile aggiungere **Windows (non di dominio)** o **Linux (non dominio)** selezionando l'opzione richiesta nell'elenco a discesa in **Aggiungi credenziali** modale. <br/><br/> È necessario specificare un nome descrittivo per le credenziali, il nome utente e la password.
**Credenziali di autenticazione SQL Server** | È possibile aggiungere **SQL Server** credenziali di autenticazione selezionando l'opzione dall'elenco a discesa in **Aggiungi credenziali** modale. <br/><br/> È necessario specificare un nome descrittivo per le credenziali, il nome utente e la password. <br/><br/> È possibile aggiungere questo tipo di credenziali per individuare SQL Server istanze e i database in esecuzione nell'ambiente VMware, se è stata configurata la modalità di autenticazione SQL Server in SQL Server.<br/> [Altre](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/authentication-in-sql-server) informazioni sui tipi di modalità di autenticazione supportati in SQL Server.<br/><br/> È necessario specificare almeno una credenziale del dominio convalidata correttamente o almeno una credenziale Windows (non di dominio), in modo che l'appliance possa completare l'inventario software per individuare SQL installato nei server prima di utilizzare le credenziali di autenticazione SQL Server per individuare le istanze di SQL Server e i database.

Controllare le autorizzazioni necessarie per le credenziali di Windows/Linux per eseguire l'inventario software, l'analisi delle dipendenze senza agenti e individuare SQL Server istanze e database.

### <a name="required-permissions"></a>Autorizzazioni necessarie

La tabella seguente elenca le autorizzazioni necessarie per le credenziali del server fornite nell'appliance per eseguire le rispettive funzionalità:

Funzionalità | Credenziali di Windows | Credenziali Linux
---| ---| ---
**Inventario software** | Account utente Guest | Normale/normale account utente (autorizzazioni di accesso non sudo)
**Individuazione di istanze e database di SQL Server** | Account utente membro del ruolo del server sysadmin. | _Attualmente non supportato_
**Analisi delle dipendenze senza agente** | Account di dominio o non di dominio (locale) con autorizzazioni amministrative | Account utente radice o <br/> un account con queste autorizzazioni per i file/bin/netstat e/bin/ls: CAP_DAC_READ_SEARCH e CAP_SYS_PTRACE.<br/><br/> Configurare queste funzionalità usando i comandi seguenti: <br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH, CAP_SYS_PTRACE = EP/bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>Procedure consigliate per fornire le credenziali

- È consigliabile creare un account utente di dominio dedicato con le [autorizzazioni necessarie](add-server-credentials.md#required-permissions), con ambito per eseguire l'inventario software, l'analisi delle dipendenze senza agenti e l'individuazione di SQL Server istanze e database nei server desiderati.
- È consigliabile specificare almeno una credenziale del dominio convalidata correttamente o almeno una credenziale non di dominio per avviare l'inventario software.
- Per individuare SQL Server istanze e i database, è possibile specificare le credenziali del dominio, se la modalità di autenticazione di Windows è stata configurata nei server SQL.
-  È anche possibile fornire le credenziali di autenticazione SQL Server se è stata configurata la modalità di autenticazione SQL Server su SQL Server, ma è consigliabile specificare almeno una credenziale di dominio convalidata correttamente o almeno una credenziale Windows (non di dominio), in modo che l'appliance possa prima completare l'inventario software.

## <a name="credentials-handling-on-appliance"></a>Gestione delle credenziali nel dispositivo

- Tutte le credenziali fornite nell'appliance Configuration Manager vengono archiviate localmente nel server del dispositivo e non inviate ad Azure.
- Le credenziali archiviate nel server appliance vengono crittografate tramite Data Protection API (DPAPI).
- Dopo aver aggiunto le credenziali, l'appliance tenta di eseguire automaticamente il mapping delle credenziali per eseguire l'individuazione nei rispettivi server.
- L'appliance usa le credenziali mappate automaticamente in un server per tutti i cicli di individuazione successivi fino a quando le credenziali non sono in grado di recuperare i dati di individuazione richiesti. Se le credenziali smettono di funzionare, il dispositivo tenta di nuovo di eseguire il mapping dall'elenco di credenziali aggiunte e continua l'individuazione in corso sul server.
- Le credenziali di dominio aggiunte verranno convalidate automaticamente per l'autenticità rispetto all'Active Directory del dominio. Ciò consente di evitare i blocchi degli account quando l'appliance tenta di eseguire il mapping delle credenziali di dominio rispetto ai server individuati. L'appliance non tenterà di eseguire il mapping delle credenziali di dominio che non hanno superato la convalida.
- Se il dispositivo non è in grado di eseguire il mapping delle credenziali di dominio o non di dominio in un server, nello stato del progetto viene visualizzato lo stato "credenziali non disponibili".

## <a name="next-steps"></a>Passaggi successivi

Esaminare le esercitazioni per l' [individuazione di server in esecuzione nell'ambiente VMware](tutorial-discover-vmware.md)