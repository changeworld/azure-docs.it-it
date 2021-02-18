---
title: Uso di account di archiviazione gestiti dal cliente in Log Analytics di Monitoraggio di Azure
description: Usare il proprio account di archiviazione per scenari di Log Analytics
ms.subservice: logs
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 09/03/2020
ms.openlocfilehash: 3c5a528ada9e7239f5c53da1cae6df7ceffac918
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100617476"
---
# <a name="using-customer-managed-storage-accounts-in-azure-monitor-log-analytics"></a>Uso di account di archiviazione gestiti dal cliente in Log Analytics di Monitoraggio di Azure

Log Analytics si basa su archiviazione di Azure in diversi scenari. Questo utilizzo viene in genere gestito automaticamente. Tuttavia, in alcuni casi è necessario fornire e gestire il proprio account di archiviazione, noto anche come account di archiviazione gestito dal cliente. Questo documento illustra l'uso dell'archiviazione gestita dal cliente per log WAD/LAD, collegamento privato e crittografia della chiave gestita dal cliente (CMK). 

> [!NOTE]
> Si consiglia di non assumere una dipendenza dal contenuto Log Analytics caricamenti nell'archiviazione gestita dal cliente, dato che la formattazione e il contenuto possono cambiare.

## <a name="ingesting-azure-diagnostics-extension-logs-wadlad"></a>Inserimento di log estensioni di Diagnostica di Azure (WAD/LAD)
Gli agenti di estensione Diagnostica di Azure (detti anche WAD e LAD per gli agenti Windows e Linux rispettivamente) raccolgono vari log del sistema operativo e li archiviano in un account di archiviazione gestito dal cliente. È quindi possibile inserire questi log in Log Analytics per esaminarli e analizzarli.
### <a name="how-to-collect-azure-diagnostics-extension-logs-from-your-storage-account"></a>Come raccogliere i log di estensione Diagnostica di Azure dall'account di archiviazione
Connettere l'account di archiviazione all'area di lavoro di Log Analytics come origine dati di archiviazione usando [il portale di Azure](../essentials/diagnostics-extension-logs.md#collect-logs-from-azure-storage) o chiamando l' [API di archiviazione Insights](/rest/api/loganalytics/storage%20insights/createorupdate).

Tipi di dati supportati:
* syslog
* Eventi Windows
* Service Fabric
* Eventi ETW
* Log IIS

## <a name="using-private-links"></a>Uso di collegamenti privati
Gli account di archiviazione gestiti dal cliente vengono usati per inserire log personalizzati o log di IIS quando si usano i collegamenti privati per connettersi alle risorse di monitoraggio di Azure. Il processo di inserimento di questi tipi di dati carica prima i log in un account di archiviazione di Azure intermediario e li inserisce in un'area di lavoro. 

### <a name="using-a-customer-managed-storage-account-over-a-private-link"></a>Uso di un account di archiviazione gestito dal cliente tramite un collegamento privato
#### <a name="workspace-requirements"></a>Requisiti dell'area di lavoro
Quando ci si connette a monitoraggio di Azure tramite un collegamento privato, gli agenti Log Analytics possono inviare i log solo alle aree di lavoro accessibili tramite un collegamento privato. Questo requisito significa che è necessario:
* Configurare un oggetto ambito di collegamento privato (AMPLS) di monitoraggio di Azure
* Connetterla alle aree di lavoro
* Connettere la AMPLS alla rete tramite un collegamento privato. 

Per altre informazioni sulla procedura di configurazione AMPLS, vedere [usare il collegamento privato di Azure per connettere in modo sicuro le reti a monitoraggio di Azure](./private-link-security.md). 

#### <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione
Perché l'account di archiviazione si connetta correttamente al collegamento privato, è necessario:
* Trovarsi nella VNet o in una rete con peering e connettersi alla VNet tramite un collegamento privato.
* Trovarsi nella stessa area dell'area di lavoro a cui è collegato.
* Consentire a monitoraggio di Azure di accedere all'account di archiviazione. Se si sceglie di consentire solo la selezione di reti per l'accesso all'account di archiviazione, è necessario selezionare l'eccezione: "Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione".
![Immagine dei servizi MS di attendibilità dell'account di archiviazione](./media/private-storage/storage-trust.png)
* Se l'area di lavoro gestisce anche il traffico da altre reti, è necessario configurare l'account di archiviazione per consentire il traffico in ingresso proveniente dalle reti/Internet pertinenti.

### <a name="using-a-customer-managed-storage-account-for-cmk-data-encryption"></a>Uso di un account di archiviazione gestito dal cliente per la crittografia dei dati CMK
Archiviazione di Azure crittografa tutti i dati inattivi in un account di archiviazione. Per impostazione predefinita, USA chiavi gestite da Microsoft (MMK) per crittografare i dati. Tuttavia, archiviazione di Azure consente anche di usare CMK da Azure Key Vault per crittografare i dati di archiviazione. È possibile importare le proprie chiavi in Azure Key Vault oppure è possibile usare le API Azure Key Vault per generare chiavi.
#### <a name="cmk-scenarios-that-require-a-customer-managed-storage-account"></a>Scenari CMK che richiedono un account di archiviazione gestito dal cliente
* Crittografia di query di avviso di log con CMK
* Crittografia delle query salvate con CMK

#### <a name="how-to-apply-cmk-to-customer-managed-storage-accounts"></a>Come applicare CMK a account di archiviazione gestiti dal cliente
##### <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione
L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse. Per altre informazioni sulla crittografia di archiviazione di Azure e sulla gestione delle chiavi, vedere [crittografia di archiviazione di Azure per dati](../../storage/common/storage-service-encryption.md)inattivi.

##### <a name="apply-cmk-to-your-storage-accounts"></a>Applicare CMK agli account di archiviazione
Per configurare l'account di archiviazione di Azure per l'uso di CMK con Azure Key Vault, usare il [portale di Azure](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json), [PowerShell](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)o l' [interfaccia](../../storage/common/customer-managed-keys-configure-key-vault.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json)della riga di comando. 

## <a name="link-storage-accounts-to-your-log-analytics-workspace"></a>Collegare gli account di archiviazione all'area di lavoro Log Analytics
### <a name="using-the-azure-portal"></a>Uso del portale di Azure
Nella portale di Azure aprire il menu area di lavoro e selezionare *account di archiviazione collegati*. Viene aperto un pannello che Mostra gli account di archiviazione collegati in base ai casi d'uso indicati in precedenza (inserimento su collegamento privato, applicazione di CMK a query salvate o ad avvisi).
![Immagine del pannello account di archiviazione collegati ](./media/private-storage/all-linked-storage-accounts.png) selezionando un elemento nella tabella vengono aperti i dettagli dell'account di archiviazione, in cui è possibile impostare o aggiornare l'account di archiviazione collegato per questo tipo. 
![Collegare un'immagine ](./media/private-storage/link-a-storage-account-blade.png) del pannello dell'account di archiviazione è possibile usare lo stesso account per casi di utilizzo diversi, se si preferisce.

### <a name="using-the-azure-cli-or-rest-api"></a>Uso dell'interfaccia della riga di comando o dell'API REST
È anche possibile collegare un account di archiviazione all'area di lavoro tramite l'interfaccia della riga di comando di [Azure](/cli/azure/monitor/log-analytics/workspace/linked-storage) o l' [API REST](/rest/api/loganalytics/linkedstorageaccounts).

I valori dataSourceType applicabili sono:
* CustomLogs: per usare l'account di archiviazione per i log personalizzati e l'inserimento di log IIS
* Query: per usare l'account di archiviazione per archiviare le query salvate (obbligatorio per la crittografia CMK)
* Avvisi: per usare l'account di archiviazione per archiviare gli avvisi basati su log, necessari per la crittografia CMK


## <a name="managing-linked-storage-accounts"></a>Gestione degli account di archiviazione collegati

### <a name="create-or-modify-a-link"></a>Creare o modificare un collegamento
Quando si collega un account di archiviazione a un'area di lavoro, Log Analytics inizierà a usarlo al posto dell'account di archiviazione di proprietà del servizio. È possibile 
* Registrare più account di archiviazione per suddividere il carico dei log tra di essi
* Riutilizzare lo stesso account di archiviazione per più aree di lavoro

### <a name="unlink-a-storage-account"></a>Scollegare un account di archiviazione
Per interrompere l'uso di un account di archiviazione, scollegare lo spazio di archiviazione dall'area di lavoro. Lo scollegamento di tutti gli account di archiviazione da un'area di lavoro significa che Log Analytics tenterà di utilizzare gli account di archiviazione gestiti dal servizio. Se la rete ha accesso limitato a Internet, queste archiviazioni potrebbero non essere disponibili e gli eventuali scenari che si basano sull'archiviazione avranno esito negativo.

### <a name="replace-a-storage-account"></a>Sostituire un account di archiviazione
Per sostituire un account di archiviazione usato per l'inserimento,
1.  **Creare un collegamento a un nuovo account di archiviazione.** Gli agenti di registrazione otterranno la configurazione aggiornata e inizieranno a inviare i dati anche alla nuova risorsa di archiviazione. Il processo potrebbe richiedere alcuni minuti.
2.  **Scollegare quindi l'account di archiviazione precedente in modo che gli agenti interrompano la scrittura nell'account rimosso.** Il processo di inserimento continua a leggere i dati da questo account fino a quando non vengono inseriti tutti. Non eliminare l'account di archiviazione finché non vengono visualizzati tutti i log inseriti.

### <a name="maintaining-storage-accounts"></a>Gestione degli account di archiviazione
#### <a name="manage-log-retention"></a>Gestire la conservazione dei log
Quando si usa il proprio account di archiviazione, la conservazione dipende dall'utente. Log Analytics non eliminerà i log archiviati nella risorsa di archiviazione privata. È invece necessario configurare un criterio per gestire il carico in base alle proprie preferenze.

#### <a name="consider-load"></a>Prendere in considerazione il carico
Gli account di archiviazione possono gestire un determinato carico di richieste di lettura e scrittura prima di avviare le richieste di limitazione (per altre informazioni, vedere [obiettivi di scalabilità e prestazioni per l'archiviazione BLOB](../../storage/common/scalability-targets-standard-account.md)). La limitazione influiscono sul tempo necessario per inserire i log. Se l'account di archiviazione è sovraccarico, registrare un account di archiviazione aggiuntivo per suddividere il carico tra di essi. Per monitorare la capacità e le prestazioni dell'account di archiviazione, esaminare le [informazioni dettagliate nella portale di Azure]( https://docs.microsoft.com/azure/azure-monitor/insights/storage-insights-overview).

### <a name="related-charges"></a>Addebiti correlati
Gli account di archiviazione vengono addebitati in base al volume dei dati archiviati, al tipo di archiviazione e al tipo di ridondanza. Per informazioni dettagliate, vedere [Prezzi per i BLOB in blocchi](https://azure.microsoft.com/pricing/details/storage/blobs) e [Prezzi di Archiviazione tabelle di Azure](https://azure.microsoft.com/pricing/details/storage/tables).


## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull' [uso del collegamento privato di Azure per connettere in modo sicuro le reti a monitoraggio di Azure](private-link-security.md)
- Informazioni sulle [chiavi gestite dal cliente di monitoraggio di Azure](../logs/customer-managed-keys.md)
