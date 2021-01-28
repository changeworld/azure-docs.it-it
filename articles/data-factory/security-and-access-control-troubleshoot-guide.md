---
title: Risolvere i problemi di sicurezza e controllo di accesso
description: Informazioni su come risolvere i problemi di sicurezza e controllo di accesso in Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 01/05/2021
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 70e4d0c1a9f26f83924c1343e4e8e51c5595b324
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944515"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Risolvere i problemi di Azure Data Factory sicurezza e controllo di accesso

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi di sicurezza e controllo di accesso in Azure Data Factory.

## <a name="common-errors-and-messages"></a>Errori comuni e messaggi

### <a name="connectivity-issue-in-the-copy-activity-of-the-cloud-datastore"></a>Problema di connettività nell'attività di copia dell'archivio dati cloud

#### <a name="symptoms"></a>Sintomi

È possibile che vengano restituiti diversi messaggi di errore quando si verificano problemi di connettività nell'archivio dati di origine o sink.

#### <a name="cause"></a>Causa 

Il problema è in genere causato da uno dei fattori seguenti:

* Impostazione del proxy nel nodo del runtime di integrazione self-hosted (IR) se si usa un runtime di integrazione self-hosted.

* Impostazione del firewall nel nodo IR indipendente, se si usa un runtime di integrazione self-hosted.

* Impostazione del firewall nell'archivio dati cloud.

#### <a name="resolution"></a>Soluzione

* Per assicurarsi che si tratta di un problema di connettività, verificare i punti seguenti:

   - L'errore viene generato dai connettori di origine o sink.
   - L'errore si trova all'inizio dell'attività di copia.
   - L'errore è coerente per Azure IR o il runtime di integrazione self-hosted con un nodo, perché potrebbe trattarsi di un errore casuale in un runtime di integrazione self-hosted a più nodi se si verificano problemi solo per alcuni nodi.

