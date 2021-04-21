---
title: Azure Key Vault'eliminazione | Microsoft Docs
description: L'eliminazione Azure Key Vault consente di ripristinare gli insiemi di credenziali delle chiavi e gli oggetti dell'insieme di credenziali delle chiavi eliminati, ad esempio chiavi, segreti e certificati.
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 03/31/2021
ms.openlocfilehash: 52cd7742f3c6961350f907cde8ffe19235cff9b8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107753255"
---
# <a name="azure-key-vault-soft-delete-overview"></a>Panoramica di eliminazione temporanea di Azure Key Vault

> [!IMPORTANT]
> È necessario abilitare immediatamente l'eliminazione in modo soft per gli insiemi di credenziali delle chiavi. La possibilità di rifiutare esplicitamente l'eliminazione soft verrà presto deprecata. Vedere i dettagli completi [qui](soft-delete-change.md)

> [!IMPORTANT]
> L'insieme di credenziali eliminato in modo soft attiva le impostazioni di eliminazione per l'integrazione con i servizi Key Vault, ad esempio assegnazioni di ruoli del controllo degli accessi in base al ruolo di Azure, sottoscrizioni di Griglia di eventi. Dopo il ripristino delle impostazioni di eliminazione Key Vault per i servizi integrati sarà necessario ricreare manualmente. 

la funzionalità di eliminazione soft di Key Vault consente il ripristino degli insiemi di credenziali eliminati e degli oggetti dell'insieme di credenziali delle chiavi eliminati ,ad esempio chiavi, segreti, certificati, noti come eliminazione soft. In particolare, vengono descritti gli scenari seguenti: questa protezione offre le protezioni seguenti:

- Dopo aver eliminato un segreto, una chiave, un certificato o un insieme di credenziali delle chiavi, rimarrà recuperabile per un periodo configurabile di 7-90 giorni di calendario. Se non viene specificata una configurazione, il periodo di recupero predefinito verrà impostato su 90 giorni. In questo modo gli utenti hanno il tempo sufficiente per notare l'eliminazione accidentale di un segreto e intervenire.
- Per eliminare definitivamente un segreto, è necessario eseguire due operazioni. Prima di tutto un utente deve eliminare l'oggetto, che lo inserisce nello stato eliminato in modo soft. In secondo momento, un utente deve ripulire l'oggetto nello stato eliminato in modo soft. L'operazione di eliminazione richiede autorizzazioni aggiuntive per i criteri di accesso. Queste protezioni aggiuntive riducono il rischio che un utente elimini accidentalmente o in modo dannoso un segreto o un insieme di credenziali delle chiavi.  
- Per ripulire un segreto nello stato eliminato in modo soft, a un'entità servizio deve essere concessa un'autorizzazione aggiuntiva per i criteri di accesso "purge". L'autorizzazione per i criteri di accesso di eliminazione non viene concessa per impostazione predefinita ad alcuna entità servizio, inclusi i proprietari dell'insieme di credenziali delle chiavi e della sottoscrizione, e deve essere impostata intenzionalmente. Richiedendo un'autorizzazione dei criteri di accesso con privilegi elevati per ripulire un segreto eliminato temporaneamente, si riduce la probabilità di eliminare accidentalmente un segreto.

## <a name="supporting-interfaces"></a>Supporto di interfacce

