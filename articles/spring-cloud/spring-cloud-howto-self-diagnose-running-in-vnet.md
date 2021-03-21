---
title: Come diagnosticare autonomamente Azure Spring cloud VNET
description: Informazioni su come diagnosticare e risolvere autonomamente i problemi nel cloud Spring di Azure in esecuzione in VNET.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/25/2021
ms.custom: devx-track-java
ms.openlocfilehash: 5407213b62902326d53b73e42ee3af1ba9b11524
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102125099"
---
# <a name="self-diagnose-running-azure-spring-cloud-in-vnet"></a>Diagnostica automatica che esegue Azure Spring cloud in VNET
La diagnostica cloud Spring di Azure supporta le app interattive per la risoluzione dei problemi in esecuzione in reti virtuali senza configurazione. La diagnostica cloud Spring di Azure identifica i problemi e guida le informazioni che consentono di risolvere i problemi e risolverli.

## <a name="navigate-to-the-diagnostics-page"></a>Passare alla pagina di diagnostica
La procedura seguente avvia la diagnostica per le applicazioni di rete.
1. Accedere al portale di Azure.
1. Passare alla pagina Panoramica di Azure Spring Cloud.
1. Selezionare **diagnostica e Risolvi i problemi** nel menu nel riquadro di spostamento a sinistra.
1. Selezionare la terza categoria, **rete**.

   ![Titolo di diagnostica automatica](media/spring-cloud-self-diagnose-vnet/self-diagostic-title.png)

## <a name="view-a-diagnostic-report"></a>Visualizzare un report di diagnostica
Dopo aver fatto clic sulla categoria **rete** , è possibile visualizzare due problemi relativi alla rete specifica del cloud Spring VNet inserito in Azure: **risoluzione DNS** e **traffico in uscita richiesto**.

   ![Opzioni di diagnostica automatica](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-req-outbound-options.png)

Selezionare il problema di destinazione per visualizzare il report di diagnostica. Verrà visualizzato un riepilogo della diagnostica, ad esempio: 

* *La risorsa è stata rimossa.*
* *La risorsa non viene distribuita nella propria rete virtuale*.

Alcuni risultati contengono documentazione correlata. Diverse subnet visualizzeranno i risultati separatamente.

## <a name="dns-resolution"></a>Risoluzione DNS 
Se si seleziona la **risoluzione DNS**, i risultati indicheranno se sono presenti problemi DNS con le applicazioni.  Le app integre sono elencate di seguito:

* *Problemi DNS risolti senza problemi nella subnet ' subnet01'*.
* *Problemi DNS risolti senza problemi nella subnet ' subnet02'*.

L'esempio di report di diagnostica seguente indica che l'integrità dell'applicazione è sconosciuta. L'intervallo di tempo di Reporting non include l'ora in cui è stato segnalato lo stato di integrità.  Si supponga che l'ora di fine del contesto sia *2021-03-03T04:20:00Z*. Il TIMESTAMP più recente nei **rendering della tabella di risoluzione DNS** è *2021-03-03T03:39:00Z*, il giorno precedente. Il log di controllo integrità potrebbe non essere stato inviato a causa di una rete bloccata. 

I risultati di stato di integrità sconosciuti contengono documentazione correlata.  È possibile fare clic sulla parentesi uncinata aperta per visualizzare la visualizzazione a discesa.

   ![DNS sconosciuto](media/spring-cloud-self-diagnose-vnet/self-diagostic-dns-unknown.png)

Se il set di record della zona DNS privato non è stato configurato correttamente, si otterrà un risultato critico, ad esempio: `Failed to resolve the Private DNS in subnet xxx` . 

Nell'elenco a discesa **rendering della tabella di risoluzione DNS** sono disponibili informazioni dettagliate sul messaggio da cui è possibile controllare la configurazione.

## <a name="required-outbound-traffic"></a>Traffico in uscita obbligatorio 

Se si seleziona il **traffico in uscita richiesto**, i risultati indicheranno se sono presenti problemi di traffico in uscita con le applicazioni.  Le app integre sono elencate di seguito:

* * Traffico in uscita obbligatorio risolto senza problemi nella subnet ' subnet01'.
* * Traffico in uscita obbligatorio risolto senza problemi nella subnet ' subnet02'.

Se una subnet è bloccata da NSG o dalle regole del firewall e se il log non è stato bloccato, si verificano i seguenti errori. È possibile controllare se sono state trascurate le [responsabilità dei clienti](spring-cloud-vnet-customer-responsibilities.md).
    
   ![Endpoint non riuscito](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-failed.png)

Se non sono presenti dati in `Required Outbound Traffic Table Renderings` entro 30 minuti, il risultato sarà `health status unknown` . Probabilmente la rete è bloccata o il servizio di log è inattivo.

   ![Endpoint di diagnostica sconosciuto](media/spring-cloud-self-diagnose-vnet/self-diagostic-endpoint-unknown.png)

## <a name="see-also"></a>Vedi anche
* [Come diagnosticare autonomamente Azure Spring cloud](spring-cloud-howto-self-diagnose-solve.md)