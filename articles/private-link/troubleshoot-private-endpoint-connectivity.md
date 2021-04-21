---
title: Risolvere i problemi di connettività all'endpoint privato di Azure
description: Istruzioni dettagliate per diagnosticare la connettività degli endpoint privati
services: private-endpoint
documentationcenter: na
author: rdhillon
manager: narayan
editor: ''
ms.service: private-link
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: rdhillon
ms.openlocfilehash: 2a4f86d9fae7b78a57cf8da7ab42d2d4a4cd7be5
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835399"
---
# <a name="troubleshoot-azure-private-endpoint-connectivity-problems"></a>Risolvere i problemi di connettività all'endpoint privato di Azure

Questo articolo fornisce indicazioni dettagliate per convalidare e diagnosticare la configurazione della connettività dell'endpoint privato di Azure.

L'endpoint privato di Azure è un'interfaccia di rete che consente di connettersi privatamente e in modo sicuro a un servizio di collegamento privato. Questa soluzione consente di proteggere i carichi di lavoro in Azure fornendo connettività privata alle risorse del servizio di Azure dalla rete virtuale. Questa soluzione consente di portare questi servizi in modo efficace nella rete virtuale.

Ecco gli scenari di connettività disponibili con l'endpoint privato:

- Rete virtuale dalla stessa area
- Reti virtuali con peer a livello di regione
- Reti virtuali con peer a livello globale
- Clienti in locale tramite VPN o Azure ExpressRoute virtuali

## <a name="diagnose-connectivity-problems"></a>Diagnosticare i problemi di connettività 

Esaminare questi passaggi per assicurarsi che tutte le configurazioni consuete siano quelle previste per risolvere i problemi di connettività con la configurazione dell'endpoint privato.

1. Esaminare la configurazione dell'endpoint privato esplorando la risorsa.

    a. Passare a **Centro collegamento privato**.

      ![Centro collegamento privato](./media/private-endpoint-tsg/private-link-center.png)

    b. Nel riquadro sinistro selezionare **Endpoint privati.**
    
      ![Endpoint privati](./media/private-endpoint-tsg/private-endpoints.png)

    c. Filtrare e selezionare l'endpoint privato che si vuole diagnosticare.

    d. Esaminare la rete virtuale e le informazioni DNS.
     - Verificare che lo stato della connessione sia **Approvato.**
     - Assicurarsi che la macchina virtuale abbia la connettività alla rete virtuale che ospita gli endpoint privati.
     - Verificare che le informazioni fqdn (copia) e l'indirizzo IP privato siano assegnate.
    
       ![Rete virtuale e configurazione DNS](./media/private-endpoint-tsg/vnet-dns-configuration.png)
    
1. Usare [Monitoraggio di Azure](../azure-monitor/overview.md) per verificare se il flusso dei dati è in esecuzione.

    a. Nella risorsa endpoint privato selezionare **Monitoraggio**.
     - Selezionare **Byte in ingresso** o Byte in **uscita.** 
     - Verificare se i dati vengono scorrono quando si tenta di connettersi all'endpoint privato. Si prevede un ritardo di circa 10 minuti.
    
       ![Verificare i dati di telemetria degli endpoint privati](./media/private-endpoint-tsg/private-endpoint-monitor.png)

1.  Usare la **risoluzione dei problemi di connessione** della macchina virtuale da Azure Network Watcher.

    a. Selezionare la macchina virtuale client.

    b. Selezionare **Risoluzione dei problemi di** connessione e quindi selezionare la scheda Connessioni **in** uscita.
    
      ![Network Watcher- Testare le connessioni in uscita](./media/private-endpoint-tsg/network-watcher-outbound-connection.png)
    
    c. Selezionare **Usa Network Watcher per la traccia della connessione dettagliata.**
    
      ![Network Watcher- Risoluzione dei problemi di connessione](./media/private-endpoint-tsg/network-watcher-connection-troubleshoot.png)

    d. Selezionare **Test in base al nome di dominio completo**.
     - Incollare il nome di dominio completo dalla risorsa endpoint privato.
     - Specificare una porta. In genere, usare 443 Archiviazione di Azure o Azure Cosmos DB e 1336 per SQL.

    e. Selezionare **Test** e convalidare i risultati del test.
    
      ![Network Watcher - Risultati del test](./media/private-endpoint-tsg/network-watcher-test-results.png)
    
        
