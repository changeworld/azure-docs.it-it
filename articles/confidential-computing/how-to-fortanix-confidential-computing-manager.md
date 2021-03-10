---
title: Fortanix Confidential computing Manager in un'applicazione gestita di Azure
description: Informazioni su come distribuire Fortanix Confidential computing Manager (CCM) in un'applicazione gestita nel portale di Azure.
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/03/2021
ms.author: jencook
ms.openlocfilehash: 757ce9b7502316bbc8a5b8f27ba672048b7bbace
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102563422"
---
# <a name="fortanix-confidential-computing-manager-in-an-azure-managed-application"></a>Fortanix Confidential computing Manager in un'applicazione gestita di Azure

Questo articolo illustra come distribuire un'applicazione gestita da Fortanix Confidential computing Manager nel portale di Azure.

Fortanix è un fornitore di software di terze parti con prodotti e servizi basati sull'infrastruttura di Azure. In Azure sono disponibili altri provider di terze parti che offrono servizi di elaborazione riservati analoghi.

> [!NOTE]
>I prodotti a cui viene fatto riferimento in questo documento non sono sotto il controllo di Microsoft. Microsoft fornisce queste informazioni solo per praticità e il riferimento a questi prodotti non Microsoft non implica l'approvazione da parte di Microsoft.

## <a name="prerequisites"></a>Prerequisiti

- Un registro Docker privato per eseguire il push delle immagini dell'applicazione convertite.
- Se non si ha una sottoscrizione di Azure, [creare un account](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/) prima di iniziare.

## <a name="deploy-a-confidential-computing-manager-through-an-azure-managed-application"></a>Distribuire un gestore di computing riservato tramite un'applicazione gestita di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/azure-portal.png" alt-text="portale di Azure.":::

2. Nella barra di ricerca cercare "Fortanix Confidential computing Manager" per visualizzare l'inserzione del Marketplace per Fortanix CCM. Selezionare **Fortanix Confidential computing Manager in Azure**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/search-marketplace-listing.png" alt-text="Inserzione nel Marketplace.":::

3. Viene aperta la pagina in cui viene creata l'applicazione gestita da CCM. Selezionare **Crea**.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/create-managed-application.png" alt-text="Creare un'applicazione.":::

4. Compilare tutti i campi obbligatori.
   1. Nella sezione Dettagli applicazione gestita, il campo **gruppo di risorse gestite** avrà un valore predefinito che l'utente può modificare se necessario.
   2. Nel campo **regione** selezionare **Australia orientale**, **Australia sudorientale**, **Stati Uniti orientali**, **Stati Uniti occidentali 2**, **Europa occidentale**, **Europa settentrionale**, **Canada centrale**, **Canada orientale** o **Stati Uniti orientali 2 EUAP**.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/required-fields.png" alt-text="Campi obbligatori":::

   Selezionare **Verifica + crea** per creare l'applicazione gestita da CCM Fortanix.

5. Esaminare i dettagli e, al termine della convalida, selezionare la casella di controllo Accetto **i termini e le condizioni** e quindi selezionare **Crea** per creare l'applicazione gestita.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/review-details.png" alt-text="Esaminare i dettagli.":::

6. Viene avviata la distribuzione CCM Fortanix e viene inviata una notifica che indica che la distribuzione è in corso.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-progress.png" alt-text="Stato della distribuzione.":::

7. Al termine della distribuzione, fare clic sul pulsante **Vai alla risorsa** per passare alla pagina "panoramica" dell'applicazione gestita da CCM distribuita per registrare il nodo di calcolo.

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-resource.png" alt-text="Screenshot che mostra una distribuzione corretta nell'portale di Azure.]":::

   :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-overview.png" alt-text="Screenshot che mostra una panoramica della risorsa di elaborazione riservata nel portale di Azure.":::

## <a name="enroll-the-compute-node-in-fortanix-ccm"></a>Registrare il nodo di calcolo in Fortanix CCM

