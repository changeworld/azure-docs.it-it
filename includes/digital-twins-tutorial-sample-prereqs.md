---
author: baanders
description: file di inclusione per le esercitazioni di Gemelli digitali di Azure - prerequisiti per il progetto di esempio
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 5a1baf9631f2d30dd14ff16d2d34beda04605c6c
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660522"
---
## <a name="prerequisites"></a>Prerequisiti

Se non si ha una sottoscrizione di Azure, **creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** prima di iniziare.

Prima di iniziare, inoltre, **installare [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) versione 16.5 o successiva** nel computer di sviluppo. Se è già installata una versione precedente, è possibile aprire l'app *Programma di installazione di Visual Studio* nel computer e seguire le istruzioni per aggiornare l'installazione.

L'esercitazione si basa su un progetto di esempio scritto in C#. L'esempio è disponibile qui: [Esempi end-to-end di Gemelli digitali di Azure](/samples/azure-samples/digital-twins-samples/digital-twins-samples). **Ottenere il progetto di esempio** nel computer passando al collegamento di esempio e selezionando il pulsante *Sfoglia codice* sotto il titolo. In questo modo si passerà al repository GitHub per gli esempi, che è possibile scaricare come *. ZIP* selezionando il pulsante *codice* e *Scarica zip*.

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="Visualizzazione del repository Digital-Twins-Samples in GitHub. Il pulsante codice è selezionato, producendo una piccola finestra di dialogo in cui è evidenziato il pulsante Scarica ZIP." lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

Verrà scaricato un *.* Eseguire la cartella zip nel computer come **digital-twins-samples-master.zip**. Decomprimere la cartella ed estrarre i file.

### <a name="prepare-an-azure-digital-twins-instance"></a>Preparare un'istanza di Gemelli digitali di Azure

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
