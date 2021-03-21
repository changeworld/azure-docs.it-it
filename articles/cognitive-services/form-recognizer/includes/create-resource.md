---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: ed5f94bdc372e317e9e312db07665d233b1b4139
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103467311"
---
Passare al portale di Azure e <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="creare una nuova risorsa di riconoscimento modulo" target="_blank">creare una nuova risorsa di riconoscimento modulo </a>. Nel riquadro **Crea** specificare le informazioni seguenti:

| Dettagli del progetto   | Descrizione   |
|--|--|
| **Sottoscrizione** | Selezionare la sottoscrizione di Azure a cui è stato concesso l'accesso. |
| **Gruppo di risorse** | [Gruppo di risorse di Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) che conterrà la risorsa. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |
| **Area** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. |
| **Nome** | Nome per la risorsa. È consigliabile usare un nome descrittivo, ad esempio *MyNameFormRecognizer*. |
| **Piano tariffario** | Il costo della risorsa varia a seconda del piano tariffario selezionato e dell'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.

> [!NOTE]
> In genere, quando si crea una risorsa di Servizi cognitivi nel portale di Azure, si ha la possibilità di creare una chiave di sottoscrizione multiservizio (usata per più servizi cognitivi) o una chiave di sottoscrizione per singolo servizio (usata solo con un determinato servizio cognitivo). Tuttavia, il servizio di riconoscimento modulo non è attualmente incluso nella sottoscrizione multiservizio.

Quando la distribuzione della risorsa di riconoscimento modulo è completata, individuarla e selezionarla dall'elenco **Tutte le risorse** nel portale. La chiave e l'endpoint saranno disponibili nella pagina relativa della risorsa in Gestione risorse. Salvarli entrambi in un percorso temporaneo prima di procedere.
