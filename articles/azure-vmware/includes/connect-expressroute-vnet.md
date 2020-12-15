---
title: Connettere ExpressRoute al gateway di rete virtuale
description: Passaggi per connettere ExpressRoute al gateway di rete virtuale.
ms.topic: include
ms.date: 12/08/2020
ms.openlocfilehash: 5f9a565a7662041dbd85e61388129496fa376962
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861519"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-configure-networking.md -->

1. Passare al cloud privato creato nell'esercitazione [Distribuire un cluster vSphere in Azure](../tutorial-create-private-cloud.md). Selezionare **Connettività** in **Gestisci**, quindi selezionare la scheda **ExpressRoute**.

1. Copiare la chiave di autorizzazione. Se non è presente una chiave di autorizzazione, è necessario crearne una. Selezionare **+ Richiedi una chiave di autorizzazione**.

   :::image type="content" source="../media/expressroute-global-reach/start-request-auth-key.png" alt-text="Copiare la chiave di autorizzazione. Se non è presente una chiave di autorizzazione, è necessario crearne una. Selezionare + Richiedi una chiave di autorizzazione." border="true" lightbox="../media/expressroute-global-reach/start-request-auth-key.png":::

1. Passare al gateway di rete virtuale creato nel passaggio precedente e quindi, in **Impostazioni**, selezionare **Connessioni**. Nella pagina **Connessioni** selezionare **+ Aggiungi**.

1. Nella pagina **Aggiungi connessione** specificare i valori per i campi e selezionare **OK**. 

   | Campo | valore |
   | --- | --- |
   | **Nome**  | Immettere un nome per la connessione.  |
   | **Tipo di connessione**  | selezionare **ExpressRoute**.  |
   | **Riscatta autorizzazione**  | Assicurarsi che questa casella sia selezionata.  |
   | **Gateway di rete virtuale** | Gateway di rete virtuale creato in precedenza.  |
   | **Chiave di autorizzazione**  | Copiare a incollare la chiave di autorizzazione dalla scheda ExpressRoute per il gruppo di risorse. |
   | **URI del circuito peer**  | Copiare e incollare l'ID ExpressRoute dalla scheda ExpressRoute per il gruppo di risorse.  |

   :::image type="content" source="../media/expressroute-global-reach/open-cloud-shell.png" alt-text="Nella pagina Aggiungi connessione specificare i valori per i campi e fare clic su OK." border="true" lightbox="../media/expressroute-global-reach/open-cloud-shell.png":::

Viene creata la connessione tra il circuito ExpressRoute e la rete virtuale.