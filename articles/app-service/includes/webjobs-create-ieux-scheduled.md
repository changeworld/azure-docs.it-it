---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5105df5793d37b166b017585a62c962933a0b019
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081111"
---
## <a name="create-a-scheduled-webjob"></a><a name="CreateScheduledCRON"></a> Creare un processo Web pianificato


1. Si è arrivati al [portale di Azure](https://portal.azure.com).
1. Vai al **servizio app** del tuo <abbr title="La risorsa dell'app può essere un'app Web, un'app per le API o un'app per dispositivi mobili.">Risorsa app</abbr>.
1. Selezionare **Processi Web**.

   ![Selezionare Processi Web](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Nella pagina **Processi Web** selezionare **Aggiungi**.

    ![Pagina Processi Web](../media/web-sites-create-web-jobs/wjblade.png)

1. Usare le impostazioni di **Aggiungi processo Web** specificate nella tabella.

    ![Pagina Aggiungi processo Web](../media/web-sites-create-web-jobs/addwjscheduled.png)
    
    | Impostazione      | Valore di esempio   |
    | ------------ | ----------------- | 
    | <abbr title="Nome univoco nell'app del servizio app. Deve iniziare con una lettera o un numero e non può contenere caratteri speciali diversi da `-` e `_` .">Nome</a> | myScheduledWebJob | 
    | <abbr title="File *ZIP* che contiene il file eseguibile o il file di script e gli eventuali file di supporto necessari per eseguire il programma o lo script.">Caricamento file</abbr> | ConsoleApp.zip |
    | <abbr title="I tipi includono Continuous, attivato.">Tipo</abbr> | Attivato |
    | <abbr title="Per il corretto funzionamento della pianificazione, abilitare la funzionalità Sempre online. Sempre online è disponibile solo nei piani tariffari Basic, Standard e Premium.">Trigger</a> | Scheduled |
    | Espressione CRON</a> | 0 0/20 * * * * | 
    
    <br>
    
    <details>
     <summary>Altre informazioni sulle espressioni CRON</summary>
     <a name="#ncrontab-expressions"></a>
    
     È possibile immettere un' [espressione NCRONTAB](../../azure-functions/functions-bindings-timer.md#ncrontab-expressions) nel portale o includere un `settings.job` file nella radice del file processo Web *. zip* , come nell'esempio seguente:
     
     ```json
     {
         "schedule": "0 */15 * * * *"
     }
     ```
     
     Per altre informazioni, vedere [pianificazione di un processo Web attivato](../webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob).
     
     [!INCLUDE [webjobs-cron-timezone-note](../../../includes/webjobs-cron-timezone-note.md)]
     </details>
     <br>

1. Fare clic su **OK**.

    Il nuovo processo Web viene visualizzato nella pagina **Processi Web**.
    
    ![Elenco dei processi Web](../media/web-sites-create-web-jobs/listallwebjobs.png)
