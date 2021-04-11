---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 40e9e2b0e7437566a132f2402d3bed01b284d11a
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081011"
---
## <a name="create-a-manually-triggered-webjob"></a><a name="CreateOnDemand"></a> Creare un processo Web attivato manualmente

1. Si è arrivati al [portale di Azure](https://portal.azure.com).
1. Vai al **servizio app** del tuo <abbr title="La risorsa dell'app può essere un'app Web, un'app per le API o un'app per dispositivi mobili.">Risorsa app</abbr>.
1. Selezionare **Processi Web**.

    ![Selezionare Processi Web](../media/web-sites-create-web-jobs/select-webjobs.png)

2. Nella pagina **Processi Web** selezionare **Aggiungi**.

   ![Pagina Processi Web](../media/web-sites-create-web-jobs/wjblade.png)

3. Usare le impostazioni di **Aggiungi processo Web** specificate nella tabella.

    ![Screenshot che mostra le impostazioni che devono essere impostate per la creazione di un processo Web attivato manualmente.](../media/web-sites-create-web-jobs/addwjtriggered.png)
    
    | Impostazione      | Valore di esempio   | 
    | ------------ | ----------------- | 
   | <abbr title="Nome univoco nell'app del servizio app. Deve iniziare con una lettera o un numero e non può contenere caratteri speciali diversi da `-` e `_` .">Nome</abbr> | myTriggeredWebJob | 
    | <abbr title="File *ZIP* che contiene il file eseguibile o il file di script e gli eventuali file di supporto necessari per eseguire il programma o lo script.">Caricamento file</abbr> | ConsoleApp.zip |
    | <abbr title="I tipi includono Continuous, attivato.">Tipo</abbr> | Attivato | 
    | <abbr title="I tipi includono scheduled o Manual">Trigger</a> | Manuale | 

4. Fare clic su **OK**. 

   Il nuovo processo Web viene visualizzato nella pagina **Processi Web**.

   ![Elenco dei processi Web](../media/web-sites-create-web-jobs/listallwebjobs.png)

7. **Per eseguire un processo Web manuale**, fare clic con il pulsante destro del mouse sul relativo nome nell'elenco e scegliere **Esegui**.
   
    ![Eseguire un processo Web](../media/web-sites-create-web-jobs/runondemand.png)