* Se si usa un runtime di integrazione **self-hosted**, controllare il proxy, il firewall e le impostazioni di rete, perché la connessione allo stesso archivio dati potrebbe avere esito positivo se si usa un Azure IR. Per risolvere questo scenario, vedere:

   * [Porte IR e firewall indipendenti](./create-self-hosted-integration-runtime.md#ports-and-firewalls)
   * [Connettore Azure Data Lake Storage](./connector-azure-data-lake-store.md)
  
* Se si usa una **Azure IR**, provare a disabilitare l'impostazione del firewall dell'archivio dati. Questo approccio consente di risolvere i problemi nelle due situazioni seguenti:
  
   * [Azure IR gli indirizzi IP](./azure-integration-runtime-ip-addresses.md) non sono presenti nell'elenco Consenti.
   * La funzionalità *Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione* è disattivata per [archiviazione BLOB di Azure](./connector-azure-blob-storage.md#supported-capabilities) e [Azure Data Lake storage generazione 2](./connector-azure-data-lake-storage.md#supported-capabilities).
   * L'impostazione *Consenti l'accesso a servizi di Azure* non è abilitata per Azure Data Lake storage Gen1.

Se nessuno dei metodi precedenti funziona, contattare Microsoft per assistenza.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Problema di chiave di autenticazione non valido o vuoto dopo la disabilitazione dell'accesso alla rete pubblica

#### <a name="symptoms"></a>Sintomi

Dopo avere disabilitato l'accesso alla rete pubblica per Data Factory, il runtime di integrazione self-hosted genera l'errore seguente: "la chiave di autenticazione non è valida o è vuota".

#### <a name="cause"></a>Causa

Il problema è probabilmente causato da un problema di risoluzione del Domain Name System (DNS), perché la disabilitazione della connettività pubblica e la definizione di un endpoint privato impedisce la riconnessione.

Per verificare se l'Data Factory nome di dominio completo (FQDN) viene risolto nell'indirizzo IP pubblico, eseguire le operazioni seguenti:

1. Verificare di aver creato la macchina virtuale (VM) di Azure nella stessa rete virtuale dell'endpoint Data Factory privato.

2. Eseguire PsPing e ping dalla macchina virtuale di Azure al nome di dominio completo Data Factory:

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`
   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   > [!Note]
   > È necessario specificare una porta per il comando PsPing. La porta 443 è visualizzata ma non è obbligatoria.

3. Verificare se entrambi i comandi si risolvono in un indirizzo IP pubblico Azure Data Factory basato su un'area specificata. L'indirizzo IP deve essere nel formato seguente: `xxx.xxx.xxx.0`

#### <a name="resolution"></a>Soluzione

Per risolvere il problema, eseguire le operazioni seguenti.
- Per Azure Data Factory articolo, fare riferimento al [collegamento privato di Azure](./data-factory-private-link.md#dns-changes-for-private-endpoints) . L'istruzione è destinata alla configurazione del server o della zona DNS privata per la risoluzione del nome di dominio completo Data Factory in un indirizzo IP privato.

- È consigliabile usare un DNS personalizzato come soluzione a lungo termine. Tuttavia, se non si vuole configurare la zona o il server DNS privato, provare la soluzione temporanea seguente:

  1. Modificare il file host in Windows ed eseguire il mapping dell'indirizzo IP privato (il Azure Data Factory endpoint privato) al nome di dominio completo Azure Data Factory.
  
     Nella macchina virtuale di Azure passare a `C:\Windows\System32\drivers\etc` , quindi aprire il file *host* nel blocco note. Aggiungere la riga che esegue il mapping dell'indirizzo IP privato al nome di dominio completo alla fine del file e salvare la modifica.
     
     ![Screenshot del mapping dell'indirizzo IP privato all'host.](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Eseguire di nuovo gli stessi comandi dei passaggi di verifica precedenti per controllare la risposta, che deve contenere l'indirizzo IP privato.

  1. Ripetere la registrazione del runtime di integrazione self-hosted e risolvere il problema.

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Non è possibile registrare la chiave di autenticazione IR nelle macchine virtuali indipendenti a causa del collegamento privato

#### <a name="symptoms"></a>Sintomi

Non è possibile registrare la chiave di autenticazione IR nella VM self-hosted perché il collegamento privato è abilitato. Viene visualizzato il messaggio di errore seguente:

"Impossibile ottenere il token del servizio dal servizio ADF con chiave * * * * * * * * * * * * * * * * * * * e costo orario: 0,1250079 secondi, il codice di errore è: InvalidGatewayKey, activityId è: XXXXXXX e il messaggio di errore dettagliato indica che l'indirizzo IP del client non è un indirizzo IP privato valido perché Data Factory non è riuscito ad accedere alla rete pubblica, quindi non è in grado di raggiungere il cloud per eseguire la connessione".

#### <a name="cause"></a>Causa

Il problema potrebbe essere causato dalla macchina virtuale in cui si sta provando a installare il runtime di integrazione self-hosted. Per connettersi al cloud, assicurarsi che sia abilitato l'accesso alla rete pubblica.

#### <a name="resolution"></a>Soluzione

**Soluzione 1**
 
Per risolvere il problema, eseguire le operazioni seguenti.

1. Passare alla pagina [Factory-Update](/rest/api/datafactory/Factories/Update) .

1. In alto a destra, selezionare il pulsante **prova** .
1. In **parametri** completare le informazioni necessarie. 
1. In **corpo** incollare la proprietà seguente:

    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ```
1. Selezionare **Esegui** per eseguire la funzione. 

1. In **parametri** completare le informazioni necessarie. 

1. In **corpo** incollare la proprietà seguente:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Selezionare **Esegui** per eseguire la funzione. 
1. Verificare che venga visualizzato il **codice di risposta: 200** . La proprietà incollata dovrebbe essere visualizzata anche nella definizione JSON.

1. Aggiungere di nuovo la chiave di autenticazione IR in Integration Runtime.


**Soluzione 2**

Per risolvere il problema, passare al [collegamento privato di Azure per Azure Data Factory](./data-factory-private-link.md).

Provare ad abilitare l'accesso alla rete pubblica nell'interfaccia utente, come illustrato nello screenshot seguente:

![Screenshot del controllo "abilitato" per "Consenti accesso alla rete pubblica" nel riquadro rete.](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Collegamento privato per Data Factory](data-factory-private-link.md)
*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&una pagina](/answers/topics/azure-data-factory.html)
*  [Forum di stack overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)