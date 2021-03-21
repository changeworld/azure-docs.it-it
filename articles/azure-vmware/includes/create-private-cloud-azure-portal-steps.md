---
title: Creare un cloud privato della soluzione Azure VMware
description: Procedura per creare un cloud privato della soluzione VMware di Azure usando il portale di Azure.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: da79881e609f982960468a8f26c98178f972ad43
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101725405"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Crea una nuova risorsa**. Nella casella di testo **Cerca nel marketplace** digitare `Azure VMware Solution` e selezionare **Soluzione Azure VMware** nell'elenco. Nella finestra **Soluzione Azure VMware** selezionare **Crea**.

1. Nella scheda **Informazioni di base** immettere i valori per i campi. La tabella seguente elenca le proprietà per i campi.

   | Campo   | Valore  |
   | ---| --- |
   | **Sottoscrizione** | La sottoscrizione che si intende usare per la distribuzione.|
   | **Gruppo di risorse** | Il gruppo di risorse per le risorse del cloud privato. |
   | **Posizione** | Selezionare una località, ad esempio **Stati Uniti orientali**.|
   | **Nome risorsa** | Nome del cloud privato della soluzione Azure VMware. |
   | **SKU** | Selezionare il valore di SKU seguente: AV36 |
   | **Host** | Numero di host da aggiungere al cluster del cloud privato. Il valore predefinito è 3 e può essere aumentato o ridotto dopo la distribuzione.  |
   | **Blocco di indirizzi** | Immettere un blocco di indirizzi IP per la rete CIDR per il cloud privato, ad esempio 10.175.0.0/22. |
   | **Rete virtuale** | Selezionare una rete virtuale o crearne una nuova per il cloud privato della soluzione Azure VMware.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Nella scheda Informazioni di base immettere i valori per i campi." border="true":::

1. Al termine, selezionare **Rivedi e crea**. Nella schermata successiva verificare le informazioni immesse. Se tutte le informazioni sono corrette, selezionare **Crea**.

   > [!NOTE]
   > Questo passaggio richiede circa due ore. 

1. Verificare che la distribuzione sia riuscita. Passare al gruppo di risorse creato e selezionare il cloud privato.  Al termine della distribuzione, verrà visualizzato lo stato **Completato**. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Verificare che la distribuzione sia riuscita." border="true":::