---
title: Distribuire Gestione traffico per bilanciare i soluzione Azure VMware di lavoro
description: Informazioni su come integrare Gestione traffico con soluzione Azure VMware bilanciare i carichi di lavoro delle applicazioni tra più endpoint in aree diverse.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 029bb9512bd19effd1c7aeb5104c7bb6d7ccdca5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876923"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Distribuire Gestione traffico per bilanciare i soluzione Azure VMware di lavoro

Questo articolo illustra i passaggi per integrare Gestione traffico di Azure [con](../traffic-manager/traffic-manager-overview.md) soluzione Azure VMware. L'integrazione bilancia i carichi di lavoro dell'applicazione tra più endpoint. Questo articolo illustra anche i passaggi per configurare Gestione traffico [](../application-gateway/overview.md) per indirizzare il traffico tra tre gateway applicazione di Azure che si estendono su soluzione Azure VMware aree. 

I gateway hanno soluzione Azure VMware macchine virtuali (VM) configurate come membri del pool back-end per bilanciare il carico delle richieste di livello 7 in ingresso. Per altre informazioni, vedere [Usare gateway applicazione di Azure per proteggere le app Web in soluzione Azure VMware](protect-azure-vmware-solution-with-application-gateway.md)

Il diagramma mostra come Gestione traffico fornisce il bilanciamento del carico per le applicazioni a livello DNS tra endpoint regionali. I gateway hanno membri del pool back-end configurati come server IIS e a cui viene fatto riferimento soluzione Azure VMware endpoint esterni. La connessione tramite la rete virtuale tra le due aree del cloud privato usa un gateway ExpressRoute.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagramma dell'architettura dell'integrazione di Gestione traffico con soluzione Azure VMware" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Prima di iniziare, esaminare prima [i](#prerequisites) prerequisiti e quindi verranno illustrate le procedure per:

> [!div class="checklist"]
> * Verificare la configurazione dei gateway applicazione e del segmento NSX-T
> * Creare il profilo di Gestione traffico
> * Aggiungere endpoint esterni al profilo di Gestione traffico

## <a name="prerequisites"></a>Prerequisiti

- Tre macchine virtuali configurate come server IIS Microsoft in esecuzione in aree soluzione Azure VMware diverse: 
   - Stati Uniti occidentali
   - Europa occidentale
   - Stati Uniti orientali (locale) 

- Un gateway applicazione con endpoint esterni nelle aree soluzione Azure VMware indicate in precedenza.

- Host con connettività Internet per la verifica. 

