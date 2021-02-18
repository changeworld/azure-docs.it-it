---
title: Creare un cloud privato della soluzione Azure VMware
description: Procedura per creare un cloud privato della soluzione VMware di Azure usando il portale di Azure.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: 983dccfaa9ea43955bfecc68bbbe432c579d51d1
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653135"
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
   | **Password dell'amministratore di vCenter** | Immettere la password dell'amministratore del cloud. |
   | **Password di NSX-T Manager** | Immettere la password amministratore di NSX-T. |
   | **Blocco di indirizzi** | Immettere un blocco di indirizzi IP per la rete CIDR per il cloud privato, ad esempio 10.175.0.0/22. |
   | **Rete virtuale** | Selezionare una rete virtuale o crearne una nuova per il cloud privato della soluzione Azure VMware.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Nella scheda Informazioni di base immettere i valori per i campi." border="true":::

1. Al termine, selezionare **Rivedi e crea**. Nella schermata successiva verificare le informazioni immesse. Se tutte le informazioni sono corrette, selezionare **Crea**.

   > [!NOTE]
   > Questo passaggio richiede circa due ore. 

1. Verificare che la distribuzione sia riuscita. Passare al gruppo di risorse creato e selezionare il cloud privato.  Al termine della distribuzione, verrà visualizzato lo stato **Completato**. 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Verificare che la distribuzione sia riuscita." border="true":::