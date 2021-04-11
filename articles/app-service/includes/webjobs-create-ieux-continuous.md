---
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: include
ms.date: 10/16/2018
ms.title: include
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 33dc766643355a5f5ebb6138e000595fd1bfe6fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101745662"
---
## <a name="create-a-continuous-webjob"></a><a name="CreateContinuous"></a> Creare un processo Web continuo

1. Si è arrivati al [portale di Azure](https://portal.azure.com).
1. Vai al **servizio app** del tuo <abbr title="La risorsa dell'app può essere un'app Web, un'app per le API o un'app per dispositivi mobili.">Risorsa app</abbr>.
1. Selezionare **Processi Web**.

   ![Selezionare Processi Web](../media/web-sites-create-web-jobs/select-webjobs.png)

1. Nella pagina **Processi Web** selezionare **Aggiungi**.

    ![Pagina Processi Web](../media/web-sites-create-web-jobs/wjblade.png)

1. Usare le impostazioni di **Aggiungi processo Web** specificate nella tabella.

   ![Screenshot che mostra le impostazioni di aggiunta di processo Web che è necessario configurare.](../media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Impostazione      | Valore di esempio   | 
   | ------------ | ----------------- | 
   | <abbr title="Nome univoco nell'app del servizio app. Deve iniziare con una lettera o un numero e non può contenere caratteri speciali diversi da `-` e `_` .">Nome</abbr> | myContinuousWebJob | 
   | <abbr title=" File *ZIP* che contiene il file eseguibile o il file di script e gli eventuali file di supporto necessari per eseguire il programma o lo script.">Caricamento file</abbr> | ConsoleApp.zip |
   | <abbr title="I tipi includono Continuous, attivato.">Tipo</abbr> | Continua | 
   | <abbr title="Disponibile solo per i processi Web continui. Determina se il programma o lo script viene eseguito in tutte le istanze o in una sola istanza. L'opzione per l'esecuzione in più istanze non si applica ai piani tariffari Gratuito o Condiviso.">Scalabilità</abbr> | Istanze multiple | 

1. Fare clic su **OK**.

    Il nuovo processo Web viene visualizzato nella pagina **Processi Web**.

    ![Elenco dei processi Web](../media/web-sites-create-web-jobs/listallwebjobs.png)

1. **Per arrestare o riavviare** un processo Web continuo, fare clic con il pulsante destro del mouse sul processo Web nell'elenco e fare clic su **Arresta** o **Avvia**.

   ![Arrestare un processo Web continuo](../media/web-sites-create-web-jobs/continuousstop.png)
