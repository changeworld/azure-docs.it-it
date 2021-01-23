---
title: Configurare la scalabilità per i servizi cloud di Azure (supporto esteso)
description: Come abilitare le opzioni di scalabilità per i servizi cloud di Azure (supporto esteso)
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 482c11395ff5dbbf2dd5bb8100451c3442f2f333
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98744516"
---
# <a name="configure-scaling-options-with-azure-cloud-services-extended-support"></a>Configurare le opzioni di scalabilità con i servizi cloud di Azure (supporto esteso) 

È possibile configurare le condizioni per abilitare le distribuzioni di servizi cloud (supporto esteso) per la scalabilità orizzontale e orizzontale. Queste condizioni possono essere basate sull'utilizzo della CPU, sul carico del disco e sul carico di rete. 

Quando si configura la scalabilità delle distribuzioni di servizi cloud, tenere presenti le seguenti informazioni:
- La scalabilità influisca sull'utilizzo di base. Le istanze del ruolo più grandi utilizzano più core ed è possibile scalare solo entro il limite di core della sottoscrizione. Per altre informazioni, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).
- La scalabilità basata sulla soglia di messaggistica della coda è supportata. Per altre informazioni, vedere [Introduzione all'archiviazione code di Azure](https://docs.microsoft.com/azure/storage/queues/storage-dotnet-how-to-use-queues).
- Per garantire la disponibilità elevata delle applicazioni del servizio cloud (supporto esteso), assicurarsi di eseguire la distribuzione con due o più istanze del ruolo.
- La scalabilità automatica personalizzata può verificarsi solo quando tutti i ruoli sono in uno stato **pronto** .

## <a name="configure-and-manage-scaling"></a>Configurare e gestire il ridimensionamento

1. Accedere al [portale di Azure](https://portal.azure.com). 
2. Selezionare la distribuzione del servizio cloud (supporto esteso) in cui si vuole configurare la scalabilità. 
3. Selezionare il pannello **scala** . 

    :::image type="content" source="media/enable-scaling-1.png" alt-text="Immagine mostra la selezione dell'opzione Desktop remoto nell'portale di Azure":::

4. In una pagina verrà visualizzato un elenco di tutti i ruoli in cui è possibile configurare il ridimensionamento. Selezionare il ruolo che si desidera configurare. 
5. Selezionare il tipo di scala da configurare
    - Con la **scalabilità manuale** viene impostato il numero assoluto di istanze.
        1. Selezionare **Scala manuale**.
        2. Immettere il numero di istanze a cui si desidera applicare la scalabilità verticale o orizzontale.
        3. Selezionare **Salva**.

        :::image type="content" source="media/enable-scaling-2.png" alt-text="Immagine che mostra la configurazione della scalabilità manuale nel portale di Azure":::

        4. L'operazione di ridimensionamento inizierà immediatamente. 
        
    - La **scalabilità automatica personalizzata** consente di impostare le regole che determinano la quantità di scalabilità o il livello di scalabilità. 
        1. Selezionare la **scalabilità automatica personalizzata**
        2. Scegliere la scalabilità in base a una metrica o a un numero di istanze.

        :::image type="content" source="media/enable-scaling-3.png" alt-text="Immagine mostra la configurazione della scalabilità automatica personalizzata nel portale di Azure":::

        3. Se si ridimensiona in base a una metrica, aggiungere le regole necessarie per ottenere i risultati di scalabilità desiderati.

        :::image type="content" source="media/enable-scaling-4.png" alt-text="Immagine mostra la configurazione di regole di scalabilità automatica personalizzate nell'portale di Azure":::

        4. Selezionare **Salva**.
        5. Le operazioni di ridimensionamento inizieranno non appena viene attivata una regola.
        
6. È possibile visualizzare o modificare le regole di scalabilità esistenti applicate alle distribuzioni selezionando la scheda **scala** .

    :::image type="content" source="media/enable-scaling-5.png" alt-text="Immagine che Mostra come modificare una regola di scalabilità esistente nell'portale di Azure":::

## <a name="next-steps"></a>Passaggi successivi 
- Esaminare i [prerequisiti di distribuzione](deploy-prerequisite.md) per i servizi cloud (supporto esteso).
- Esaminare le [domande frequenti](faq.md) per i servizi cloud (supporto esteso).
- Distribuire un servizio cloud (supporto esteso) usando il [portale di Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), il [modello](deploy-template.md) o [Visual Studio](deploy-visual-studio.md).
