---
title: Argomento abilitare l'identità gestita in un sistema di griglia di eventi di Azure
description: Questo articolo descrive come abilitare l'identità del servizio gestito per un argomento di sistema di griglia di eventi di Azure.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 66b418787e5570dc5da06a5332dd834ccbfd4aef
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630522"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Assegnare un'identità gestita dal sistema a un argomento di sistema di griglia di eventi
Questo articolo illustra come abilitare l'identità gestita dal sistema per un argomento esistente del sistema di griglia di eventi. Per informazioni sulle identità gestite, vedere [che cosa sono le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md).  

> [!IMPORTANT]
> Attualmente, non è possibile abilitare un'identità gestita dal sistema durante la creazione di un nuovo argomento di sistema, ovvero quando si crea una sottoscrizione di eventi in una risorsa di Azure che supporta gli argomenti di sistema. 


## <a name="use-azure-portal"></a>Usare il portale di Azure
La procedura seguente illustra come abilitare l'identità gestita dal sistema per un argomento di sistema. 

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Cercare gli **argomenti di sistema di griglia di eventi** nella barra di ricerca nella parte superiore.
3. Selezionare l' **argomento di sistema** per il quale si desidera abilitare l'identità gestita. 
4. Nel menu a sinistra selezionare **Identity (identità** ). Questa opzione non viene visualizzata per un argomento di sistema presente nel percorso globale. 
5. Attivare l'opzione per **abilitare l'identità** . 
1. Selezionare **Salva** sulla barra degli strumenti per salvare l'impostazione. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="Pagina identità per un argomento di sistema"::: 
1. Selezionare **Sì** nel messaggio di conferma. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Assegnare l'identità a un argomento di sistema-conferma"::: 
1. Verificare di visualizzare l'ID oggetto dell'identità gestita assegnata dal sistema e visualizzare un collegamento per assegnare i ruoli. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Assegnare l'identità a un argomento di sistema-completato"::: 

## <a name="global-azure-sources"></a>Origini globali di Azure
È possibile abilitare l'identità gestita dal sistema solo per le risorse di Azure a livello di area. Non è possibile abilitarla per gli argomenti di sistema associati a risorse di Azure globali, ad esempio sottoscrizioni di Azure, gruppi di risorse o mappe di Azure. Gli argomenti di sistema per queste origini globali non sono associati anche a un'area specifica. Non viene visualizzata la pagina **identità** per l'argomento di sistema il cui percorso è impostato su **globale**. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="Argomento di sistema con percorso impostato su globale"::: 



## <a name="next-steps"></a>Passaggi successivi
Aggiungere l'identità a un ruolo appropriato, ad esempio il mittente dei dati del bus di servizio, nella destinazione, ad esempio una coda del bus di servizio. Per i passaggi dettagliati, vedere [aggiungere identità ai ruoli di Azure nelle destinazioni](add-identity-roles.md). 