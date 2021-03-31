---
title: Distribuire un servizio cloud di Azure (supporto esteso)-portale di Azure
description: Distribuire un servizio cloud di Azure (supporto esteso) usando il portale di Azure
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 9c1abe1323bc095fe4dfbfc559ef7e159d1f7532
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98880676"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Distribuire un servizio cloud di Azure (supporto esteso) usando il portale di Azure
Questo articolo illustra come usare la portale di Azure per creare una distribuzione del servizio cloud (supporto esteso). 

> [!IMPORTANT]
> Servizi cloud (supporto esteso) è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Prima di iniziare

Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso) e creare le risorse associate. 

## <a name="deploy-a-cloud-services-extended-support"></a>Distribuire un servizio cloud (supporto esteso) 
1. Accedere al [portale di Azure](https://portal.azure.com)

2.  Usando la barra di ricerca che si trova nella parte superiore della portale di Azure, cercare e selezionare **servizi cloud (supporto esteso)**.

    :::image type="content" source="media/deploy-portal-1.png" alt-text="Immagine Visualizza il pannello tutte le risorse nella portale di Azure.":::
 
3.  Nel riquadro servizi cloud (supporto esteso) selezionare **Crea**. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="Immagine che mostra l'acquisto di un servizio cloud dal Marketplace.":::

4. La finestra di creazione dei servizi cloud (supporto esteso) viene visualizzata nella scheda **nozioni di base** . 
    - Selezionare una sottoscrizione.
    - Scegliere un gruppo di risorse oppure crearne uno nuovo.
    - Immettere il nome desiderato per la distribuzione del servizio cloud (supporto esteso).
        - Il nome DNS del servizio cloud è separato e specificato dall'etichetta del nome DNS dell'indirizzo IP pubblico e può essere modificato nella sezione IP pubblico nella scheda configurazione.
    -  Selezionare l'area in cui eseguire la distribuzione.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="Immagine Visualizza il pannello Home Servizi cloud (supporto esteso).":::

5. Aggiungere i file di configurazione, di pacchetto e di definizione del servizio cloud. È possibile aggiungere file esistenti dall'archiviazione BLOB o caricarli dal computer locale. Se si carica dal computer locale, questi verranno archiviati in un account di archiviazione. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="Image Mostra la sezione upload della scheda nozioni di base durante la creazione.":::

6. Una volta completati tutti i campi, spostare e completare la scheda **configurazione** . 
    - Selezionare una rete virtuale da associare al servizio cloud o crearne una nuova. 
        - Le distribuzioni del servizio cloud (supporto esteso) **devono** trovarsi in una rete virtuale. Nella sezione è **necessario** fare riferimento anche alla rete virtuale nel file di configurazione del servizio (con estensione cscfg) `NetworkConfiguration` .
    - Selezionare un indirizzo IP pubblico esistente da associare al servizio cloud o crearne uno nuovo.
        - Se nel file di definizione del servizio (con estensione csdef) sono definiti **endpoint di input IP** , è necessario creare un indirizzo IP pubblico per il servizio cloud. 
        - Servizi cloud (supporto esteso) supporta solo lo SKU di indirizzi IP di base.
        - Se la configurazione del servizio (. cscfg) contiene un indirizzo IP riservato, il tipo di allocazione per l'IP pubblico deve essere impostato su TP **static**. 
        - Facoltativamente, assegnare un nome DNS per l'endpoint del servizio cloud aggiornando la proprietà etichetta DNS dell'indirizzo IP pubblico associato al servizio cloud.  
    - Opzionale Avviare il servizio cloud. Scegliere Avvia o non avviare il servizio immediatamente dopo la creazione.
    - Selezionare un Key Vault 
        - Key Vault è necessario quando si specifica uno o più certificati nel file di configurazione del servizio (con estensione cscfg). Quando si seleziona un insieme di credenziali delle chiavi, si tenterà di trovare i certificati selezionati dal file di configurazione del servizio (con estensione cscfg) in base alle relative identificazioni personali. Se nell'insieme di credenziali delle chiavi mancano certificati, è possibile caricarli adesso e fare clic su **Aggiorna**.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="Immagine mostra il pannello configurazione nel portale di Azure durante la creazione di un servizio cloud (supporto esteso).":::

7. Una volta completati tutti i campi, passare alla scheda **revisione e creazione** per convalidare la configurazione della distribuzione e creare il servizio cloud (supporto esteso).

## <a name="next-steps"></a>Passaggi successivi 
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
- Visitare il [repository di esempi di servizi cloud (supporto esteso)](https://github.com/Azure-Samples/cloud-services-extended-support)