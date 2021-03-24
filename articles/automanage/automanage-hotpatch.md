---
title: Danneggiata per Windows Server Azure Edition (anteprima)
description: Informazioni sul funzionamento di danneggiata per Windows Server Azure Edition e su come abilitarlo
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.openlocfilehash: 92b8bf240dfd73cc9191675db07f20816b7156a8
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953392"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Danneggiata per le nuove macchine virtuali (anteprima)

Patch a caldo è un nuovo modo per installare gli aggiornamenti nelle nuove macchine virtuali (VM) di Windows Server Azure Edition che non richiedono un riavvio dopo l'installazione. Questo articolo illustra le informazioni su danneggiata per le macchine virtuali di Windows Server Azure Edition con i vantaggi seguenti:
* Minore effetto sul carico di lavoro con meno riavvii
* Distribuzione più rapida degli aggiornamenti poiché i pacchetti sono più piccoli, installare più velocemente e avere più semplice orchestrazione patch con Azure Update Manager
* Migliore protezione, poiché i pacchetti di aggiornamento danneggiata hanno come ambito gli aggiornamenti della sicurezza di Windows che vengono installati più velocemente senza riavviare

## <a name="how-hotpatch-works"></a>Funzionamento di danneggiata

Danneggiata funziona stabilendo prima di tutto una baseline con un Windows Update aggiornamento cumulativo più recente. Hotpatches vengono rilasciati periodicamente (ad esempio, il secondo martedì del mese) che si basano su tale Baseline. Hotpatches conterrà gli aggiornamenti che non richiedono un riavvio. Periodicamente (a partire da ogni tre mesi), la baseline viene aggiornata con un nuovo aggiornamento cumulativo più recente.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Pianificazione di esempio danneggiata.":::

Esistono due tipi di Baseline: **baseline pianificate** e **linee di base non pianificate**.
*  Le **linee di base pianificate** vengono rilasciate a cadenza regolare, con le versioni danneggiata.  Le linee di base pianificate includono tutti gli aggiornamenti in un _aggiornamento cumulativo più recente_ e comparabile per quel mese e richiedono un riavvio.
    * La pianificazione di esempio precedente illustra quattro versioni di base pianificate in un anno di calendario (cinque in totale nel diagramma) e otto versioni di danneggiata.
* Le **linee di base non pianificate** vengono rilasciate quando viene rilasciato un aggiornamento importante, ad esempio una correzione di zero giorni, e questo particolare aggiornamento non può essere rilasciato come danneggiata.  Quando vengono rilasciate linee di base non pianificate, una versione danneggiata verrà sostituita con una baseline non pianificata nel mese.  Le linee di base non pianificate includono anche tutti gli aggiornamenti in un _aggiornamento cumulativo più recente_ e comparabile per quel mese e richiedono anche un riavvio.
    * La pianificazione di esempio precedente illustra due baseline non pianificate che sostituiranno le versioni danneggiata per quei mesi (il numero effettivo di linee di base non pianificate in un anno non è noto in anticipo).

## <a name="regional-availability"></a>Disponibilità a livello di area
Danneggiata è disponibile in anteprima in tutte le aree di Azure globali. Le aree di Azure per enti pubblici non sono supportate nell'anteprima.

## <a name="how-to-get-started"></a>Operazioni preliminari