- Un [segmento di rete NSX-T creato in soluzione Azure VMware](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Verificare la configurazione dei gateway applicazione

I passaggi seguenti verificano la configurazione dei gateway applicazione.

1. Nel portale di Azure gateway applicazione selezionare **Gateway applicazione per** visualizzare un elenco dei gateway applicazione correnti:

   - AVS-GW-WUS
   - AVS-GW-EUS (locale)
   - AVS-GW-WEU

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Screenshot della pagina Gateway applicazione che mostra l'elenco dei gateway applicazione configurati." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Selezionare uno dei gateway applicazione distribuiti in precedenza. 

   Viene visualizzata una finestra che mostra varie informazioni sul gateway applicazione. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Screenshot della pagina Gateway applicazione che mostra i dettagli del gateway applicazione selezionato." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Selezionare **Pool back-end** per verificare la configurazione di uno dei pool back-end. Viene visualizzato un membro del pool back-end della macchina virtuale configurato come server Web con indirizzo IP 172.29.1.10.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Screenshot della pagina Modifica pool back-end con l'indirizzo IP di destinazione evidenziato.":::

1. Verificare la configurazione degli altri gateway applicazione e dei membri del pool back-end. 

## <a name="verify-the-nsx-t-segment-configuration"></a>Verificare la configurazione del segmento NSX-T

I passaggi seguenti verificano la configurazione del segmento NSX-T nell'soluzione Azure VMware virtuale.

1. Selezionare **Segmenti** per visualizzare i segmenti configurati.  Viene visualizzato Contoso-segment1 connesso al gateway Contoso-T01, un router flessibile di livello 1.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Screenshot che mostra i profili di segmento in NSX-T Manager." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Selezionare **Gateway di livello 1 per** visualizzare un elenco di gateway di livello 1 con il numero di segmenti collegati. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Screenshot che mostra l'indirizzo del gateway del segmento selezionato.":::    

1. Selezionare il segmento collegato a Contoso-T01. Viene visualizzata una finestra che mostra l'interfaccia logica configurata nel router di livello 01. Funge da gateway per la macchina virtuale membro del pool back-end connessa al segmento.

1. Nel client vSphere selezionare la macchina virtuale per visualizzarne i dettagli. 

   >[!NOTE]
   >L'indirizzo IP corrisponde al membro del pool back-end della macchina virtuale configurato come server Web dalla sezione precedente: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Screenshot che mostra i dettagli della macchina virtuale nel client vSphere." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Selezionare la macchina virtuale, quindi **selezionare AZIONI > Modifica impostazioni** per verificare la connessione al segmento NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Creare il profilo di Gestione traffico

1. Accedere al [portale di Azure](https://rc.portal.azure.com/#home). In **Servizi di Azure > rete** selezionare Profili di Gestione **traffico.**

2. Selezionare **+ Aggiungi** per creare un nuovo profilo di Gestione traffico.
 
3. Specificare le informazioni seguenti e quindi selezionare **Crea:**

   - Nome profilo
   - Metodo di routing (usare [ponderato](../traffic-manager/traffic-manager-routing-methods.md)
   - Subscription
   - Resource group

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Aggiungere endpoint esterni nel profilo di Gestione traffico

1. Selezionare il profilo di Gestione traffico nel riquadro dei risultati della ricerca, **selezionare Endpoint** e quindi **+ Aggiungi.**

1. Per ognuno degli endpoint esterni nelle diverse aree, immettere i dettagli necessari e quindi selezionare **Aggiungi:** 
   - Type
   - Nome
   - Nome di dominio completo (FQDN) o IP
   - Peso (assegnare un peso di 1 a ogni endpoint). 

   Dopo la creazione, tutte e tre le informazioni vengono visualizzate nel profilo di Gestione traffico. Lo stato del monitoraggio di tutti e tre deve essere **Online.**

3. Selezionare **Panoramica** e copiare l'URL in **Nome DNS**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Screenshot che mostra una panoramica dell'endpoint di Gestione traffico con il nome DNS evidenziato." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Incollare l'URL del nome DNS in un browser. Lo screenshot mostra il traffico diretto all'area Europa occidentale.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Screenshot della finestra del browser che mostra il traffico indirizzato all'Europa occidentale."::: 

5. Aggiornare il browser. Lo screenshot mostra il traffico diretto a un altro set di membri del pool back-end nell'area Stati Uniti occidentali.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Screenshot della finestra del browser che mostra il traffico instradato agli Stati Uniti occidentali."::: 

6. Aggiornare di nuovo il browser. Lo screenshot mostra il traffico che indirizza al set finale di membri del pool back-end in locale.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Screenshot della finestra del browser che mostra il traffico instradato in locale.":::

## <a name="next-steps"></a>Passaggi successivi

Dopo aver trattato l'integrazione di Gestione traffico di Azure con soluzione Azure VMware, è possibile ottenere informazioni su:

- [Uso gateway applicazione di Azure in soluzione Azure VMware](protect-azure-vmware-solution-with-application-gateway.md)
- [Metodi di routing di Gestione traffico](../traffic-manager/traffic-manager-routing-methods.md)
- [Combinazione dei servizi di bilanciamento del carico in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md)
- [Misurazione delle prestazioni di Gestione traffico](../traffic-manager/traffic-manager-performance-considerations.md)
