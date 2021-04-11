---
title: 'Azure AD Connect sincronizzazione oggetto singolo '
description: Informazioni su come sincronizzare un oggetto da Active Directory a Azure AD per la risoluzione dei problemi.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/19/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 373cebee4e7f95062791d8bc68bfee7d845e1465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726064"
---
# <a name="azure-ad-connect-single-object-sync"></a>Azure AD Connect sincronizzazione oggetto singolo 

Lo strumento Azure AD Connect sincronizzazione di singoli oggetti è un cmdlet di PowerShell che può essere usato per sincronizzare un singolo oggetto da Active Directory a Azure Active Directory. Il report generato può essere utilizzato per analizzare e risolvere i problemi di sincronizzazione degli oggetti. 

> [!NOTE]
> Lo strumento supporta la sincronizzazione da Active Directory a Azure Active Directory. Non supporta la sincronizzazione da Azure Active Directory a Active Directory. 
>
> Lo strumento supporta la sincronizzazione di aggiunta e aggiornamento di una modifica dell'oggetto. Non supporta la sincronizzazione dell'eliminazione di una modifica dell'oggetto. 

## <a name="how-it-works"></a>Funzionamento
Lo strumento di sincronizzazione dell'oggetto singolo richiede un nome distinto Active Directory come input per trovare il connettore di origine e la partizione per l'importazione. Esporta le modifiche in Azure Active Directory. Lo strumento genera un output JSON simile al tipo di risorsa **provisioningObjectSummary** . 

Lo strumento di sincronizzazione di oggetti singoli esegue i passaggi seguenti: 

 1. Determinare se il dominio (di origine) dell'oggetto (Active Directory connettore e la partizione) nell'ambito di sincronizzazione. 
 2. Determinare se il dominio (destinazione) dell'oggetto (Azure Active Directory connettore e la partizione) nell'ambito di sincronizzazione. 
 3. Determinare se l'unità organizzativa dell'oggetto è nell'ambito di sincronizzazione. 
 4. Determinare se l'oggetto è accessibile utilizzando le credenziali dell'account del connettore. 
 5. Determinare se il tipo dell'oggetto è nell'ambito di sincronizzazione. 
 6. Determinare se l'oggetto è nell'ambito di sincronizzazione se è abilitato il filtro del gruppo. 
 7. Importa oggetto da Active Directory nello spazio Active Directory connettore. 
 8. Importa oggetto da Azure Active Directory nello spazio Azure Active Directory connettore. 
 9. Oggetto di sincronizzazione dallo spazio Active Directory connettore. 
 10. Esporta l'oggetto dallo spazio Azure Active Directory connettore Azure Active Directory. 

Oltre all'output JSON, lo strumento genera un report HTML con tutti i dettagli dell'operazione di sincronizzazione. Il report HTML si trova in **C:\ProgramData\AADConnect\ADSyncObjectDiagnostics\ ADSyncSingleObjectSyncResult- <date> . htm**. Il report HTML può essere condiviso con il team di supporto per eseguire ulteriori operazioni di risoluzione dei problemi, se necessario. 

Il report HTML presenta gli elementi seguenti: 

|Scheda|Descrizione|
|-----|-----|
|Passaggi|Vengono delineati i passaggi necessari per sincronizzare un oggetto. Ogni passaggio contiene i dettagli per la risoluzione dei problemi. I passaggi di importazione, sincronizzazione ed esportazione contengono informazioni aggiuntive sugli attributi, ad esempio nome, multivalore, tipo, valore, valore aggiunto, eliminazione valore, operazione, regola di sincronizzazione, tipo di mapping e origine dati.| 
|Risoluzione dei problemi & raccomandazione|Fornisce il codice e il motivo dell'errore. Le informazioni sull'errore sono disponibili solo se si verifica un errore.| 
|e modificate|Mostra il valore precedente e il nuovo valore. Se non è presente alcun valore precedente o se il nuovo valore viene eliminato, la cella è vuota. Per gli attributi multivalore viene visualizzato il conteggio. Il nome dell'attributo è un collegamento alla scheda passaggi: Esporta oggetto da Azure Active Directory connettore spazio a Azure Active Directory: informazioni sugli attributi che contengono dettagli aggiuntivi dell'attributo, ad esempio nome, è multivalore, tipo, valore, valore aggiunto, valore Delete, operazione, regola di sincronizzazione, tipo di mapping e origine dati.| 
|Riepilogo|Viene fornita una panoramica di ciò che è successo e degli identificatori per l'oggetto nei sistemi di origine e di destinazione.| 

