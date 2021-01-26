---
title: Eseguire la migrazione a una chiave di creazione delle risorse di Azure
titleSuffix: Azure Cognitive Services
description: Questo articolo descrive come eseguire la migrazione di Language Understanding (LUIS) la creazione dell'autenticazione da un account di posta elettronica a una risorsa di Azure.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.custom: seodec18, contperf-fy21q2
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/14/2020
ms.openlocfilehash: 3ff48ff5a3f46d8ec0fbf81b4cd20d20c217344b
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98787638"
---
# <a name="migrate-to-an-azure-resource-authoring-key"></a>Eseguire la migrazione a una chiave di creazione delle risorse di Azure

> [!IMPORTANT]
>  A partire dal 3 dicembre, gli utenti LUIS esistenti devono completare il processo di migrazione per continuare a creare applicazioni LUIS.

La creazione dell'autenticazione di Language Understanding (LUIS) è cambiata da un account di posta elettronica a una risorsa di Azure. Usare questo articolo per informazioni su come eseguire la migrazione dell'account, se non è ancora stata eseguita la migrazione.  


## <a name="what-is-migration"></a>Che cos'è la migrazione?

La migrazione è il processo di modifica dell'autenticazione di creazione da un account di posta elettronica a una risorsa di Azure. L'account verrà collegato a una sottoscrizione di Azure e a una risorsa di creazione di Azure dopo la migrazione.

