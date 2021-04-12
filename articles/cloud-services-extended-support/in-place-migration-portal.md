---
title: Come eseguire la migrazione-portale
description: Come eseguire la migrazione ai servizi cloud (supporto esteso) usando il portale di Azure
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 79889b08baa80dc67b30ae445004e37d9f9fe295
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286889"
---
# <a name="migrate-to-cloud-services-extended-support-using-the-azure-portal"></a>Eseguire la migrazione ai servizi cloud (supporto esteso) usando il portale di Azure

Questo articolo illustra come usare la portale di Azure per eseguire la migrazione da [servizi cloud (versione classica)](../cloud-services/cloud-services-choose-me.md) a [servizi cloud (supporto esteso)](overview.md).

> [!IMPORTANT]
> La migrazione da servizi cloud (versione classica) a servizi cloud (supporto esteso) con lo strumento di migrazione è attualmente disponibile in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Prima di iniziare

**Assicurarsi di essere un amministratore della sottoscrizione.**

Per eseguire questa migrazione, è necessario essere aggiunti come coamministratore per la sottoscrizione nel [portale di Azure](https://portal.azure.com).

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu **Hub** selezionare **sottoscrizione**. Se questa voce non viene visualizzata, selezionare **Tutti i servizi**.
3. Individuare la voce della sottoscrizione appropriata e quindi esaminare il campo **ruolo personale** . Per un coamministratore, il valore deve essere *amministratore account*.

Se non si è in grado di aggiungere un coamministratore, contattare un amministratore del servizio o un [coamministratore](../role-based-access-control/classic-administrators.md) per la sottoscrizione per essere aggiunti.

**Iscriversi al provider di risorse per la migrazione**

1. Eseguire la registrazione con il provider di risorse `Microsoft.ClassicInfrastructureMigrate` di migrazione e la funzionalità di anteprima nello `Cloud Services` spazio dei nomi Microsoft. Compute usando il [portale di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider-1).  
1. Attendere cinque minuti per il completamento della registrazione, quindi controllare lo stato dell'approvazione. 

## <a name="migrate-your-cloud-service-resources"></a>Eseguire la migrazione delle risorse dei servizi cloud

1. Andare al pannello del [portale di servizi cloud (classico)](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.classicCompute%2FdomainNames). 
2. Selezionare il servizio cloud di cui si vuole eseguire la migrazione.
3. Selezionare il pannello **Esegui la migrazione al pannello ARM** .

    > [!NOTE]
    > Se si esegue la migrazione di un servizio cloud (classico) che si trova all'interno di una rete virtuale (classica), verrà visualizzato un messaggio di intestazione che richiede di spostare il pannello rete virtuale (classica).
    > Si verrà portati al pannello rete virtuale (classica) per completare la migrazione delle distribuzioni della rete virtuale (classica) e dei servizi cloud (versione classica) al suo interno.
    > :::image type="content" source="media/in-place-migration-portal-2.png" alt-text="Image Mostra lo stato di una rete virtuale classica nel portale di Azure.":::
 

4. Convalidare la migrazione. 

    Se la convalida ha esito positivo, tutte le distribuzioni sono supportate e pronte per essere preparate.  

    :::image type="content" source="media/in-place-migration-portal-1.png" alt-text="Image Mostra il pannello migrate to ARM nel portale di Azure.":::

    Se la convalida ha esito negativo, verrà visualizzato un elenco di scenari non supportati che devono essere corretti prima che la migrazione possa continuare. 

    :::image type="content" source="media/in-place-migration-portal-3.png" alt-text="Image Mostra un errore di convalida nel portale di Azure.":::

5. Preparare la migrazione.

    Se la preparazione ha esito positivo, la migrazione è pronta per il commit.
    
    :::image type="content" source="media/in-place-migration-portal-4.png" alt-text="Image Mostra il passaggio della convalida nel portale di Azure.":::

    Se la preparazione ha esito negativo, esaminare l'errore, risolvere eventuali problemi e ripetere la preparazione. 

    :::image type="content" source="media/in-place-migration-portal-5.png" alt-text="L'immagine mostra un errore di convalida non riuscita.":::

      Al termine della preparazione, tutti i servizi cloud in una rete virtuale sono disponibili per le operazioni di lettura usando i servizi cloud (versione classica) e i servizi cloud (supporto esteso) portale di Azure Blade. È ora possibile testare la distribuzione del servizio cloud (supporto esteso) per garantire il corretto funzionamento prima di completare la migrazione. 
 
    :::image type="content" source="media/in-place-migration-portal-6.png" alt-text="Image Mostra le API di test nel pannello del portale.":::

6.  **(Facoltativo)** Interrompi migrazione. 
    
    Se si sceglie di interrompere la migrazione, usare il pulsante **Interrompi** per eseguire il rollback dei passaggi precedenti. La distribuzione di servizi cloud (versione classica) viene quindi sbloccata per tutte le operazioni CRUD.

    :::image type="content" source="media/in-place-migration-portal-7.png" alt-text="Immagine che mostra il passaggio di convalida.":::

    Se Interrompi ha esito negativo, selezionare **Riprova Interrompi**. Un nuovo tentativo dovrebbe risolvere il problema. In caso contrario, contattare il supporto tecnico. 
 
    :::image type="content" source="media/in-place-migration-portal-8.png" alt-text="Image Mostra un messaggio di errore di convalida non riuscita.":::

7.  Eseguire il commit della migrazione.

    >[!IMPORTANT]
    > Quando si esegue il commit della migrazione, non è possibile eseguire il rollback. 
    
    Digitare "Yes" (Sì) per confermare ed eseguire il commit della migrazione. La migrazione è ora completata. La distribuzione dei servizi cloud di cui è stata eseguita la migrazione (supporto esteso) è sbloccata per tutte le operazioni. 

## <a name="next-steps"></a>Passaggi successivi
Esaminare la sezione [post-migrazione modifiche](in-place-migration-overview.md#post-migration-changes) per visualizzare le modifiche apportate ai file di distribuzione, all'automazione e ad altri attributi della distribuzione del nuovo servizio cloud (supporto esteso). 
