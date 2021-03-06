---
title: Azure AD Connect endpoint V2 | Microsoft Docs
description: Questo documento illustra gli aggiornamenti dell'API degli endpoint Sync V2 di Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4434b59044aed8c9814431864e5c3c9b7d98254c
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575718"
---
# <a name="azure-ad-connect-sync-v2-endpoint-api"></a>API dell'endpoint Servizio di sincronizzazione Azure AD Connect v2 
Microsoft ha distribuito un nuovo endpoint (API) per Azure AD Connect che consente di migliorare le prestazioni delle operazioni del servizio di sincronizzazione per Azure Active Directory. Usando il nuovo endpoint V2, si verificherà un notevole miglioramento delle prestazioni di esportazione e importazione in Azure AD. Questo nuovo endpoint supporta gli elementi seguenti:
    
 - sincronizzazione di gruppi con un massimo di 250.000 membri
 - miglioramento delle prestazioni di esportazione e importazione in Azure AD
 
> [!NOTE]
> Attualmente, il nuovo endpoint non ha un limite di dimensioni del gruppo configurato per Microsoft 365 gruppi di cui viene scritto il codice. Questo può influire sulle latenze del ciclo di Active Directory e della sincronizzazione. È consigliabile aumentare le dimensioni del gruppo in modo incrementale.  

>[!NOTE]
> L Azure AD Connect'API dell'endpoint V2 di sincronizzazione è attualmente disponibile solo in questi ambienti Azure:
> - Azure Commercial
> - Azure Cina cloud
> - Cloud di Azure US Government Non verrà reso disponibile nel cloud tedesco di Azure

## <a name="prerequisites"></a>Prerequisiti  
Per usare il nuovo endpoint V2, è necessario usare [Azure AD Connect versione 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) o versioni successive e seguire i passaggi di distribuzione indicati di seguito per abilitare l'endpoint V2 per il server Azure AD Connect.   

## <a name="deployment-guidance"></a>Linee guida per la distribuzione 
Per usare l'endpoint V2, sarà necessario distribuire [Azure AD Connect versione 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) o successiva. Usare il collegamento fornito per il download. 

