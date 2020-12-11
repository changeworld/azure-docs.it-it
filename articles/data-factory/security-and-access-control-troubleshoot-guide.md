---
title: Risolvere i problemi di sicurezza e controllo di accesso
description: Informazioni su come risolvere i problemi di sicurezza e controllo di accesso in Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 99c03ae4430d1a4caf575bdb9900200af0217bf1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109768"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Risolvere i problemi di Azure Data Factory sicurezza e controllo di accesso

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi di sicurezza e controllo di accesso in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Errori comuni e messaggi


### <a name="connectivity-issue-of-copy-activity-in-cloud-data-store"></a>Problema di connettività dell'attività di copia nell'archivio dati cloud

#### <a name="symptoms"></a>Sintomi

È possibile che vengano restituiti vari tipi di messaggi di errore quando si verifica un problema di connettività per l'archivio dati di origine/sink.

#### <a name="cause"></a>Causa 

Il problema è dovuto principalmente ai fattori seguenti:

1. Impostazione del proxy nel nodo del runtime di integrazione self-hosted (se si usa il runtime di integrazione self-hosted)

1. Impostazione del firewall nel nodo del runtime di integrazione self-hosted (se si usa il runtime di integrazione self-hosted)

1. Impostazione del firewall nell'archivio dati cloud

#### <a name="resolution"></a>Soluzione

1. Verificare prima di tutto i punti seguenti per assicurarsi che il problema sia causato da un problema di connettività:

   - L'errore viene generato dai connettori di origine/sink.

   - L'attività ha esito negativo all'inizio della copia

   - Si tratta di un errore coerente per Azure IR o un runtime di integrazione self-hosted con un nodo, perché potrebbe essere un errore casuale per il runtime di integrazione self-hosted a più nodi se si verifica solo una parte dei nodi.

1. Verificare il proxy, il firewall e le impostazioni di rete se si usa il runtime di integrazione **self-hosted** perché l'esecuzione nello stesso archivio dati potrebbe avere esito positivo in Azure IR. Per la risoluzione dei problemi, vedere i collegamenti seguenti:

   [Porte IR e firewall indipendenti](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls) 
    [Connettore ADLS](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
1. Se si usa **Azure IR**, provare a disabilitare l'impostazione del firewall dell'archivio dati. In questo modo, è possibile risolvere il problema relativo alle due circostanze seguenti:
  
   * [Azure IR gli indirizzi IP](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) non sono inclusi nell'elenco Consenti.

   * *Consenti ai servizi Microsoft attendibili di accedere a questa* funzionalità dell'account di archiviazione è disattivato per [archiviazione BLOB di Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities) e [ADLS generazione 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities).

   * *Consenti l'accesso ai servizi di Azure* non è abilitato per ADLS Gen1.

1. Se i metodi precedenti non funzionano, contattare Microsoft per assistenza.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Problema di chiave di autenticazione non valido o vuoto dopo la disabilitazione dell'accesso alla rete pubblica

#### <a name="symptoms"></a>Sintomi

Dopo avere disabilitato l'accesso alla rete pubblica per Data Factory, perché il runtime di integrazione self-hosted genera l'errore seguente: "la chiave di autenticazione non è valida o è vuota".

#### <a name="cause"></a>Causa

Il problema è probabilmente causato da un problema di risoluzione DNS, perché la disabilitazione della connettività pubblica e la definizione di un endpoint privato non sono utili per la riconnessione.

È possibile seguire questa procedura per verificare se Data Factory FQDN viene risolto in un indirizzo IP pubblico:

1. Verificare di aver creato la macchina virtuale di Azure nella stessa VNET Data Factory endpoint privato.

2. Eseguire PsPing e ping dalla macchina virtuale di Azure al nome di dominio completo Data Factory:

   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`

   > [!Note]
   > È necessario specificare una porta per il comando PsPing, mentre la porta 443 non è un must.

3. Controllare se entrambi i comandi sono stati risolti in un indirizzo IP pubblico di ADF basato sull'area specificata (formato xxx. xxx. xxx. 0).

#### <a name="resolution"></a>Soluzione

- È possibile fare riferimento all'articolo nel [collegamento privato di Azure per Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints). L'istruzione è destinata alla configurazione del server o della zona DNS privata per risolvere Data Factory FQDN in un indirizzo IP privato.

- Se non si è disposti a configurare attualmente la zona o il server DNS privato, attenersi alla procedura seguente come soluzione temporanea. Tuttavia, il DNS personalizzato è ancora consigliato come soluzione a lungo termine.

  1. Modificare il file host in Windows ed eseguire il mapping dell'indirizzo IP privato (endpoint privato ADF) al nome di dominio completo di ADF.
  
     Passare al percorso "C:\Windows\System32\drivers\etc" nella macchina virtuale di Azure e aprire il file **host** con il blocco note. Aggiungere la riga di mapping dell'indirizzo IP privato al nome di dominio completo alla fine del file e salvare la modifica.
     
     ![Aggiungi mapping a host](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Eseguire di nuovo gli stessi comandi descritti in precedenza passaggi di verifica per controllare la risposta, che deve contenere l'indirizzo IP privato.

  1. Ripetere la registrazione del runtime di integrazione self-hosted e risolvere il problema.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Non è possibile registrare la chiave di autenticazione IR nelle macchine virtuali indipendenti a causa del collegamento privato

#### <a name="symptoms"></a>Sintomi

Non è possibile registrare la chiave di autenticazione IR nella macchina virtuale indipendente a causa del collegamento privato abilitato.

Le informazioni sull'errore sono indicate di seguito:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Causa

Il problema potrebbe essere causato dalla macchina virtuale in cui si sta tentando di installare il runtime di integrazione self-hosted. Per connettersi al cloud, è necessario assicurarsi che sia abilitato l'accesso alla rete pubblica.

#### <a name="resolution"></a>Soluzione

 **Soluzione 1:** Per risolvere il problema, è possibile attenersi alla procedura seguente:

1. Passare alla pagina [Factory-Update](https://docs.microsoft.com/rest/api/datafactory/Factories/Update) .

1. In alto a destra, selezionare il pulsante **prova** .

1. In **parametri** completare le informazioni necessarie. 

1. In **corpo** incollare la proprietà seguente:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Selezionare **Esegui** per eseguire la funzione. 

1. Verificare che venga visualizzato il **codice di risposta: 200** . La proprietà incollata dovrebbe essere visualizzata anche nella definizione JSON.

1. Aggiungere di nuovo la chiave di autenticazione IR in Integration Runtime.


**Soluzione 2:** È possibile fare riferimento all'articolo seguente per la soluzione:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Provare ad abilitare l'accesso alla rete pubblica nell'interfaccia utente, come illustrato nello screenshot seguente:

![Abilitare l'accesso alla rete pubblica](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Collegamento privato per Data Factory](data-factory-private-link.md)
*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&una pagina](/answers/topics/azure-data-factory.html)
*  [Forum di stack overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
