---
title: "Esercitazione: Pianificare un'attività di Controllo di accesso"
description: Questa esercitazione illustra come eseguire un'attività Registro Azure Container in base a una pianificazione definita impostando uno o più trigger timer
ms.topic: article
ms.date: 11/24/2020
ms.openlocfilehash: fa80bcbd318266a86c5bec08c9ee60fc0d22a10d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780858"
---
# <a name="tutorial-run-an-acr-task-on-a-defined-schedule"></a>Esercitazione: Eseguire un'attività ACR in base a una pianificazione definita

Questa esercitazione illustra come eseguire [un'attività ACR](container-registry-tasks-overview.md) in base a una pianificazione. Pianificare un'attività configurando uno o più *trigger timer*. I trigger timer possono essere usati da soli o in combinazione con altri trigger di attività.

Questa esercitazione illustra le attività di pianificazione e:

> [!div class="checklist"]
> * Creare un'attività con un trigger timer
> * Gestire i trigger timer

La pianificazione di un'attività è utile per scenari come i seguenti:

* Eseguire un carico di lavoro del contenitore per operazioni di manutenzione pianificate. Ad esempio, eseguire un'app in contenitori per rimuovere le immagini non necessario dal registro.
* Eseguire un set di test in un'immagine di produzione durante il giorno lavorativo come parte del monitoraggio del sito live.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="about-scheduling-a-task"></a>Informazioni sulla pianificazione di un'attività

* **Trigger con espressione cron:** il trigger timer per un'attività usa *un'espressione cron*. L'espressione è una stringa con cinque campi che specificano il minuto, l'ora, il giorno, il mese e il giorno della settimana per attivare l'attività. Sono supportate frequenze fino a una volta al minuto.

  Ad esempio, l'espressione `"0 12 * * Mon-Fri"` attiva un'attività alle ore 12.00 UTC di ogni giorno della settimana. Vedere [i dettagli](#cron-expressions) più avanti in questo articolo.
* **Più trigger timer:** è consentita l'aggiunta di più timer a un'attività, purché le pianificazioni siano diverse.
    * Specificare più trigger timer quando si crea l'attività o aggiungerli in un secondo momento.
    * Facoltativamente, assegnare un nome ai trigger per semplificare la gestione oppure attività ACR fornirà nomi di trigger predefiniti.
    * Se le pianificazioni del timer si sovrappongono alla volta, ACR Tasks attiva l'attività all'ora pianificata per ogni timer.
* **Altri trigger di attività:** in un'attività attivata dal timer è anche possibile abilitare i trigger in base al [commit](container-registry-tutorial-build-task.md) del codice sorgente o agli aggiornamenti dell'immagine [di base.](container-registry-tutorial-base-image-update.md) Analogamente ad altre attività di ACR, è anche possibile [eseguire manualmente un'attività][az-acr-task-run] pianificata.

## <a name="create-a-task-with-a-timer-trigger"></a>Creare un'attività con un trigger timer

### <a name="task-command"></a>Comando dell'attività

Popolare prima di tutto la variabile di ambiente della shell seguente con un valore appropriato per l'ambiente. Questo passaggio non è obbligatorio, ma semplifica in parte l'esecuzione dei comandi su più righe dell'interfaccia della riga di comando di Azure. Se non si popola la variabile di ambiente, è necessario sostituire manualmente ogni valore ovunque venga visualizzato nei comandi di esempio.

[![Incorpora avvio](https://shell.azure.com/images/launchcloudshell.png "Avviare Azure Cloud Shell")](https://shell.azure.com)

```console
ACR_NAME=<registry-name>        # The name of your Azure container registry
```

Quando si crea un'attività con [il comando az acr task create,][az-acr-task-create] è possibile aggiungere facoltativamente un trigger timer. Aggiungere il `--schedule` parametro e passare un'espressione cron per il timer.

Come semplice esempio, l'attività seguente attiva l'esecuzione `hello-world` dell'immagine Microsoft Container Registry ogni giorno alle 21:00 UTC. L'attività viene eseguita senza un contesto del codice sorgente.

```azurecli
az acr task create \
  --name timertask \
  --registry $ACR_NAME \
  --cmd mcr.microsoft.com/hello-world \
  --schedule "0 21 * * *" \
  --context /dev/null
```

Eseguire il comando [az acr task show][az-acr-task-show] per verificare che il trigger del timer sia configurato. Per impostazione predefinita, è abilitato anche il trigger di aggiornamento dell'immagine di base.

```azurecli
az acr task show --name timertask --registry $ACR_NAME --output table
```

```output
NAME      PLATFORM    STATUS    SOURCE REPOSITORY       TRIGGERS
--------  ----------  --------  -------------------     -----------------
timertask linux       Enabled                           BASE_IMAGE, TIMER
```

## <a name="trigger-the-task"></a>Attivare l'attività

Attivare manualmente l'attività [con az acr task run][az-acr-task-run] per assicurarsi che sia configurata correttamente:

```azurecli
az acr task run --name timertask --registry $ACR_NAME
```

Se il contenitore viene eseguito correttamente, l'output è simile al seguente. L'output è ridotto per mostrare i passaggi principali

```output
Queued a run with ID: cf2a
Waiting for an agent...
2020/11/20 21:03:36 Using acb_vol_2ca23c46-a9ac-4224-b0c6-9fde44eb42d2 as the home volume
2020/11/20 21:03:36 Creating Docker network: acb_default_network, driver: 'bridge'
[...]
2020/11/20 21:03:38 Launching container with name: acb_step_0

Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

Dopo l'ora pianificata, eseguire il [comando az acr task list-runs][az-acr-task-list-runs] per verificare che il timer ha attivato l'attività come previsto:

```azurecli
az acr task list-runs --name timertask --registry $ACR_NAME --output table
```

Quando il timer ha esito positivo, l'output è simile al seguente:

```output
RUN ID    TASK       PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ---------  ----------  ---------  ---------  --------------------  ----------
ca15      timertask  linux       Succeeded  Timer      2020-11-20T21:00:23Z  00:00:06
ca14      timertask  linux       Succeeded  Manual     2020-11-20T20:53:35Z  00:00:06
```

## <a name="manage-timer-triggers"></a>Gestire i trigger timer

Usare i [comandi az acr task timer][az-acr-task-timer] per gestire i trigger timer per un'attività ACR.

### <a name="add-or-update-a-timer-trigger"></a>Aggiungere o aggiornare un trigger timer

Dopo aver creato un'attività, aggiungere facoltativamente un trigger timer usando il [comando az acr task timer add.][az-acr-task-timer-add] Nell'esempio seguente viene aggiunto un nome di trigger *timer timer2* a *timertask* creato in precedenza. Questo timer attiva l'attività ogni giorno alle 10:30 UTC.

```azurecli
az acr task timer add \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 10 * * *"
```

Aggiornare la pianificazione di un trigger esistente o modificarne lo stato usando il [comando az acr task timer update.][az-acr-task-timer-update] Ad esempio, aggiornare il trigger denominato *timer2* per attivare l'attività alle 11:30 UTC:

```azurecli
az acr task timer update \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2 \
  --schedule "30 11 * * *"
```

### <a name="list-timer-triggers"></a>Elencare i trigger timer

Il [comando az acr task timer list][az-acr-task-timer-list] mostra i trigger timer impostati per un'attività:

```azurecli
az acr task timer list --name timertask --registry $ACR_NAME
```

Output di esempio:

```JSON
[
  {
    "name": "timer2",
    "schedule": "30 11 * * *",
    "status": "Enabled"
  },
  {
    "name": "t1",
    "schedule": "0 21 * * *",
    "status": "Enabled"
  }
]
```

### <a name="remove-a-timer-trigger"></a>Rimuovere un trigger timer

Usare il [comando az acr task timer remove][az-acr-task-timer-remove] per rimuovere un trigger timer da un'attività. L'esempio seguente rimuove il trigger *timer2* da *timertask*:

```azurecli
az acr task timer remove \
  --name timertask \
  --registry $ACR_NAME \
  --timer-name timer2
```

## <a name="cron-expressions"></a>Espressioni Cron

ACR Tasks usa la [libreria NCronTab](https://github.com/atifaziz/NCrontab) per interpretare le espressioni cron. Le espressioni supportate nelle attività di ACR hanno cinque campi obbligatori separati da spazi vuoti:

`{minute} {hour} {day} {month} {day-of-week}`

Il fuso orario usato con le espressioni cron è Coordinated Universal Time (UTC). Le ore sono in formato 24 ore.

> [!NOTE]
> ACR Tasks non supporta il `{second}` campo o nelle espressioni `{year}` cron. Se si copia un'espressione cron usata in un altro sistema, assicurarsi di rimuovere tali campi, se usati.

Ogni campo può avere uno dei tipi di valori seguenti:

|Type  |Esempio  |Quando viene attivato  |
|---------|---------|---------|
|Valore specifico |<nobr>`"5 * * * *"`</nobr>|ogni ora a 5 minuti dall'ora|
|Tutti i valori (`*`)|<nobr>`"* 5 * * *"`</nobr>|ogni minuto dell'ora a partire da 5:00 UTC (60 volte al giorno)|
|Intervallo (operatore `-`)|<nobr>`"0 1-3 * * *"`</nobr>|3 volte al giorno, alle 13:00, 2:00 e 3:00 UTC|
|Set di valori (operatore `,`)|<nobr>`"20,30,40 * * * *"`</nobr>|3 volte all'ora, a 20 minuti, 30 minuti e 40 minuti dopo l'ora|
|Valore di intervallo (operatore `/`)|<nobr>`"*/10 * * * *"`</nobr>|6 volte all'ora, a 10 minuti, 20 minuti e così via, oltre l'ora

[!INCLUDE [functions-cron-expressions-months-days](../../includes/functions-cron-expressions-months-days.md)]

### <a name="cron-examples"></a>Esempi di Cron

|Esempio|Quando viene attivato  |
|---------|---------|
|`"*/5 * * * *"`|Una volta ogni cinque minuti|
|`"0 * * * *"`|Una volta all'inizio di ogni ora|
|`"0 */2 * * *"`|Una volta ogni due ore|
|`"0 9-17 * * *"`|una volta ogni ora dalle 9:00 alle 17:00 UTC|
|`"30 9 * * *"`|alle 9:30 UTC ogni giorno|
|`"30 9 * * 1-5"`|alle 9:30 UTC ogni giorno della settimana|
|`"30 9 * Jan Mon"`|alle 9:30 UTC ogni lunedì di gennaio|

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere tutte le risorse create in questa serie di esercitazioni, inclusi il registro contenitori o i registri, l'istanza del contenitore, l'insieme di credenziali delle chiavi e l'entità servizio, eseguire i comandi seguenti:

```azurecli
az group delete --resource-group $RES_GROUP
az ad sp delete --id http://$ACR_NAME-pull
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare Registro Azure Container attività attivate automaticamente da un timer. 

Per un esempio di uso di un'attività pianificata per pulire i repository in un Registro di sistema, vedere [Ripulire](container-registry-auto-purge.md)automaticamente le immagini da un Registro Azure Container.

Per esempi di attività attivate dai commit del codice sorgente o dagli aggiornamenti delle immagini di base, vedere altri articoli della serie di esercitazioni [sulle attività di ACR.](container-registry-tutorial-quick-task.md)



<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks


<!-- LINKS - Internal -->
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-timer]: /cli/azure/acr/task/timer
[az-acr-task-timer-add]: /cli/azure/acr/task/timer#az_acr_task_timer_add
[az-acr-task-timer-remove]: /cli/azure/acr/task/timer#az_acr_task_timer_remove
[az-acr-task-timer-list]: /cli/azure/acr/task/timer#az_acr_task_timer_list
[az-acr-task-timer-update]: /cli/azure/acr/task/timer#az_acr_task_timer_update
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task]: /cli/azure/acr/task
[azure-cli-install]: /cli/azure/install-azure-cli