Si consiglia di seguire il metodo della [migrazione swing](./how-to-upgrade-previous-version.md#swing-migration) per implementare il nuovo endpoint nell'ambiente in uso. In questo modo verrà fornito un piano di emergenza chiaro qualora sia necessario un ripristino dello stato precedente principale. L'esempio seguente illustra come usare una migrazione swing in questo scenario. Per altre informazioni sul metodo di distribuzione migrazione swing, fare riferimento al collegamento fornito. 

### <a name="swing-migration-for-deploying-v2-endpoint"></a>Migrazione swing per la distribuzione dell'endpoint V2
I passaggi seguenti consentiranno di distribuire l'endpoint V2 usando il metodo swing.

1. Distribuire l'endpoint V2 sul server di staging corrente. Questo server sarà noto come **server V2** nei passaggi seguenti. Il server attivo corrente continuerà a elaborare il carico di lavoro di produzione usando l'endpoint V1, che verrà chiamato **server V1**.
1. Verificare che il **server V2** stia ancora elaborando le importazioni come previsto. In questa fase, non verrà eseguito il provisioning di gruppi di grandi dimensioni in Azure AD o in AD locale, ma sarà possibile verificare che l'aggiornamento non abbia causato un altro effetto imprevisto per il processo di sincronizzazione esistente. 
2. Al termine della convalida, impostare il **server v2** come server attivo e il **server V1** come server di staging. In questo momento, verrà effettuato il provisioning in Azure AD di gruppi di grandi dimensioni che sono nell'ambito di sincronizzazione, mentre per i gruppi unificati di Microsoft 365 di grandi dimensioni verrà eseguito il provisioning in AD, se il writeback dei gruppi è abilitato.
3. Verificare che il **server V2** stia eseguendo ed elaborando correttamente gruppi di grandi dimensioni. È possibile scegliere di rimanere in questo passaggio e monitorare il processo di sincronizzazione per un periodo di tempo.
  >[!NOTE]
  > Se è necessario eseguire la transizione alla configurazione precedente, è possibile eseguire nuovamente una migrazione swing dal **server V2** al **server V1**. Poiché l'endpoint V1 non supporta gruppi con oltre 50.000 membri, i gruppi di grandi dimensioni di cui è stato eseguito il provisioning da Azure AD Connect, in Azure AD o in AD locale, verranno eliminati successivamente. 
4. Quando si è sicuri di usare l'endpoint V2, aggiornare il **server V1** per iniziare a usare l'endpoint V2. 
 

## <a name="expectations-of-performance-impact"></a>Aspettative relative all'impatto sulle prestazioni  
Quando si usa l'endpoint V2, i miglioramenti delle prestazioni sono una funzione del numero di gruppi sincronizzati, delle dimensioni di tali gruppi e della varianza del gruppo (l'attività risultante dall'aggiunta e rimozione di utenti come membri del gruppo). Se si usa il nuovo endpoint, senza aumentare il numero, la dimensione o la varianza dei gruppi sincronizzati, l'esportazione e l'importazione in Azure AD dovrebbero richiedere tempi più brevi. 
 
Tuttavia, il miglioramento delle prestazioni può essere negato dall'elaborazione aggiuntiva necessaria per la sincronizzazione di gruppi di grandi dimensioni. È possibile che si verifichi un aumento del tempo di sincronizzazione complessivo a causa dell'aggiunta di un numero eccessivo di gruppi di grandi dimensioni al processo di sincronizzazione.  

Per comprendere meglio il modo in cui l'aggiunta dei nuovi gruppi influirà sulle prestazioni di sincronizzazione, è consigliabile iniziare sincronizzando solo alcuni gruppi di grandi dimensioni con meno di 100.000 membri. È quindi possibile aumentare il numero e le dimensioni dei gruppi inserendone altri nell'ambito, tramite un'unità organizzativa, un attributo o un filtro delle dimensioni massime del gruppo. I miglioramenti delle prestazioni verranno realizzati nelle attività di esportazione e importazione per il connettore di Azure AD, non per il connettore di AD locale. 

## <a name="deployment-step-by-step"></a>Procedura dettagliata per la distribuzione 
Le tre fasi seguenti sono un esempio dettagliato della distribuzione del nuovo endpoint V2.  Usare le fasi come linee guida per la distribuzione.

### <a name="phase-1--install-and-validate-azure-ad-connect"></a>Fase 1: installare e convalidare Azure AD Connect 
È consigliabile eseguire prima i passaggi per installare o eseguire l'aggiornamento ad [Azure AD Connect versione 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) o versioni successive e convalidare il processo di sincronizzazione prima di passare alla seconda fase in cui verrà abilitato l'endpoint V2. Sul server di Azure AD Connect: 


1. [Facoltativo] Eseguire il backup del database 
2. Installare o eseguire l'aggiornamento ad [Azure AD Connect versione 1.5.30.0](https://www.microsoft.com/download/details.aspx?id=47594) o versioni successive.
3. Convalidare l'installazione 

### <a name="phase-2--enable-the-v2-endpoint"></a>Fase 2: abilitare l'endpoint V2 
Il passaggio successivo consiste nell'abilitare l'endpoint V2. 

> [!NOTE]
> Dopo aver abilitato l'endpoint V2 per il server, sarà possibile osservare alcuni miglioramenti delle prestazioni per il carico di lavoro esistente. Tuttavia, non sarà ancora possibile sincronizzare i gruppi con più di 50.000 membri. 

Per passare all'endpoint V2, attenersi ai passaggi seguenti: 

1. Aprire un prompt di PowerShell come amministratore. 
2. Disabilitare l'utilità di pianificazione della sincronizzazione dopo aver verificato che non siano in esecuzione operazioni di sincronizzazione: 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`
 
3. Importare il nuovo modulo: 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 
 
4.  Passare all'endpoint v2:

 `Set-ADSyncAADConnectorExportApiVersion 2` 
 
 `Set-ADSyncAADConnectorImportApiVersion 2` 

 ![PowerShell](media/how-to-connect-sync-endpoint-api-v2/endpoint1.png)
 
A questo punto è stato abilitato l'endpoint V2 per il server. Verificare che non vi siano risultati imprevisti dopo aver abilitato l'endpoint V2 prima di passare alla fase successiva in cui si aumenterà il limite delle dimensioni del gruppo. 
>[!NOTE]
>I percorsi del file/modulo possono usare una lettera di unità diversa, a seconda del percorso di installazione fornito durante l'installazione di Azure AD Connect. 


### <a name="phase-3--increase-the-group-membership-limit"></a>Fase 3: aumentare il limite di appartenenza al gruppo 
Dopo aver verificato che il servizio sia in esecuzione senza risultati imprevisti, è possibile procedere per aumentare il limite di appartenenza al gruppo. Si consiglia di aumentare prima di tutto il limite di appartenenza a un valore leggermente superiore, ad es. 75.000 membri, per visualizzare i gruppi più grandi che vengono sincronizzati in Azure AD. Quando si è soddisfatti dei risultati, è possibile aumentare ulteriormente il limite dei membri.  

Il limite massimo è 250.000 membri per gruppo. 

Per aumentare il limite di appartenenza, è possibile usare i passaggi seguenti:  

1. Aprire l'editor delle regole di sincronizzazione di Azure AD 
2. Nell'editor scegliere **In uscita** per la direzione 
3. Fare clic sulla regola di sincronizzazione **Uscita in AAD – Group Join** 
4. Fare clic **sul pulsante** Modifica Screenshot che mostra "Visualizzare e gestire le regole di sincronizzazione" con l'opzione ![ "Out to AAD - Group Join" selezionata.](media/how-to-connect-sync-endpoint-api-v2/endpoint2.png)

6. Fare clic sul pulsante **Sì** per disabilitare la regola predefinita e creare una copia modificabile.
 ![Screenshot che mostra la finestra "Modifica conferma regola riservata" con il pulsante "Sì" selezionato.](media/how-to-connect-sync-endpoint-api-v2/endpoint3.png)

7. Nella finestra popup della  pagina Descrizione impostare la precedenza su un valore disponibile compreso tra 1 e 99 Screenshot che mostra la finestra "Modifica regola di sincronizzazione in uscita" con la voce ![ "Precedenza" evidenziata.](media/how-to-connect-sync-endpoint-api-v2/endpoint4.png)

8. Nella pagina **Trasformazioni** aggiornare il valore **Origine** per la trasformazione dei **membri**, sostituendo "50000&quot; con un valore compreso tra 50001 e 250000. Questa sostituzione aumenterà le dimensioni massime di appartenenza dei gruppi che verranno sincronizzati in Azure AD. Si consiglia di iniziare con un numero di 100.000 per comprendere l'effetto che la sincronizzazione di gruppi di grandi dimensioni avrà sulle prestazioni di sincronizzazione. 
 
 **Esempio** 
 
 `IIF((ValueCount(&quot;member")> 75000),Error("Maximum Group member count exceeded"),IgnoreThisFlow)` 
 
 ![Modifica regola di sincronizzazione](media/how-to-connect-sync-endpoint-api-v2/endpoint5.png)

9. Fare clic su Salva. 
10. Aprire un prompt di PowerShell come amministratore 
11. Riabilitare l'utilità di pianificazione della sincronizzazione 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true` 
 
>[!NOTE]
> Se Azure AD Connect Health non è abilitato, modificare le impostazioni del registro eventi dell'applicazione di Windows per archiviare i log, anziché sovrascriverli. I log possono essere usati per semplificare le attività future di risoluzione dei problemi. 

>[!NOTE]
> Dopo aver abilitato il nuovo endpoint, è possibile che vengano visualizzati altri errori di esportazione nel connettore AAD con il nome "dn-attributes-failure". Sarà presente una voce del registro eventi corrispondente per ogni errore con ID 6949. Gli errori sono informativi e non indicano un problema con l'installazione, ma piuttosto che il processo di sincronizzazione non è stato in grado di aggiungere determinati membri a un gruppo in Azure AD perché l'oggetto membro stesso non è stato sincronizzato in Azure AD. 

Il nuovo codice dell'endpoint V2 gestisce alcuni tipi di errori di esportazione leggermente diversi da quelli del codice V1.  Quando si usa l'endpoint V2, è possibile che vengano visualizzati più messaggi di errore informativi. 

>[!NOTE]
> Quando si esegue l'aggiornamento di Azure AD Connect, assicurarsi che i passaggi della fase 2 vengano eseguiti nuovamente, in quanto le modifiche non vengono mantenute durante il processo di aggiornamento. 

Durante gli aumenti successivi al limite dei membri del gruppo nella regola di sincronizzazione **Uscita in AAD – Group Join**, non è necessaria una sincronizzazione completa, quindi è possibile scegliere di eliminare la sincronizzazione completa eseguendo il comando seguente in PowerShell. 

 `Set-ADSyncSchedulerConnectorOverride -FullSyncRequired $false -ConnectorName "<AAD Connector Name>" `
 
>[!NOTE]
> Se sono Microsoft 365 gruppi unificati che hanno più di 50.000 membri, i gruppi verranno letti in Azure AD Connect e, se il writeback dei gruppi è abilitato, verranno scritti nell'istanza locale di AD. 

## <a name="rollback"></a>Rollback 
Se l'endpoint V2 è stato abilitato ed è necessario eseguire il ripristino dello stato precedente, attenersi ai passaggi seguenti: 

1. Sul server di Azure AD Connect: a. [Facoltativo] Eseguire il backup del database 
2. Aprire un prompt di PowerShell come amministratore:
3. Disabilitare l'utilità di pianificazione della sincronizzazione dopo aver verificato che non siano in esecuzione operazioni di sincronizzazione
 
 `Set-ADSyncScheduler -SyncCycleEnabled $false`

4. Passare all'endpoint V1 * 
 
 `Import-Module 'C:\Program Files\Microsoft Azure AD Sync\Extensions\AADConnector.psm1'` 

 `Set-ADSyncAADConnectorExportApiVersion 1`

 `Set-ADSyncAADConnectorImportApiVersion 1`
 
5. Aprire l'editor delle regole di sincronizzazione di Azure AD 
6. Eliminare la copia modificabile della regola di sincronizzazione **Uscita in AAD – Group Join** 
7. Abilitare la copia predefinita della regola di sincronizzazione **Uscita in AAD – Group Join** 
8. Aprire un prompt di PowerShell come amministratore 
9. Riabilitare l'utilità di pianificazione della sincronizzazione 
 
 `Set-ADSyncScheduler -SyncCycleEnabled $true`
 
>[!NOTE]
> Quando si torna dagli endpoint V2 a V1, i gruppi sincronizzati con più di 50.000 membri verranno eliminati dopo l'esecuzione di una sincronizzazione completa, sia per i gruppi di Active Directory di cui è stato effettuato il provisioning Azure AD che per i gruppi unificati di Microsoft 365 di cui è stato effettuato il provisioning in AD. 

## <a name="frequently-asked-questions"></a>Domande frequenti  
 
**Quando il nuovo punto finale diventerà l'impostazione predefinita per gli aggiornamenti e le nuove installazioni?**  
</br>È in programma una nuova versione di AADConnect che verrà pubblicata per il download nel mese di febbraio 2021. Questa versione userà l'end point V2 per impostazione predefinita e abiliterà la sincronizzazione di gruppi di dimensioni superiori a 50.000 senza alcuna configurazione aggiuntiva. Questa versione verrà successivamente pubblicata per l'aggiornamento automatico ai server idonei.
 
## <a name="next-steps"></a>Passaggi successivi

* [Servizio di sincronizzazione Azure AD Connect: comprendere e personalizzare la sincronizzazione](how-to-connect-sync-whatis.md)
* [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md)
