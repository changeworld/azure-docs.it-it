---
title: Come configurare un endpoint privato-QnA Maker
description: Comprendere la creazione di endpoint privati disponibile in QnA Maker gestito.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 01/12/2021
ms.openlocfilehash: 7907c81e45680de49f6653891fb4204a59db1002
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710550"
---
# <a name="private-endpoints"></a>Endpoint privati

L'endpoint privato di Azure è un'interfaccia di rete che connette privatamente e in modo sicuro a un servizio basato su collegamento privato di Azure. QnA Maker offre ora il supporto per la creazione di endpoint privati per la servizio di ricerca di Azure. Questa funzionalità è disponibile in QnA Maker gestito. 

Gli endpoint privati vengono forniti dal [collegamento privato di Azure](../../private-link/private-link-overview.md)come servizio separato. Per ulteriori informazioni sui costi, vedere la [pagina relativa ai prezzi.](https://azure.microsoft.com/pricing/details/private-link/) 

## <a name="prerequisites"></a>Prerequisiti
> [!div class="checklist"]
> * Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/cognitive-services/) prima di iniziare.
> * Risorsa QnA Maker [gestita](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) creata nel portale di Azure. Prendere nota dei valori di ID di Azure Active Directory, sottoscrizione e nome della risorsa di QnA selezionati durante la creazione della risorsa.

## <a name="steps-to-enable-private-endpoint"></a>Passaggi per abilitare l'endpoint privato
1. Assegnare il ruolo di *collaboratore* a QnA Maker servizio gestito nell'istanza di Azure servizio di ricerca. Questa operazione richiede l'accesso del *proprietario* alla sottoscrizione. Passare alla scheda identità della risorsa del servizio per ottenere l'identità.
![Identità del servizio gestito](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-identity.png)

2. Per aggiungere l'identità precedente come *Contributor* , passare a Azure servizio di ricerca scheda IAM. ![ IAM servizio gestito](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-access-control.png)

3. Fare clic su *Aggiungi assegnazioni di ruolo*, aggiungere l'identità e fare clic su *Salva*.
![Assegnazione di ruolo gestita](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-role-assignment.png)

4. Passare quindi alla scheda *rete* nell'istanza di Azure servizio di ricerca e passare i dati di connettività endpoint da *pubblico* a *privato*. Questa operazione è un processo a esecuzione prolungata e può richiedere fino a 30 minuti per il completamento. 
![Rete gestita di ricerca di Azure](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking.png)

5. Passare alla scheda *rete* del servizio gestito QnA Maker e in *Consenti accesso da* selezionare l'opzione *reti selezionate ed endpoint privati* , quindi fare clic su *Salva*. 
![Newtorking QnA Maker gestito](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-2.png)

Verrà stabilita una connessione all'endpoint privato tra il servizio QnA Maker e l'istanza di servizio ricerca cognitiva di Azure. È possibile verificare la connessione all'endpoint privato nella scheda *rete* dell'istanza del servizio ricerca di Azure. Una volta completata l'intera operazione, è possibile usare il servizio QnA Maker. 
![Servizio di rete gestito](../QnAMaker/media/qnamaker-reference-private-endpoints/private-endpoint-networking-3.png)


## <a name="support-details"></a>Dettagli sul supporto
 * Non è supportata la modifica di Azure servizio di ricerca dopo aver abilitato l'accesso privato al servizio QnAMaker. Se si modifica il servizio di ricerca di Azure tramite la scheda ' configurazione ' dopo aver abilitato l'accesso privato, il servizio QnAMaker diventerà inutilizzabile.
 * Dopo aver stabilito la connessione all'endpoint privato, se si passa alla rete di Azure servizio di ricerca a "public", non sarà possibile usare il servizio QnAMaker. La rete di Azure servizio di ricerca deve essere ' privata ' per il funzionamento della connessione all'endpoint privato