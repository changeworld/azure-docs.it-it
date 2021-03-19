---
title: Creare un'area di lavoro con la protezione Exfiltration dati abilitata
description: Questo articolo illustra come creare un'area di lavoro con protezione Exfiltration dati in Azure sinapsi Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 59c2f5e5738b29aa11e9227b157f8b11d53f2b25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598083"
---
# <a name="create-a-workspace-with-data-exfiltration-protection-enabled"></a>Creare un'area di lavoro con la protezione Exfiltration dati abilitata
Questo articolo descrive come creare un'area di lavoro con la protezione Exfiltration dati abilitata e come gestire i tenant Azure AD approvati per questa area di lavoro.

>[!Note]
>Dopo la creazione dell'area di lavoro, non è possibile modificare la configurazione dell'area di lavoro per la rete virtuale gestita e la protezione Exfiltration dati.

## <a name="prerequisites"></a>Prerequisiti
- Autorizzazioni per la creazione di una risorsa dell'area di lavoro in Azure.
- Autorizzazioni dell'area di lavoro sinapsi per creare endpoint privati gestiti.
- Sottoscrizioni registrate per il provider di risorse di rete. [Altre informazioni.](../../azure-resource-manager/management/resource-providers-and-types.md)

Seguire i passaggi elencati nella [Guida introduttiva: creare un'area di lavoro sinapsi](../quickstart-create-workspace.md) per iniziare a creare l'area di lavoro. Prima di creare l'area di lavoro, usare le informazioni riportate di seguito per aggiungere la protezione Exfiltration dati all'area di lavoro.

## <a name="add-data-exfiltration-protection-when-creating-your-workspace"></a>Aggiungere la protezione Exfiltration dati durante la creazione dell'area di lavoro
1. Nella scheda rete selezionare la casella di controllo "Abilita rete virtuale gestita".
1. Selezionare "Sì" per l'opzione "Consenti traffico dati in uscita solo a destinazioni approvate".
1. Scegliere i tenant Azure AD approvati per l'area di lavoro.
1. Esaminare la configurazione e creare l'area di lavoro.
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-creation-data-exfiltration-protection.png" alt-text="Screenshot che mostra un'area di lavoro di creazione sinapsi con ' Abilita gestione rete virtuale ' selezionata.":::

## <a name="manage-approved-azure-active-directory-tenants-for-the-workspace"></a>Gestisci tenant Azure Active Directory approvati per l'area di lavoro
1. Dal portale di Azure dell'area di lavoro passare a "Approved Azure AD Tenants". L'elenco dei tenant Azure AD approvati per l'area di lavoro verrà elencato qui. Il tenant dell'area di lavoro è incluso per impostazione predefinita e non è elencato.
1. Usare "+ Aggiungi" per includere nuovi tenant nell'elenco approvato.
1. Per rimuovere un tenant Azure AD dall'elenco approvato, selezionare il tenant e selezionare "Delete" (Elimina) e quindi "Save" (Salva).
:::image type="content" source="./media/how-to-create-a-workspace-with-data-exfiltration-protection/workspace-manage-aad-tenant-list.png" alt-text="Creare un'area di lavoro con protezione Exfiltration dati":::


## <a name="connecting-to-azure-resources-in-approved-azure-ad-tenants"></a>Connessione alle risorse di Azure in tenant Azure AD approvati

È possibile creare endpoint privati gestiti per connettersi alle risorse di Azure che si trovano in Azure AD tenant, che sono approvati per un'area di lavoro. Seguire i passaggi elencati nella Guida per la [creazione di endpoint privati gestiti](./how-to-create-managed-private-endpoints.md).

>[!IMPORTANT]
>Le risorse in tenant diversi dal tenant dell'area di lavoro non devono avere regole firewall di blocco per i pool SQL per la connessione. Le risorse all'interno della rete virtuale gestita dell'area di lavoro, ad esempio i cluster Spark, possono connettersi attraverso collegamenti privati gestiti a risorse protette da firewall.

## <a name="known-limitations"></a>Limitazioni note
Gli utenti possono fornire un file di configurazione dell'ambiente per installare i pacchetti Python da repository pubblici, ad esempio PyPI. Nelle aree di lavoro protette exfiltration dati, le connessioni ai repository in uscita sono bloccate. Di conseguenza, la libreria Python installata da repository pubblici come PyPI non è supportata. 

In alternativa, gli utenti possono caricare i pacchetti dell'area di lavoro o creare un canale privato all'interno del proprio account di Azure Data Lake Storage primario. Per altre informazioni, vedere [gestione dei pacchetti in Azure sinapsi Analytics](./spark/../../spark/apache-spark-azure-portal-add-libraries.md) 
  
## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sulla [protezione Exfiltration dati nelle aree di lavoro sinapsi](./workspace-data-exfiltration-protection.md)

Altre informazioni sulla [rete virtuale dell'area di lavoro gestita](./synapse-workspace-managed-vnet.md)

Altre informazioni sugli [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md)

[Creare endpoint privati gestiti per le origini dati](./how-to-create-managed-private-endpoints.md)