1. La risoluzione DNS dai risultati del test deve avere lo stesso indirizzo IP privato assegnato all'endpoint privato.

    a. Se le impostazioni DNS non sono corrette, seguire questa procedura:
     - Se si usa una zona privata: 
       - Assicurarsi che la rete virtuale della macchina virtuale client sia associata alla zona privata.
       - Verificare che il record di zona DNS privato esista. Se non esiste, crearlo.
     - Se si usa un DNS personalizzato:
       - Esaminare le impostazioni DNS personalizzate e verificare che la configurazione DNS sia corretta.
       Per indicazioni, vedere [Panoramica dell'endpoint privato: configurazione DNS.](./private-endpoint-overview.md#dns-configuration)

    b. Se la connettività non riesce a causa di gruppi di sicurezza di rete (NSG) o route definite dall'utente:
     - Esaminare le regole in uscita del gruppo di sicurezza di rete e creare le regole in uscita appropriate per consentire il traffico.
    
       ![Regole in uscita del gruppo di sicurezza di rete](./media/private-endpoint-tsg/nsg-outbound-rules.png)

1. La macchina virtuale di origine deve avere la route all'hop successivo dell'indirizzo IP dell'endpoint privato come InterfaceEndpoints nelle route effettive della scheda di interfaccia di rete. 

    a. Se non è possibile visualizzare la route dell'endpoint privato nella macchina virtuale di origine, verificare se 
     - La macchina virtuale di origine e l'endpoint privato appartengono alla stessa rete virtuale. In caso affermativa, è necessario coinvolgere il supporto. 
     - La macchina virtuale di origine e l'endpoint privato fanno parte di reti virtuali diverse, quindi verificare la connettività IP tra le reti virtuali. Se è presente connettività IP e non è ancora possibile visualizzare la route, coinvolgere il supporto. 

1. Se la connessione ha restituito risultati convalidati, il problema di connettività potrebbe essere correlato ad altri aspetti, ad esempio segreti, token e password a livello di applicazione.
   - In questo caso, esaminare la configurazione della risorsa collegamento privato associata all'endpoint privato. Per altre informazioni, vedere la guida collegamento privato di Azure [risoluzione dei problemi](troubleshoot-private-link-connectivity.md)
   
1. È sempre bene restringersi prima di generare il ticket di supporto. 

    a. Se l'origine si connette in locale all'endpoint privato in Azure con problemi, provare a connettersi 
      - A un'altra macchina virtuale da locale e verificare se si dispone di connettività IP alla rete virtuale da locale. 
      - Da una macchina virtuale nella rete virtuale all'endpoint privato.
      
    b. Se l'origine è Azure e l'endpoint privato si trova in una rete virtuale diversa, provare a connettersi 
      - Per l'endpoint privato da un'origine diversa. In questo modo è possibile isolare eventuali problemi specifici della macchina virtuale. 
      - A qualsiasi macchina virtuale che fa parte della stessa rete virtuale di quella dell'endpoint privato.  

1. Contattare il team [supporto di Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) se il problema non è ancora risolto e se esiste ancora un problema di connettività.

## <a name="next-steps"></a>Passaggi successivi

 * [Creare un endpoint privato nella subnet aggiornata (portale di Azure)](./create-private-endpoint-portal.md)
 * [collegamento privato di Azure guida alla risoluzione dei problemi](troubleshoot-private-link-connectivity.md)