1. Selezionare **Confidential computing Manager** dal menu di spostamento a sinistra. Accedere a Fortanix CCM e creare un account come visualizzato nella **Figura 9**.

    Per altri dettagli su come eseguire l'iscrizione, accedere e creare un account in CCM fare riferimento a [ccm Introduzione](https://support.fortanix.com/hc/en-us/articles/360034373551-User-s-Guide-Logging-in).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/fortanix-login.png" alt-text="Screenshot che mostra l'account di accesso di Fortanix Confidential computing Manager.":::
    
2. Per ottenere il token di join dalla console di gestione CCM, selezionare prima il pulsante **registra nodo** . Quindi, nella finestra Registra nodo selezionare il pulsante **copia** per copiare il token di join.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/get-join-token.png" alt-text="Screenshot che Mostra come ottenere il token di join.":::

3. A questo punto, per registrare un agente del nodo, selezionare la scheda **Confidential computing node Agent** e selezionare **Aggiungi** per aggiungere un agente del nodo CCM.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/add-node-agent.png" alt-text="Screenshot che mostra l'aggiunta dell'agente del nodo.":::

4.  Nel modulo dell'agente del nodo CCM, compilare tutti i campi obbligatori. Incollare il token di join copiato nel passaggio 2 nel **token di join**. Selezionare **Verifica + Invia** per confermare.

    Per altre informazioni su come registrare un nodo di calcolo CCM, vedere [registrare il nodo di calcolo](https://support.fortanix.com/hc/en-us/articles/360043085652-User-s-Guide-Compute-Nodes).
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/enroll-compute-node.png" alt-text="Screenshot che mostra la registrazione del nodo di calcolo.":::
    
5. Al termine della convalida, selezionare **Invia** per completare la creazione dell'agente del nodo.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-agent-created.png" alt-text="Screenshot che mostra che l'agente del nodo è stato creato.":::

6. Per controllare lo stato della distribuzione, passare alla scheda **Panoramica** e selezionare collegamento **gruppo di risorse gestite** .

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-enrolled.png" alt-text="Screenshot che mostra il nodo registrato.":::
    
    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/managed-resource-group.png" alt-text="Screenshot che mostra il controllo dello stato di distribuzione.":::

7. A questo punto si noterà che lo stato della distribuzione è ancora in corso e che l'agente del nodo sarà registrato correttamente.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-in-progress.png" alt-text="Screenshot che mostra la distribuzione in corso.":::

8. Quando la registrazione dell'agente del nodo ha esito positivo, lo stato diventa "succeeded".

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/deployment-succeeded.png" alt-text="Screenshot che mostra la distribuzione riuscita.":::

9. Nell'applicazione gestita da CCM passare alle pagine dei nodi di calcolo e si noterà che il nodo si trova in uno stato **attivo** ed è stato registrato correttamente.

    :::image type="content" source="media/how-to-fortanix-confidential-computing-manager/node-active-state.png" alt-text="Screenshot che mostra il nodo registrato correttamente.":::

## <a name="clean-up-resources"></a>Pulire le risorse

L'utente può anche eliminare un agente del nodo CCM dalla pagina agente del nodo di elaborazione riservata. Per eliminare l'agente del nodo, selezionare l'agente del nodo e fare clic sul pulsante **Elimina** nella barra superiore.

:::image type="content" source="media/how-to-fortanix-confidential-computing-manager/delete-node-agent.png" alt-text="Screenshot che mostra l'eliminazione dell'agente del nodo.":::

## <a name="next-steps"></a>Passaggi successivi

In questa Guida introduttiva è stato registrato un nodo usando un'app gestita di Azure per la gestione dei privilegi di Fortanix. La registrazione del nodo consente di convertire l'immagine dell'applicazione per l'esecuzione in una macchina virtuale di elaborazione riservata. Per altre informazioni sulle macchine virtuali di confidential computing in Azure, vedere [Soluzioni nelle macchine virtuali](virtual-machine-solutions.md).

Per altre informazioni sulle offerte di informatica riservate di Azure, vedere [Azure Confidential computing](overview.md).

Informazioni su come completare attività simili con altre offerte di terze parti in Azure, ad esempio [Anjuna](https://azuremarketplace.microsoft.com/marketplace/apps/anjuna-5229812.aee-az-v1) e [Scone](https://sconedocs.github.io).

