---
title: Distribuire Gestione traffico per il bilanciamento dei carichi di lavoro della soluzione VMware di Azure
description: Informazioni su come integrare Gestione traffico con la soluzione VMware di Azure per bilanciare i carichi di lavoro delle applicazioni tra più endpoint in aree diverse.
ms.topic: how-to
ms.date: 02/08/2021
ms.openlocfilehash: 46570c5a61fc0a641d83126fd0f8ef35b3dc42cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988595"
---
# <a name="deploy-traffic-manager-to-balance-azure-vmware-solution-workloads"></a>Distribuire Gestione traffico per il bilanciamento dei carichi di lavoro della soluzione VMware di Azure

Questo articolo illustra i passaggi per integrare [Gestione traffico di Azure](../traffic-manager/traffic-manager-overview.md) con la soluzione VMware di Azure. L'integrazione bilancia i carichi di lavoro dell'applicazione tra più endpoint. Questo articolo illustra anche come configurare Gestione traffico per indirizzare il traffico tra tre [applicazione Azure gateway](../application-gateway/overview.md) che si estende su più aree della soluzione VMware di Azure. 

I gateway hanno macchine virtuali (VM) della soluzione VMware di Azure configurate come membri del pool back-end per il bilanciamento del carico delle richieste di livello 7 in ingresso. Per altre informazioni, vedere [usare applicazione Azure gateway per proteggere le app Web in una soluzione VMware di Azure](protect-azure-vmware-solution-with-application-gateway.md)

Il diagramma mostra come gestione traffico fornisce il bilanciamento del carico per le applicazioni a livello di DNS tra gli endpoint internazionali. I gateway hanno membri del pool back-end configurati come server IIS e a cui si fa riferimento come endpoint esterni della soluzione VMware di Azure. La connessione tramite la rete virtuale tra le due aree del cloud privato usa un gateway ExpressRoute.   

:::image type="content" source="media/traffic-manager/traffic-manager-topology.png" alt-text="Diagramma dell'architettura dell'integrazione di gestione traffico con la soluzione VMware di Azure" lightbox="media/traffic-manager/traffic-manager-topology.png" border="false":::

