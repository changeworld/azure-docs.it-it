---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: edcbeb15b58d59bf080a1acf1d54f1b60e109c8a
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425741"
---
Passare al portale di Azure e <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title="creare una nuova risorsa di riconoscimento modulo" target="_blank">creare una nuova risorsa di riconoscimento modulo </a>. Nel riquadro **Crea** specificare le informazioni seguenti:

|    |    |
|--|--|
| **Nome** | Nome per la risorsa. È consigliabile usare un nome descrittivo, ad esempio *MyNameFormRecognizer*. |
| **Sottoscrizione** | Selezionare la sottoscrizione di Azure a cui è stato concesso l'accesso. |
| **Posizione** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. |
| **Piano tariffario** | Il costo della risorsa varia a seconda del piano tariffario selezionato e dell'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.
| **Gruppo di risorse** | [Gruppo di risorse di Azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group) che conterrà la risorsa. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |

> [!NOTE]
> In genere, quando si crea una risorsa di Servizi cognitivi nel portale di Azure, si ha la possibilità di creare una chiave di sottoscrizione multiservizio (usata per più servizi cognitivi) o una chiave di sottoscrizione per singolo servizio (usata solo con un determinato servizio cognitivo). Tuttavia, il servizio di riconoscimento modulo non è attualmente incluso nella sottoscrizione multiservizio.

Quando la distribuzione della risorsa di riconoscimento modulo è completata, individuarla e selezionarla dall'elenco **Tutte le risorse** nel portale. La chiave e l'endpoint saranno disponibili nella pagina relativa della risorsa in Gestione risorse. Salvarli entrambi in un percorso temporaneo prima di procedere.