La funzionalità di eliminazione soft è disponibile tramite [l'API REST,](/rest/api/keyvault/)l'interfaccia della riga di comando di [Azure,](./key-vault-recovery.md) [Azure PowerShell](./key-vault-recovery.md)e le interfacce [.NET/C#,](/dotnet/api/microsoft.azure.keyvault) nonché i modelli [arm.](/azure/templates/microsoft.keyvault/2019-09-01/vaults)

## <a name="scenarios"></a>Scenari

Gli insiemi di credenziali delle chiavi di Azure sono risorse tracciate, gestite da Azure Resource Manager. Azure Resource Manager specifica anche un comportamento ben definito per l'eliminazione, in base al quale al termine dell'operazione di eliminazione la risorsa non deve essere più accessibile. Con la funzionalità di eliminazione temporanea viene consentito invece il ripristino dell'oggetto eliminato, indipendentemente dal fatto che l'eliminazione sia stata intenzionale o accidentale.

1. In uno scenario tipico, un utente può aver accidentalmente eliminato un insieme di credenziali delle chiavi o un oggetto di un insieme di credenziali delle chiavi. Se l'insieme di credenziali delle chiavi o l'oggetto dell'insieme è stato impostato in modo da essere recuperabile entro un periodo predeterminato, l'utente può annullare l'eliminazione e ripristinare i dati.

2. In uno scenario diverso, un utente non autorizzato può tentare di eliminare un insieme di credenziali delle chiavi o un oggetto di un insieme di credenziali delle chiavi, ad esempio una chiave in un insieme di credenziali, per provocare un'interruzione delle attività. In questo caso, separare l'eliminazione dell'insieme di credenziali delle chiavi o dell'oggetto dell'insieme di credenziali delle chiavi dall'eliminazione effettiva dei dati sottostanti può rappresentare una misura di sicurezza, poiché consente, ad esempio, di limitare le autorizzazioni di eliminazione dei dati solo a un ruolo attendibile. Con questo approccio, infatti, è necessario un quorum per un'operazione che, altrimenti, determinerebbe una perdita immediata di dati.

### <a name="soft-delete-behavior"></a>Comportamento della funzione di eliminazione temporanea

Quando è abilitata l'eliminazione soft, le risorse contrassegnate come risorse eliminate vengono mantenute per un periodo specificato (90 giorni per impostazione predefinita). Il servizio offre anche un meccanismo per il ripristino dell'oggetto eliminato, essenzialmente annullando l'operazione di eliminazione.

Quando si crea un nuovo insieme di credenziali delle chiavi, l'eliminazione soft è impostata su on per impostazione predefinita. È possibile creare un insieme di credenziali delle chiavi senza eliminazione soft tramite l'interfaccia della riga di comando di [Azure](./key-vault-recovery.md) [Azure PowerShell](./key-vault-recovery.md). Dopo aver abilitato l'eliminazione soft in un insieme di credenziali delle chiavi, non è possibile disabilitarla

Il periodo di conservazione predefinito è di 90 giorni, ma, durante la creazione dell'insieme di credenziali delle chiavi, è possibile impostare l'intervallo dei criteri di conservazione su un valore compreso tra 7 e 90 giorni fino al portale di Azure. I criteri di conservazione con protezione da eliminazione usano lo stesso intervallo. Una volta impostato, l'intervallo dei criteri di conservazione non può essere cambiato.

Non è possibile riutilizzare il nome di un insieme di credenziali delle chiavi eliminato automaticamente fino al termine del periodo di conservazione.

### <a name="purge-protection"></a>Protezione dall'eliminazione

La protezione dall'eliminazione è un comportamento Key Vault facoltativo e **non è abilitata per impostazione predefinita.** La protezione dall'eliminazione può essere abilitata solo dopo aver abilitato l'eliminazione soft.  Può essere attivato tramite l'interfaccia della [riga di](./key-vault-recovery.md?tabs=azure-cli) comando [o PowerShell.](./key-vault-recovery.md?tabs=azure-powershell)

Quando la protezione dall'eliminazione è impostata su On, un insieme di credenziali o un oggetto nello stato eliminato non può essere ripulito fino al termine del periodo di conservazione. Gli insiemi di credenziali e gli oggetti eliminati automaticamente possono comunque essere ripristinati, assicurandosi che i criteri di conservazione siano seguiti.

Il periodo di conservazione predefinito è di 90 giorni, ma è possibile impostare l'intervallo dei criteri di conservazione su un valore compreso tra 7 e 90 giorni fino al portale di Azure. Una volta impostato e salvato, l'intervallo dei criteri di conservazione non può essere modificato per tale insieme di credenziali.

### <a name="permitted-purge"></a>Ripulitura consentita

L'eliminazione permanente di un insieme di credenziali delle chiavi non è possibile tramite un'operazione POST sulla risorsa proxy e richiede privilegi speciali. In genere, solo il proprietario della sottoscrizione può eliminare in modo permanente un insieme di credenziali delle chiavi. L'operazione POST attiva l'eliminazione immediata e irreversibile dell'insieme di credenziali delle chiavi. 

Le eccezioni sono:
- Quando la sottoscrizione di Azure è stata contrassegnata *come non recapitabile.* In questo caso solo il servizio può eseguire l'eliminazione effettiva, sotto forma di processo pianificato. 
- Quando è `--enable-purge-protection flag` abilitato nell'insieme di credenziali stesso. In questo caso, Key Vault attenderà 90 giorni da quando l'oggetto segreto originale è stato contrassegnato per l'eliminazione definitiva dell'oggetto.

Per la procedura, vedere Come usare l'eliminazione Key Vault soft con l'interfaccia della riga di [comando:](./key-vault-recovery.md?tabs=azure-cli#key-vault-cli) Ripulitura di un insieme di credenziali delle chiavi o Come usare l'eliminazione Key Vault soft con [PowerShell:](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)Ripulitura di un insieme di credenziali delle chiavi.

### <a name="key-vault-recovery"></a>Recupero di un insieme di credenziali delle chiavi

In seguito all'eliminazione di un insieme di credenziali delle chiavi, il servizio crea una risorsa proxy nella sottoscrizione, a cui aggiungerà metadati sufficienti per il ripristino. La risorsa proxy è un oggetto archiviato disponibile nello stesso percorso dell'insieme di credenziali delle chiavi eliminato. 

### <a name="key-vault-object-recovery"></a>Recupero di un oggetto di un insieme di credenziali delle chiavi

Dopo l'eliminazione di un oggetto dell'insieme di credenziali delle chiavi, ad esempio una chiave, il servizio inserirà l'oggetto in uno stato eliminato, rendendolo inaccessibile a qualsiasi operazione di recupero. In questo stato l'oggetto dell'insieme di credenziali delle chiavi può essere solo elencato, recuperato o eliminato in modo forzato o permanente. Per visualizzare gli oggetti, usare il comando dell'interfaccia della riga di comando di Azure (come descritto in Come usare l'eliminazione soft di Key Vault con l'interfaccia della riga di comando) o il parametro Azure PowerShell (come descritto `az keyvault key list-deleted` [](./key-vault-recovery.md) `-InRemovedState` Key Vault [soft-delete con PowerShell).](./key-vault-recovery.md?tabs=azure-powershell#key-vault-powershell)  

Al tempo stesso, Key Vault pianificherà l'esecuzione dell'eliminazione dei dati sottostanti, corrispondenti all'insieme di credenziali delle chiavi o all'oggetto dell'insieme di credenziali delle chiavi eliminato, dopo un intervallo di memorizzazione predeterminato. Per la durata dell'intervallo di memorizzazione viene conservato anche il record DNS corrispondente all'insieme di credenziali delle chiavi.

### <a name="soft-delete-retention-period"></a>Periodo di memorizzazione dell'eliminazione temporanea

Le risorse eliminate in modo soft vengono mantenute per un periodo di tempo impostato, 90 giorni. Durante il periodo di memorizzazione dell'eliminazione temporanea tenere presente quanto segue:

- È possibile elencare tutti gli insiemi di credenziali delle chiavi e gli oggetti di insiemi di credenziali delle chiavi in stato di eliminazione temporanea nella sottoscrizione, nonché accedere alle informazioni di eliminazione e recupero disponibili su di essi.
  - Gli insiemi di credenziali delle chiavi eliminati possono essere elencati solo da utenti con autorizzazioni speciali. Per la gestione degli insiemi di credenziali delle chiavi eliminati è consigliabile creare un ruolo personalizzato a cui assegnare queste autorizzazioni speciali.
- Non è possibile creare un insieme di credenziali delle chiavi con lo stesso nome nello stesso percorso. di conseguenza, non è possibile creare un oggetto dell'insieme di credenziali delle chiavi in un insieme di credenziali specificato se tale insieme di credenziali delle chiavi contiene un oggetto con lo stesso nome e che si trova in uno stato eliminato.
- Solo un utente con privilegi specifici può recuperare un insieme di credenziali delle chiavi o un oggetto di un insieme di credenziali delle chiavi eseguendo un comando di ripristino sulla risorsa proxy corrispondente.
  - Può recuperare l'insieme di credenziali delle chiavi l'utente che, membro del ruolo personalizzato, dispone dei privilegi necessari per creare un insieme di credenziali delle chiavi nel gruppo di risorse.
- Solo un utente con privilegi specifici può eliminare in modo forzato un insieme di credenziali delle chiavi o un oggetto di un insieme di credenziali delle chiavi eseguendo un comando di eliminazione sulla risorsa proxy corrispondente.

Se l'insieme di credenziali delle chiavi o l'oggetto dell'insieme di credenziali delle chiavi non viene recuperato, alla fine dell'intervallo di memorizzazione il servizio esegue un'operazione di ripulitura sull'insieme di credenziali delle chiavi o sull'oggetto dell'insieme eliminato in modo temporaneo, nonché sui relativi contenuti. È possibile che l'eliminazione della risorsa non venga ripianificata.

### <a name="billing-implications"></a>Implicazioni relative alla fatturazione

In generale, quando un oggetto (un insieme di credenziali delle chiavi o una chiave o una chiave privata) è in stato di eliminazione, esistono solo due operazioni possibili: “Ripulisci” e “Ripristina”. Tutte le altre operazioni avranno esito negativo. Pertanto, anche se l'oggetto esiste, non potrà essere eseguita alcuna operazioni e non sarà possibile utilizzarlo,quindi non vi sarà alcuna fatturazione. Tuttavia, sono presenti le seguenti eccezioni:

- le operazioni “Ripulisci“ e '”Ripristina'” verranno considerate come normali operazioni di insieme di credenziali delle chiavi e verranno fatturate.
- Se l'oggetto è una chiave HSM, l'addebito “Chiave protetta dal modulo di protezione hardware” verrà applicato per ogni versione della chiave per ogni mese se è stata utilizzata una versione della chiave negli ultimi 30 giorni. In seguito, poiché l'oggetto è in stato di eliminazione, non sarà possibile eseguire alcune operazioni contrastanti, pertanto non verrà applicato alcun costo.

## <a name="next-steps"></a>Passaggi successivi

Le due guide seguenti descrivono gli scenari principali di utilizzo dell'eliminazione temporanea.

- [Come usare Key Vault'eliminazione soft con il portale](./key-vault-recovery.md?tabs=azure-portal)
- [Come usare l'eliminazione temporanea di Key Vault con PowerShell](./key-vault-recovery.md) 
- [Come usare l'eliminazione temporanea di Key Vault con l'interfaccia della riga di comando](./key-vault-recovery.md)