Prima di iniziare, esaminare prima di tutto i [prerequisiti](#prerequisites) e quindi le procedure per:

> [!div class="checklist"]
> * Verificare la configurazione dei gateway applicazione e del segmento NSX-T
> * Creare il profilo di gestione traffico
> * Aggiungere endpoint esterni al profilo di gestione traffico

## <a name="prerequisites"></a>Prerequisiti

- Tre macchine virtuali configurate come server Microsoft IIS in esecuzione in diverse aree della soluzione VMware di Azure: 
   - Stati Uniti occidentali
   - Europa occidentale
   - Stati Uniti orientali (locale) 

- Un gateway applicazione con endpoint esterni nelle aree della soluzione VMware di Azure menzionate in precedenza.

- Host con connettività Internet per la verifica. 

- [Segmento di rete NSX-T creato nella soluzione VMware di Azure](tutorial-nsx-t-network-segment.md).

## <a name="verify-your-application-gateways-configuration"></a>Verificare la configurazione dei gateway applicazione

I passaggi seguenti verificano la configurazione dei gateway applicazione.

1. Nella portale di Azure selezionare **gateway applicazione** per visualizzare un elenco dei gateway applicazione correnti:

   - AVS-GW-WUS
   - AVS-GW-EUS (locale)
   - AVS-GW-UEO

   :::image type="content" source="media/traffic-manager/app-gateways-list-1.png" alt-text="Screenshot della pagina del gateway applicazione che mostra l'elenco dei gateway applicazione configurati." lightbox="media/traffic-manager/app-gateways-list-1.png":::

1. Selezionare uno dei gateway applicazione distribuiti in precedenza. 

   Viene visualizzata una finestra che mostra varie informazioni sul gateway applicazione. 

   :::image type="content" source="media/traffic-manager/backend-pool-config.png" alt-text="Screenshot della pagina del gateway applicazione che mostra i dettagli del gateway applicazione selezionato." lightbox="media/traffic-manager/backend-pool-config.png":::

1. Selezionare **pool back-end** per verificare la configurazione di uno dei pool back-end. Viene visualizzato un membro del pool back-end della VM configurato come server Web con un indirizzo IP di 172.29.1.10.
 
   :::image type="content" source="media/traffic-manager/backend-pool-ip-address.png" alt-text="Screenshot della pagina Modifica pool back-end con indirizzo IP di destinazione evidenziato.":::

1. Verificare la configurazione degli altri gateway applicazione e dei membri del pool back-end. 

## <a name="verify-the-nsx-t-segment-configuration"></a>Verificare la configurazione del segmento NSX-T

I passaggi seguenti verificano la configurazione del segmento NSX-T nell'ambiente della soluzione VMware di Azure.

1. Selezionare **segmenti** per visualizzare i segmenti configurati.  Si noterà che contoso-segment1 è connesso al gateway contoso-T01, un router flessibile di livello 1.

   :::image type="content" source="media/traffic-manager/nsx-t-segment-avs.png" alt-text="Screenshot che mostra i profili dei segmenti in NSX-T Manager." lightbox="media/traffic-manager/nsx-t-segment-avs.png":::    

1. Selezionare **gateway di primo livello** per visualizzare un elenco di gateway di livello 1 con il numero di segmenti collegati. 

   :::image type="content" source="media/traffic-manager/nsx-t-segment-linked-2.png" alt-text="Screenshot che mostra l'indirizzo del gateway del segmento selezionato.":::    

1. Selezionare il segmento collegato a Contoso-T01. Viene visualizzata una finestra che mostra l'interfaccia logica configurata nel router Tier-01. Funge da gateway per la macchina virtuale membro del pool back-end connessa al segmento.

1. Nel client di vSphere selezionare la macchina virtuale per visualizzarne i dettagli. 

   >[!NOTE]
   >Il relativo indirizzo IP corrisponde al membro del pool back-end della VM configurato come server Web dalla sezione precedente: 172.29.1.10.

   :::image type="content" source="media/traffic-manager/nsx-t-vm-details.png" alt-text="Screenshot che mostra i dettagli della VM in vSphere client." lightbox="media/traffic-manager/nsx-t-vm-details.png":::    

4. Selezionare la macchina virtuale, quindi selezionare **azioni > modifica impostazioni** per verificare la connessione al segmento NSX-T.

## <a name="create-your-traffic-manager-profile"></a>Creare il profilo di gestione traffico

1. Accedere al [portale di Azure](https://rc.portal.azure.com/#home). In **servizi di Azure > rete** selezionare **profili di gestione traffico**.

2. Selezionare **+ Aggiungi** per creare un nuovo profilo di gestione traffico.
 
3. Fornire le informazioni seguenti e quindi selezionare **Crea**:

   - Nome profilo
   - Metodo di routing (usare [ponderato](../traffic-manager/traffic-manager-routing-methods.md)
   - Subscription
   - Resource group

## <a name="add-external-endpoints-into-the-traffic-manager-profile"></a>Aggiungere endpoint esterni al profilo di gestione traffico

1. Selezionare il profilo di gestione traffico nel riquadro dei risultati della ricerca, selezionare **endpoint** e quindi **+ Aggiungi**.

1. Per ogni endpoint esterno nelle diverse aree, immettere i dettagli richiesti e quindi selezionare **Aggiungi**: 
   - Type
   - Nome
   - Nome di dominio completo (FQDN) o IP
   - Peso (assegnare un peso di 1 a ogni endpoint). 

   Una volta creati, tutti e tre sono visualizzati nel profilo di gestione traffico. Lo stato di monitoraggio di tutti e tre deve essere **online**.

3. Selezionare **Panoramica** e copiare l'URL in **nome DNS**.

   :::image type="content" source="media/traffic-manager/traffic-manager-endpoints.png" alt-text="Screenshot che mostra una panoramica dell'endpoint di gestione traffico con il nome DNS evidenziato." lightbox="media/traffic-manager/traffic-manager-endpoints.png"::: 

4. Incollare l'URL del nome DNS in un browser. La schermata mostra il traffico indirizzato all'area Europa occidentale.

   :::image type="content" source="media/traffic-manager/traffic-to-west-europe.png" alt-text="Screenshot della finestra del browser che mostra il traffico indirizzato all'Europa occidentale."::: 

5. Aggiornare il browser. La schermata mostra il traffico indirizzato a un altro set di membri del pool back-end nell'area Stati Uniti occidentali.

   :::image type="content" source="media/traffic-manager/traffic-to-west-us.png" alt-text="Screenshot della finestra del browser che mostra il traffico indirizzato agli Stati Uniti occidentali."::: 

6. Aggiornare di nuovo il browser. La schermata mostra il traffico diretto al set finale di membri del pool back-end locali.

   :::image type="content" source="media/traffic-manager/traffic-to-on-premises.png" alt-text="Screenshot della finestra del browser che mostra il traffico indirizzato al sito locale.":::

## <a name="next-steps"></a>Passaggi successivi

Ora che è stata analizzata l'integrazione di gestione traffico di Azure con la soluzione VMware di Azure, è possibile ottenere informazioni su:

- [Uso di applicazione Azure gateway in una soluzione VMware di Azure](protect-azure-vmware-solution-with-application-gateway.md).
- [Metodi di routing di gestione traffico](../traffic-manager/traffic-manager-routing-methods.md).
- [Combinare i servizi di bilanciamento del carico in Azure](../traffic-manager/traffic-manager-load-balancing-azure.md).
- [Misurazione delle prestazioni di gestione traffico](../traffic-manager/traffic-manager-performance-considerations.md).
