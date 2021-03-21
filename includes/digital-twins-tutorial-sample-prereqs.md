---
author: baanders
description: file di inclusione per le esercitazioni di Gemelli digitali di Azure - prerequisiti per il progetto di esempio
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 43cc3dfc5b425df6d9dd5e2c2f35a792907ccdea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622204"
---
## <a name="prerequisites"></a>Prerequisiti

Per completare i passaggi di questa esercitazione, è necessario prima completare i prerequisiti seguenti. 

Se non si ha una sottoscrizione di Azure, **creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** prima di iniziare.

### <a name="get-required-resources"></a>Ottenere le risorse necessarie

Per completare questa esercitazione, **installare [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/), versione 16,5 o successiva** nel computer di sviluppo. Se è già installata una versione precedente, è possibile aprire l'app *Programma di installazione di Visual Studio* nel computer e seguire le istruzioni per aggiornare l'installazione.

>[!NOTE]
> Assicurarsi che l'installazione di Visual Studio 2019 includa il **[carico di lavoro sviluppo di Azure](/dotnet/azure/configure-visual-studio)**. Questo carico di lavoro consente a un'applicazione di pubblicare funzioni di Azure ed eseguire altre attività di sviluppo di Azure.

L'esercitazione si basa su un progetto di esempio scritto in C#. L'esempio è disponibile qui: [Esempi end-to-end di Gemelli digitali di Azure](/samples/azure-samples/digital-twins-samples/digital-twins-samples). **Ottenere il progetto di esempio** nel computer passando al collegamento di esempio e selezionando il pulsante *Sfoglia codice* sotto il titolo. In questo modo si passerà al repository GitHub per gli esempi, che è possibile scaricare come *. ZIP* selezionando il pulsante *codice* e *Scarica zip*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Visualizzazione del repository Digital-Twins-Samples in GitHub. Il pulsante codice è selezionato, producendo una piccola finestra di dialogo in cui è evidenziato il pulsante Scarica ZIP." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Verrà scaricato un *.* Eseguire la cartella zip nel computer come **digital-twins-samples-master.zip**. Decomprimere la cartella ed estrarre i file.

### <a name="prepare-an-azure-digital-twins-instance"></a>Preparare un'istanza di Gemelli digitali di Azure

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