> [!NOTE]
> Durante la fase di anteprima è possibile iniziare solo con il portale di Azure usando [questo collegamento](https://aka.ms/AzureAutomanageHotPatch).

Per iniziare a usare danneggiata in una nuova macchina virtuale, seguire questa procedura:
1.  Abilita l'accesso in anteprima
    * L'abilitazione dell'accesso per l'anteprima monouso è necessaria per ogni sottoscrizione.
    * L'accesso in anteprima può essere abilitato tramite API, PowerShell o l'interfaccia della riga di comando, come descritto nella sezione seguente.
1.  Creare una macchina virtuale dalla portale di Azure
    * Durante l'anteprima è necessario iniziare a usare [questo collegamento](https://aka.ms/AzureAutomanageHotPatch).
1.  Specificare i dettagli della macchina virtuale
    * Assicurarsi che _Windows Server 2019 datacenter: Azure Edition_ sia selezionato nell'elenco a discesa immagine
    * Nel passaggio della scheda Gestione scorrere verso il basso fino alla sezione ' aggiornamenti sistema operativo guest '. Si noterà che patch a caldo è impostato su on e l'installazione della patch è stata impostata per impostazione predefinita su patch orchestrata da Azure.
    * Le procedure consigliate per la gestione delle macchine virtuali verranno abilitate per impostazione predefinita
1. Creare la nuova macchina virtuale

## <a name="enabling-preview-access"></a>Abilitazione dell'accesso in anteprima

### <a name="rest-api"></a>API REST

Nell'esempio seguente viene descritto come abilitare l'anteprima per la sottoscrizione:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

Usare il ```Register-AzProviderFeature``` cmdlet per abilitare l'anteprima per la sottoscrizione.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

Usare ```az feature register``` per abilitare l'anteprima per la sottoscrizione.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Installazione patch

Durante l'anteprima, l'applicazione [automatica delle patch Guest](../virtual-machines/automatic-vm-guest-patching.md) per le VM è abilitata automaticamente per tutte le macchine virtuali create con _Windows Server 2019 datacenter: Azure Edition_. Con l'applicazione automatica delle patch guest VM abilitata:
* Le patch classificate come critiche o la sicurezza vengono scaricate e applicate automaticamente nella macchina virtuale.
* Le patch vengono applicate durante gli orari di minore traffico nel fuso orario della macchina virtuale.
* Patch Orchestration è gestito da Azure e le patch vengono applicate in base ai [principi di disponibilità](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching).
* L'integrità della macchina virtuale, come determinato dai segnali di integrità della piattaforma, viene monitorata per rilevare errori di applicazione delle patch.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Come funziona l'applicazione automatica delle patch per guest VM?

Quando l'applicazione [automatica delle patch Guest](../virtual-machines/automatic-vm-guest-patching.md) per le VM è abilitata in una macchina virtuale, le patch di sicurezza e critiche disponibili vengono scaricate e applicate automaticamente. Questo processo viene automaticamente interrotto ogni mese quando vengono rilasciate nuove patch. La valutazione e l'installazione delle patch sono automatiche e il processo include il riavvio della macchina virtuale secondo le esigenze.

Con danneggiata abilitato in _Windows Server 2019 datacenter:_ macchine virtuali dell'edizione di Azure, la maggior parte degli aggiornamenti di sicurezza mensili viene recapitata come hotpatches che non richiedono il riavvio. Gli aggiornamenti cumulativi più recenti inviati nei mesi di base pianificati o non pianificati richiederanno il riavvio della macchina virtuale. Potrebbero essere disponibili periodicamente altre patch critiche o di sicurezza che potrebbero richiedere il riavvio della macchina virtuale.

La macchina virtuale viene valutata automaticamente ogni pochi giorni e più volte entro un periodo di 30 giorni per determinare le patch applicabili per tale macchina virtuale. Questa valutazione automatica garantisce che tutte le patch mancanti vengano individuate alla prima possibile opportunità.

Le patch vengono installate entro 30 giorni dalle versioni con patch mensili, seguendo i [principi di disponibilità](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching). Le patch vengono installate solo durante gli orari di minore traffico per la macchina virtuale, a seconda del fuso orario della macchina virtuale. Per installare automaticamente le patch, la macchina virtuale deve essere in esecuzione durante gli orari di minore traffico. Se una macchina virtuale viene spenta durante una valutazione periodica, la macchina virtuale verrà valutata e le patch applicabili verranno installate automaticamente durante la valutazione periodica successiva quando la macchina virtuale è accesa. La valutazione periodica successiva si verifica in genere entro pochi giorni.

Gli aggiornamenti delle definizioni e altre patch non classificate come critiche o la sicurezza non verranno installate tramite l'applicazione automatica delle patch Guest per le macchine virtuali.

## <a name="understanding-the-patch-status-for-your-vm"></a>Informazioni sullo stato delle patch per la macchina virtuale

Per visualizzare lo stato della patch per la macchina virtuale, passare alla sezione **aggiornamenti guest + host** per la macchina virtuale nel portale di Azure. Nella sezione **aggiornamenti del sistema operativo guest** fare clic su "Vai a danneggiata (anteprima)" per visualizzare lo stato più recente della patch per la macchina virtuale.

In questa schermata verrà visualizzato lo stato danneggiata per la macchina virtuale. È anche possibile verificare se sono presenti patch disponibili per la VM che non sono state installate. Come descritto nella sezione precedente "installazione patch", tutti gli aggiornamenti critici e di sicurezza verranno installati automaticamente nella macchina virtuale tramite l'applicazione [automatica di patch per guest VM](../virtual-machines/automatic-vm-guest-patching.md) e non sono necessarie azioni aggiuntive. Le patch con altre classificazioni di aggiornamento non vengono installate automaticamente. Sono invece visualizzabili nell'elenco delle patch disponibili nella scheda "conformità aggiornamenti". È anche possibile visualizzare la cronologia delle distribuzioni degli aggiornamenti nella macchina virtuale tramite "cronologia aggiornamenti". Viene visualizzata la cronologia degli aggiornamenti degli ultimi 30 giorni, insieme ai dettagli sull'installazione della patch.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Gestione danneggiata.":::

Con l'applicazione automatica di patch per guest VM, la macchina virtuale viene valutata periodicamente e automaticamente per gli aggiornamenti disponibili. Queste valutazioni periodiche assicurano che vengano rilevate patch disponibili. È possibile visualizzare i risultati della valutazione nella schermata degli aggiornamenti precedente, inclusa la data dell'ultima valutazione. È anche possibile scegliere di attivare una valutazione delle patch su richiesta per la macchina virtuale in qualsiasi momento usando l'opzione "valuta ora" ed esaminare i risultati al termine della valutazione.

Analogamente alla valutazione su richiesta, è anche possibile installare patch su richiesta per la macchina virtuale usando l'opzione "Installa aggiornamenti ora". Qui è possibile scegliere di installare tutti gli aggiornamenti in classificazioni patch specifiche. È inoltre possibile specificare gli aggiornamenti da includere o escludere fornendo un elenco di singoli articoli della Knowledge base. Le patch installate su richiesta non vengono installate usando i principi di disponibilità e possono richiedere più riavvii e tempi di inattività delle macchine virtuali per l'installazione degli aggiornamenti.

## <a name="supported-updates"></a>Aggiornamenti supportati

Danneggiata copre gli aggiornamenti della sicurezza di Windows e mantiene la parità con il contenuto degli aggiornamenti della sicurezza emessi nel canale normale (non danneggiata) di Windows Update.

Ci sono alcune considerazioni importanti per l'esecuzione di una VM Windows Server Azure Edition con danneggiata abilitato. I riavvii sono ancora necessari per installare gli aggiornamenti che non sono inclusi nel programma danneggiata. I riavvii sono necessari anche periodicamente dopo l'installazione di una nuova baseline. Questi riavvii mantengono la sincronizzazione della macchina virtuale con patch non di sicurezza incluse nell'aggiornamento cumulativo più recente.
* Le patch attualmente non incluse nel programma danneggiata includono gli aggiornamenti non della sicurezza rilasciati per Windows e gli aggiornamenti non Windows, ad esempio le patch .NET.  È necessario installare questi tipi di patch durante un mese di base e sarà necessario riavviare il computer.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-is-hotpatching"></a>Che cos'è patch a caldo?

* Patch a caldo è un nuovo modo per installare gli aggiornamenti in un Windows Server 2019 datacenter: una macchina virtuale di Azure Edition in Azure che non richiede un riavvio dopo l'installazione. Funziona con l'applicazione di patch al codice in memoria dei processi in esecuzione senza la necessità di riavviare il processo.

### <a name="how-does-hotpatching-work"></a>Come funziona patch a caldo?

* Patch a caldo funziona stabilendo una linea di base con un Windows Update aggiornamento cumulativo più recente, quindi si basa su tale Baseline con gli aggiornamenti che non richiedono un riavvio per rendere effettiva l'effetto.  La linea di base viene aggiornata periodicamente con un nuovo aggiornamento cumulativo. L'aggiornamento cumulativo include tutti gli aggiornamenti di sicurezza e qualità ed è necessario riavviare il computer.

### <a name="why-should-i-use-hotpatch"></a>Perché usare danneggiata?

* Quando si usa danneggiata in Windows Server 2019 datacenter: Azure Edition, la macchina virtuale avrà una disponibilità più elevata (meno riavvii) e aggiornamenti più veloci (pacchetti più piccoli che vengono installati più velocemente senza la necessità di riavviare i processi). Questo processo genera una macchina virtuale sempre aggiornata e sicura.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Quali tipi di aggiornamenti sono coperti da danneggiata?

* Danneggiata attualmente copre gli aggiornamenti della sicurezza di Windows.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Quando si riceve il primo aggiornamento danneggiata?

* Gli aggiornamenti danneggiata vengono in genere rilasciati il secondo martedì di ogni mese. Per altre informazioni, vedere sotto.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Quale sarà l'aspetto della pianificazione danneggiata?

* Patch a caldo funziona stabilendo una linea di base con un Windows Update aggiornamento cumulativo più recente, quindi si basa su tale Baseline con gli aggiornamenti danneggiata rilasciati ogni mese.  Durante l'anteprima, le linee di base verranno rilasciate iniziando ogni tre mesi. Vedere l'immagine seguente per un esempio di pianificazione annuale di tre mesi, incluse le previsioni di esempio non pianificate a causa di correzioni di zero giorni.

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Pianificazione di esempio danneggiata.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>I riavvii sono ancora necessari per una macchina virtuale registrata in danneggiata?

* I riavvii sono ancora necessari per installare gli aggiornamenti non inclusi nel programma danneggiata e sono necessari periodicamente dopo l'installazione di una baseline (Windows Update aggiornamento cumulativo più recente). Questo riavvio manterrà la macchina virtuale sincronizzata con tutte le patch incluse nell'aggiornamento cumulativo. Le linee di base (che richiedono un riavvio) iniziano con una cadenza di tre mesi e aumentano nel tempo.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Le applicazioni sono interessate quando viene installato un aggiornamento danneggiata?

* Poiché danneggiata patches il codice in memoria dei processi in esecuzione senza la necessità di riavviare il processo, le applicazioni non saranno interessate dal processo di applicazione delle patch. Si noti che questo è separato dalle possibili implicazioni di prestazioni e funzionalità della patch.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>È possibile disattivare danneggiata nella macchina virtuale?

* È possibile disattivare danneggiata in una macchina virtuale tramite il portale di Azure.  La disattivazione di danneggiata Annulla la registrazione della macchina virtuale da danneggiata, che ripristina il comportamento tipico degli aggiornamenti per Windows Server.  Dopo aver annullato la registrazione da danneggiata in una macchina virtuale, è possibile registrare di nuovo la macchina virtuale quando viene rilasciata la prossima Baseline danneggiata.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>È possibile eseguire l'aggiornamento dal sistema operativo Windows Server esistente?

* L'aggiornamento da versioni esistenti di Windows Server (ovvero, Windows Server 2016 o 2019 edizioni non Azure) non è attualmente supportato. Sarà supportato l'aggiornamento alle versioni future di Windows Server Azure Edition.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>È possibile usare danneggiata per i carichi di lavoro di produzione durante l'anteprima?

* Le anteprime sono progettate solo a scopo di test e non per l'uso in ambienti di produzione.

### <a name="will-i-be-charged-during-the-preview"></a>Verranno addebitati i costi durante l'anteprima?

* La licenza per Windows Server Azure Edition è gratuita durante l'anteprima. Tuttavia, il costo di qualsiasi infrastruttura sottostante configurato per la macchina virtuale (archiviazione, calcolo, rete e così via) verrà comunque addebitato alla sottoscrizione.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Come è possibile ottenere supporto per la risoluzione dei problemi per patch a caldo?

* È possibile archiviare un [ticket del caso di supporto tecnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Per l'opzione servizio, cercare e selezionare la **macchina virtuale che esegue Windows** in calcolo. Selezionare le **funzionalità di Azure** per il tipo di problema e l'applicazione automatica di patch per **guest VM** per il sottotipo di problema.

## <a name="next-steps"></a>Passaggi successivi

* Scopri di più su Azure Gestione aggiornamenti [qui](../automation/update-management/overview.md).
* Altre informazioni sull'applicazione automatica di patch per guest VM sono disponibili [qui](../virtual-machines/automatic-vm-guest-patching.md)