## <a name="prerequisites"></a>Prerequisiti 

Per usare lo strumento di sincronizzazione di oggetti singoli, è necessario usare la versione di marzo 2021 di Azure AD Connect o versione successiva. 

### <a name="run-the-single-object-sync-tool"></a>Eseguire lo strumento di sincronizzazione dell'oggetto singolo 

Per eseguire lo strumento di sincronizzazione dell'oggetto singolo, seguire questa procedura: 

 1. Aprire una nuova sessione di Windows PowerShell nel server di Azure AD Connect con l'opzione Esegui come amministratore. 

 2. Impostare i [criteri di esecuzione](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy) su RemoteSigned o Unrestricted. 

 3. Disabilitare l'utilità di pianificazione della sincronizzazione dopo aver verificato che non siano in esecuzione operazioni di sincronizzazione. 

     `Set-ADSyncScheduler -SyncCycleEnabled $false` 

 4. Importare il modulo di diagnostica AdSync 

     `Import-module -Name "C:\Program Files\Microsoft Azure AD Sync\Bin\ADSyncDiagnostics\ADSyncDiagnostics.psm1"` 

 5. Richiamare il cmdlet di sincronizzazione dell'oggetto singolo. 

     `Invoke-ADSyncSingleObjectSync -DistinguishedName "CN=testobject,OU=corp,DC=contoso,DC=com" | Out-File -FilePath ".\output.json"` 

 6. Abilitare di nuovo l'utilità di pianificazione della sincronizzazione. 

     `Set-ADSyncScheduler -SyncCycleEnabled $true`

|Parametri di input per la sincronizzazione di un singolo oggetto|Descrizione| 
|-----|----|
|DistinguishedName|Si tratta di un parametro di stringa obbligatorio. </br></br>Si tratta del nome distinto dell'oggetto Active Directory che richiede la sincronizzazione e la risoluzione dei problemi.| 
|StagingMode|Si tratta di un parametro facoltativo opzionale.</br></br>Questo parametro può essere utilizzato per impedire l'esportazione delle modifiche in Azure Active Directory.</br></br>**Nota**: il cmdlet eseguirà il commit dell'operazione di sincronizzazione. </br></br>**Nota**: Azure ad Connect Server di gestione temporanea non esporterà le modifiche apportate al Azure Active Directory.|
|NoHtmlReport|Si tratta di un parametro facoltativo opzionale.</br></br>Questo parametro può essere utilizzato per evitare la generazione del report HTML. 

## <a name="single-object-sync-throttling"></a>Limitazione della sincronizzazione di un singolo oggetto 

Lo strumento di sincronizzazione dell'oggetto singolo **è** progettato per l'analisi e la risoluzione dei problemi relativi alla sincronizzazione degli oggetti. **Non** è progettato per sostituire il ciclo di sincronizzazione eseguito dall'utilità di pianificazione. L'importazione da Azure Active Directory ed Esporta in Azure Active Directory sono soggette a limiti di limitazione delle richieste. Se si raggiunge il limite di limitazione, riprovare dopo 5 minuti. 

## <a name="next-steps"></a>Passaggi successivi
- [Risoluzione dei problemi di sincronizzazione degli oggetti](tshoot-connect-objectsync.md)
- [Risoluzione dei problemi dell'oggetto non in sincronizzazione](tshoot-connect-object-not-syncing.md)
- [Risoluzione dei problemi end-to-end degli oggetti e degli attributi di Azure AD Connect](https://docs.microsoft.com/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes)