La migrazione deve essere eseguita dal [portale Luis](https://www.luis.ai). Se si creano le chiavi di creazione usando l'interfaccia della riga di comando di LUIS, ad esempio, sarà necessario completare il processo di migrazione nel portale LUIS. È ancora possibile avere co-autori nelle applicazioni dopo la migrazione, ma questi verranno aggiunti a livello di risorsa di Azure invece che a livello di applicazione. La migrazione dell'account non può essere annullata.

> [!Note]
> * Se è necessario creare una risorsa di runtime di stima, esiste [un processo separato](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) per crearlo.
> * Per informazioni sul modo in cui le applicazioni e i collaboratori saranno interessati, vedere la sezione [Note sulla migrazione](#migration-notes) . 
> * La creazione di un'app LUIS è gratuita, come indicato dal livello F0. [Altre informazioni sui piani tariffari](luis-limits.md#key-limits).

## <a name="migration-prerequisites"></a>Prerequisiti per la migrazione

* Una sottoscrizione di Azure valida. Chiedere all'amministratore del tenant di aggiungere l'utente alla sottoscrizione o [iscriversi per una versione gratuita](https://azure.microsoft.com/free/cognitive-services).
* Una risorsa di creazione di LUIS Azure dal portale LUIS o dalla [portale di Azure](https://portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne). 
    * La creazione di una risorsa di authoring dal portale LUIS è parte del processo di migrazione descritto nella sezione successiva.
* Se si è un collaboratore sulle applicazioni, le applicazioni non verranno migrate automaticamente. Verrà richiesto di esportare queste app durante l'attraversamento del flusso di migrazione. È anche possibile usare l' [API Export](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40). È possibile importare di nuovo l'app in LUIS dopo la migrazione. Il processo di importazione crea una nuova app con un nuovo ID app, per il quale si è proprietari.        
* Se si è proprietari dell'applicazione, non è necessario esportare le app perché verranno migrate automaticamente. Viene fornito un modello di messaggio di posta elettronica con un elenco di tutti i collaboratori per ogni applicazione, in modo che possano ricevere notifiche del processo di migrazione.

## <a name="migration-steps"></a>Passaggi della migrazione

1. Quando si accede al [portale Luis](https://www.luis.ai), viene aperta una finestra di migrazione di Azure con i passaggi per la migrazione. Se lo si ignora, non sarà possibile procedere con la creazione delle applicazioni LUIS e l'unica azione visualizzata sarà continuare con la migrazione.

    > [!div class="mx-imgBorder"]
    > ![Introduzione alla finestra migrazione](./media/migrate-authoring-key/notify-azure-migration.png)

2. Se si hanno collaboratori su una qualsiasi delle app, viene visualizzato un elenco di nomi di applicazioni di proprietà dell'utente, insieme ai messaggi di posta elettronica dell'area di creazione e del collaboratore in ogni applicazione. È consigliabile inviare ai collaboratori un messaggio di posta elettronica che li informa sulla migrazione facendo clic sul pulsante **Invia** simbolo a sinistra del nome dell'applicazione.
Un `*` simbolo verrà visualizzato accanto al nome dell'applicazione se un collaboratore ha una risorsa di stima assegnata all'applicazione. Dopo la migrazione, queste app avranno ancora a disposizione queste risorse di stima anche se i collaboratori non avranno accesso per la creazione delle applicazioni. Questa assegnazione verrà tuttavia interruppe se il proprietario della risorsa di stima ha [rigenerato le chiavi](./luis-how-to-azure-subscription.md#regenerate-an-azure-key) dal portale di Azure.  

   > [!div class="mx-imgBorder"]
   > ![Notifica a collaboratori](./media/migrate-authoring-key/notify-azure-migration-collabs.png)


   Per ogni collaboratore e app, l'applicazione di posta elettronica predefinita si apre con un messaggio di posta elettronica in formato lieve. È possibile modificare il messaggio di posta elettronica prima di inviarlo. Il modello di messaggio di posta elettronica include l'ID app esatto e il nome dell'app.

   ```html
   Dear Sir/Madam,

   I will be migrating my LUIS account to Azure. Consequently, you will no longer have access to the following app:

   App Id: <app-ID-omitted>
   App name: Human Resources

   Thank you
   ```
   > [!Note]
   > Dopo aver eseguito la migrazione dell'account ad Azure, le app non saranno più disponibili per i collaboratori.

3. Se si è un collaboratore in qualsiasi app, viene visualizzato un elenco di nomi di applicazioni condivisi con l'utente, insieme all'area di creazione e ai messaggi di posta elettronica del proprietario in ogni applicazione. Si consiglia di esportare una copia delle app facendo clic sul pulsante Esporta a sinistra del nome dell'applicazione. È possibile importare di nuovo queste app dopo la migrazione, perché non verranno automaticamente migrate con l'utente.
`*`Se si dispone di una risorsa di stima assegnata a un'applicazione, verrà visualizzato un simbolo accanto al nome dell'applicazione. Dopo la migrazione, la risorsa di stima verrà ancora assegnata a queste applicazioni anche se non si avrà più accesso per creare queste app. Se si desidera interrompere l'assegnazione tra la risorsa di stima e l'applicazione, sarà necessario passare a portale di Azure e [rigenerare le chiavi](./luis-how-to-azure-subscription.md#regenerate-an-azure-key).

   > [!div class="mx-imgBorder"]
   > ![Esporta le tue applicazioni.](./media/migrate-authoring-key/migration-export-apps.png)


4. Nella finestra per la migrazione delle aree verrà richiesto di eseguire la migrazione delle applicazioni a una risorsa di Azure nella stessa area in cui sono state create. LUIS dispone di tre aree [di creazione e portali](./luis-reference-regions.md#luis-authoring-regions). Nella finestra vengono visualizzate le aree in cui sono state create le applicazioni di proprietà. Le aree di migrazione visualizzate possono variare a seconda del portale regionale usato e delle app create. 

   > [!div class="mx-imgBorder"]
   > ![Migrazione di più aree.](./media/migrate-authoring-key/migration-regional-flow.png)

5. Per ogni area, scegliere di creare una nuova risorsa LUIS authoring o di eseguire la migrazione a una nuova risorsa usando i pulsanti.

   > [!div class="mx-imgBorder"]
   > ![scegliere la creazione o la risorsa di creazione esistente](./media/migrate-authoring-key/migration-multiregional-resource.png)

   Specificare le informazioni seguenti:

   * **Nome del tenant**: il tenant a cui è associata la sottoscrizione di Azure. Per impostazione predefinita, questo valore è impostato sul tenant attualmente in uso. È possibile cambiare i tenant chiudendo questa finestra e selezionando l'avatar nella parte superiore destra della schermata, contenente le iniziali. Fare clic su **migrate to Azure** per riaprire la finestra.
   * **Nome della sottoscrizione di Azure**: la sottoscrizione che verrà associata alla risorsa. Se si dispone di più di una sottoscrizione appartenente al tenant, selezionare quella desiderata dall'elenco a discesa.
   * **Nome della risorsa di creazione**: nome personalizzato scelto. Viene usato come parte dell'URL per le query di creazione e di endpoint di stima. Se si crea una nuova risorsa di creazione, si noti che il nome della risorsa può includere solo caratteri alfanumerici, `-` e non può iniziare o terminare con `-` . Se nel nome sono inclusi altri simboli, la creazione e la migrazione delle risorse avranno esito negativo.
   * **Nome del gruppo di risorse di Azure**: nome del gruppo di risorse personalizzato scelto dall'elenco a discesa. I gruppi di risorse consentono di raggruppare le risorse di Azure per l'accesso e la gestione. Se non si dispone attualmente di un gruppo di risorse nella sottoscrizione, non sarà possibile crearne uno nel portale LUIS. Passare a [portale di Azure](https://ms.portal.azure.com/#create/Microsoft.ResourceGroup) per crearne uno, quindi passare a Luis per continuare il processo di accesso.

6. Dopo aver eseguito la migrazione in tutte le aree, fare clic su fine. A questo punto sarà possibile accedere alle applicazioni. È possibile continuare a creare e gestire tutte le applicazioni in tutte le aree all'interno del portale.

## <a name="migration-notes"></a>Note sulla migrazione

* Prima della migrazione, i coautori sono noti come _collaboratori_ a livello di app Luis. Dopo la migrazione, il ruolo di _collaboratore_ di Azure viene usato per la stessa funzionalità a livello di risorsa di Azure.
* Se è stato eseguito l'accesso a più di un [portale di Luis Regional](./luis-reference-regions.md#luis-authoring-regions), verrà richiesto di eseguire la migrazione in più aree contemporaneamente.
* Se si è il proprietario dell'applicazione, verranno automaticamente migrate le applicazioni. Se si è un collaboratore nell'applicazione, le applicazioni non verranno migrate. Tuttavia, ai collaboratori verrà richiesto di esportare le app necessarie.
* I proprietari dell'applicazione non possono scegliere un subset di app di cui eseguire la migrazione e non è possibile che un proprietario sappia se è stata eseguita la migrazione dei collaboratori.
* La migrazione non sposta o aggiunge automaticamente collaboratori alla risorsa di creazione di Azure. Il proprietario dell'app è quello che deve completare questo passaggio dopo la migrazione. Questo passaggio richiede [le autorizzazioni per la risorsa di creazione di Azure](./luis-how-to-collaborate.md).
* Dopo aver assegnato i collaboratori alla risorsa di Azure, sarà necessario eseguire la migrazione prima di poter accedere alle applicazioni. In caso contrario, non avranno accesso per la creazione delle applicazioni.


## <a name="using-apps-after-migration"></a>Uso di app dopo la migrazione

Al termine del processo di migrazione, tutte le app LUIS per cui si è proprietari saranno ora assegnate a una singola risorsa di authoring LUIS.
L'elenco **app personali** Mostra le app di cui è stata eseguita la migrazione alla nuova risorsa di creazione. Prima di accedere alle app, selezionare **scegliere una risorsa di creazione diversa** per selezionare la sottoscrizione e la risorsa di creazione per visualizzare le app che possono essere create.

> [!div class="mx-imgBorder"]
> ![Selezionare la sottoscrizione e la risorsa di creazione](./media/migrate-authoring-key/select-sub-and-resource.png)


Se si prevede di modificare le app a livello di codice, saranno necessari i valori delle chiavi di creazione. Questi valori vengono visualizzati facendo clic su **Gestisci** nella parte superiore della schermata del portale Luis e quindi selezionando **risorse di Azure**. Sono disponibili anche nella portale di Azure della pagina **chiave ed endpoint** della risorsa. È anche possibile creare altre risorse di creazione e assegnarle dalla stessa pagina.

## <a name="adding-contributors-to-authoring-resources"></a>Aggiunta di collaboratori a risorse di creazione

[!INCLUDE [Manage contributors for the Azure authoring resource for language understanding](./includes/manage-contributors-authoring-resource.md)]

Informazioni su [come aggiungere collaboratori](luis-how-to-collaborate.md) alla risorsa di creazione. I collaboratori avranno accesso a tutte le applicazioni in tale risorsa.

È possibile aggiungere collaboratori alla risorsa di creazione dalla portale di Azure nella pagina **controllo di accesso (IAM)** per la risorsa. Per altre informazioni, vedere [aggiungere collaboratori all'app](luis-how-to-collaborate.md).

> [!Note]
> Se il proprietario dell'app LUIS ha eseguito la migrazione e ha aggiunto il collaboratore come collaboratore nella risorsa di Azure, il collaboratore non potrà ancora accedere all'app, a meno che non ne venga eseguita la migrazione.

## <a name="troubleshooting-the-migration-process"></a>Risoluzione dei problemi relativi al processo di migrazione

Se non è possibile trovare la sottoscrizione di Azure nell'elenco a discesa:
* Assicurarsi di disporre di una sottoscrizione di Azure valida autorizzata a creare risorse di servizi cognitivi. Passare alla [portale di Azure](https://ms.portal.azure.com) e verificare lo stato della sottoscrizione. Se non è disponibile, [creare un account Azure gratuito](https://azure.microsoft.com/free/cognitive-services/).
* Assicurarsi di trovarsi nel tenant appropriato associato alla sottoscrizione valida. È possibile cambiare i tenant selezionando l'avatar nella parte superiore destra della schermata, contenente le iniziali.

  > [!div class="mx-imgBorder"]
  > ![Pagina per il cambio di directory](./media/migrate-authoring-key/switch-directories.png)

Se si dispone di una risorsa di creazione esistente ma non è possibile trovarla quando si seleziona l'opzione **USA risorsa di creazione esistente** :
* La risorsa è stata probabilmente creata in un'area diversa da quella in cui si sta tentando di eseguire la migrazione.
* In alternativa, creare una nuova risorsa dal portale LUIS.

Se si seleziona l'opzione **Crea nuova risorsa** di creazione e la migrazione ha esito negativo con il messaggio di errore "Impossibile recuperare le informazioni di Azure dell'utente, riprovare più tardi":
* La sottoscrizione potrebbe avere 10 o più risorse di creazione per ogni area, per sottoscrizione. In tal caso, non sarà possibile creare una nuova risorsa di creazione.
* Eseguire la migrazione selezionando l'opzione **Usa la risorsa di creazione esistente** e selezionando una delle risorse esistenti nella sottoscrizione.

## <a name="create-new-support-request"></a>Crea nuova richiesta di supporto

Se si verificano problemi con la migrazione che non sono stati risolti nella sezione risoluzione dei problemi, [creare un argomento di supporto](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) e fornire le informazioni seguenti con i campi seguenti:

   * **Tipo di problema**: tecnico
   * **Sottoscrizione**: scegliere una sottoscrizione dall'elenco a discesa
   * **Servizio**: cercare e selezionare "Servizi cognitivi"
   * **Risorsa**: scegliere una risorsa Luis se ne esiste già una. In caso contrario, selezionare domanda generale.

## <a name="next-steps"></a>Passaggi successivi

* Esaminare i [concetti relativi alla creazione e alle chiavi di runtime](luis-how-to-azure-subscription.md)
* Esaminare come [assegnare chiavi](luis-how-to-azure-subscription.md) e [aggiungere collaboratori](luis-how-to-collaborate.md)