---
title: Hotpatch per Windows Server Azure Edition (anteprima)
description: Informazioni sul funzionamento di Hotpatch per Windows Server Azure Edition e su come abilitarlo
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1b3fc9f12dfa6ad4edcc120ac7c9592c9435a0e4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830179"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>Hotpatch per le nuove macchine virtuali (anteprima)

Il hotpatching è un nuovo modo per installare gli aggiornamenti nelle nuove macchine virtuali (VM) di Windows Server Azure Edition che non richiedono un riavvio dopo l'installazione. Questo articolo illustra le informazioni su Hotpatch per le macchine virtuali Windows Server Azure Edition, con i vantaggi seguenti:
* Riduzione dell'impatto del carico di lavoro con un minor numero di riavvii
* Distribuzione più rapida degli aggiornamenti, poiché i pacchetti sono più piccoli, l'installazione più veloce e l'orchestrazione delle patch con Gestione aggiornamenti di Azure è più semplice
* Protezione migliore, poiché l'ambito dei pacchetti di aggiornamento della patch a caldo è quello degli aggiornamenti della sicurezza di Windows che vengono installati più velocemente senza riavvio

## <a name="how-hotpatch-works"></a>Funzionamento della hotpatch

La patch a caldo funziona stabilendo prima una baseline con un Windows Update aggiornamento cumulativo più recente. I hotpatches vengono rilasciati periodicamente ,ad esempio il secondo martedì del mese, che si basano su tale baseline. Gli hotpatches conterranno aggiornamenti che non richiedono un riavvio. Periodicamente (a partire da ogni tre mesi), la baseline viene aggiornata con un nuovo aggiornamento cumulativo più recente.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Pianificazione dell'esempio di hotpatch.":::

Esistono due tipi di baseline: **baseline pianificate** e **baseline non pianificate.**
*  **Le baseline pianificate** vengono rilasciate a cadenza regolare, con le versioni di hotpatch in mezzo.  Le baseline pianificate includono tutti gli aggiornamenti in un aggiornamento cumulativo più recente _confrontabile_ per tale mese e richiedono un riavvio.
    * La pianificazione di esempio precedente illustra quattro versioni di base pianificate in un anno di calendario (cinque in totale nel diagramma) e otto versioni di hotpatch.
* **Le baseline non pianificate** vengono rilasciate quando viene rilasciato un aggiornamento importante, ad esempio una correzione di zero giorni, e tale aggiornamento specifico non può essere rilasciato come hotpatch.  Quando vengono rilasciate baseline non pianificate, una versione di hotpatch verrà sostituita con una baseline non pianificata in quel mese.  Le baseline non pianificate includono anche tutti  gli aggiornamenti in un aggiornamento cumulativo più recente confrontabile per tale mese e richiedono anche un riavvio.
    * La pianificazione di esempio precedente illustra due baseline non pianificate che sostituirebbero le versioni di hotpatch per tali mesi (il numero effettivo di baseline non pianificate in un anno non è noto in anticipo).

## <a name="regional-availability"></a>Disponibilità a livello di area
La funzionalità hotpatch è disponibile in tutte le aree di Azure globali in anteprima. Azure per enti pubblici non sono supportate nell'anteprima.

## <a name="how-to-get-started"></a>Operazioni preliminari

> [!NOTE]
> Durante la fase di anteprima è possibile iniziare solo nel portale di Azure usando [questo collegamento](https://aka.ms/AzureAutomanageHotPatch).

Per iniziare a usare hotpatch in una nuova macchina virtuale, seguire questa procedura:
1.  Abilitare l'accesso in anteprima
    * L'abilitazione dell'accesso in anteprima una sola volta è necessaria per ogni sottoscrizione.
    * L'accesso in anteprima può essere abilitato tramite API, PowerShell o interfaccia della riga di comando, come descritto nella sezione seguente.
1.  Creare una macchina virtuale dal portale di Azure
    * Durante l'anteprima, è necessario iniziare a usare [questo collegamento.](https://aka.ms/AzureAutomanageHotPatch)
1.  Specificare i dettagli della macchina virtuale
    * Assicurarsi che _windows Server 2019 Datacenter: Azure Edition sia_ selezionato nell'elenco a discesa Immagine.
    * Nel passaggio della scheda Gestione scorrere verso il basso fino alla sezione "Aggiornamenti del sistema operativo guest". Per impostazione predefinita, l'opzione Hotpatching è impostata su On e l'installazione delle patch è impostata su Azure orchestrata.
    * Le procedure consigliate per la gestione automatica delle macchine virtuali verranno abilitate per impostazione predefinita
1. Creare la nuova macchina virtuale

## <a name="enabling-preview-access"></a>Abilitazione dell'accesso in anteprima

### <a name="rest-api"></a>API REST

L'esempio seguente descrive come abilitare l'anteprima per la sottoscrizione:

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

Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

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

Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

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

Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>Installazione delle patch

Durante l'anteprima, [l'applicazione automatica](../virtual-machines/automatic-vm-guest-patching.md) delle patch guest alle macchine virtuali viene abilitata automaticamente per tutte le macchine virtuali create con _Windows Server 2019 Datacenter: Azure Edition._ Con l'applicazione automatica delle patch guest alle macchine virtuali abilitata:
* Le patch classificate come Critiche o Sicurezza vengono scaricate e applicate automaticamente nella macchina virtuale.
* Le patch vengono applicate durante le ore di minore attività nel fuso orario della macchina virtuale.
* L'orchestrazione delle patch viene gestita da Azure e le patch vengono applicate seguendo [i principi di disponibilità-first.](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching)
* L'integrità delle macchine virtuali, come determinato tramite i segnali di integrità della piattaforma, viene monitorata per rilevare gli errori di applicazione delle patch.

### <a name="how-does-automatic-vm-guest-patching-work"></a>Come funziona l'applicazione automatica delle patch guest alle macchine virtuali?

Quando [l'applicazione automatica delle patch guest](../virtual-machines/automatic-vm-guest-patching.md) alle macchine virtuali è abilitata in una macchina virtuale, le patch critiche e di sicurezza disponibili vengono scaricate e applicate automaticamente. Questo processo inizia automaticamente ogni mese quando vengono rilasciate nuove patch. La valutazione e l'installazione delle patch sono automatiche e il processo include il riavvio della macchina virtuale in base alle esigenze.

Con Hotpatch abilitato in _Windows Server 2019 Datacenter:_ macchine virtuali dell'edizione di Azure, la maggior parte degli aggiornamenti della sicurezza mensili viene recapitata come hotpatches che non richiedono riavvii. Gli aggiornamenti cumulativi più recenti inviati nei mesi di base pianificati o non pianificati richiederanno il riavvio della macchina virtuale. È anche possibile che siano disponibili periodicamente patch di sicurezza o critiche aggiuntive che potrebbero richiedere il riavvio della macchina virtuale.

La macchina virtuale viene valutata automaticamente ogni pochi giorni e più volte in qualsiasi periodo di 30 giorni per determinare le patch applicabili per tale macchina virtuale. Questa valutazione automatica garantisce che eventuali patch mancanti siano individuate al più presto.

Le patch vengono installate entro 30 giorni dalla versione mensile delle patch, in base [ai principi di disponibilità first.](../virtual-machines/automatic-vm-guest-patching.md#availability-first-patching) Le patch vengono installate solo durante gli orari di minore attività per la macchina virtuale, a seconda del fuso orario della macchina virtuale. La macchina virtuale deve essere in esecuzione durante gli orari di minore attività per l'installazione automatica delle patch. Se una macchina virtuale viene spenta durante una valutazione periodica, verrà valutata e le patch applicabili verranno installate automaticamente durante la valutazione periodica successiva quando la macchina virtuale viene accesa. La valutazione periodica successiva viene in genere eseguita entro pochi giorni.

Gli aggiornamenti delle definizioni e altre patch non classificate come Critico o Sicurezza non verranno installati tramite l'applicazione automatica di patch guest alle macchine virtuali.

## <a name="understanding-the-patch-status-for-your-vm"></a>Informazioni sullo stato della patch per la macchina virtuale

Per visualizzare lo stato della patch per la macchina virtuale, passare alla sezione **Aggiornamenti guest** e host per la macchina virtuale nel portale di Azure. Nella sezione **Aggiornamenti del sistema operativo guest** fare clic su "Vai alla patch a caldo (anteprima)" per visualizzare lo stato della patch più recente per la macchina virtuale.

In questa schermata verrà visualizzato lo stato di hotpatch per la macchina virtuale. È anche possibile verificare se sono presenti patch disponibili per la macchina virtuale che non sono state installate. Come descritto nella sezione "Installazione della patch" precedente, tutti gli aggiornamenti critici e di sicurezza verranno installati automaticamente nella macchina virtuale usando l'applicazione automatica delle patch [guest](../virtual-machines/automatic-vm-guest-patching.md) della macchina virtuale e non sono necessarie azioni aggiuntive. Le patch con altre classificazioni di aggiornamento non vengono installate automaticamente. Sono invece visualizzabili nell'elenco delle patch disponibili nella scheda "Conformità aggiornamenti". È anche possibile visualizzare la cronologia delle distribuzioni degli aggiornamenti nella macchina virtuale tramite "Cronologia aggiornamenti". Viene visualizzata la cronologia degli aggiornamenti degli ultimi 30 giorni, insieme ai dettagli di installazione delle patch.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="Gestione della hotpatch.":::

Con l'applicazione automatica delle patch guest alle macchine virtuali, la macchina virtuale viene valutata periodicamente e automaticamente per gli aggiornamenti disponibili. Queste valutazioni periodiche garantiscono che le patch disponibili siano rilevate. È possibile visualizzare i risultati della valutazione nella schermata Aggiornamenti precedente, inclusa l'ora dell'ultima valutazione. È anche possibile scegliere di attivare una valutazione delle patch su richiesta per la macchina virtuale in qualsiasi momento usando l'opzione "Valuta adesso" ed esaminare i risultati al termine della valutazione.

Analogamente alla valutazione su richiesta, è anche possibile installare patch su richiesta per la macchina virtuale usando l'opzione "Installa aggiornamenti ora". Qui è possibile scegliere di installare tutti gli aggiornamenti in classificazioni di patch specifiche. È anche possibile specificare gli aggiornamenti da includere o escludere fornendo un elenco di singoli knowledge base articoli. Le patch installate su richiesta non vengono installate usando i principi di disponibilità-first e possono richiedere più riavvii e tempi di inattività delle macchine virtuali per l'installazione degli aggiornamenti.

## <a name="supported-updates"></a>Aggiornamenti supportati

Hotpatch Sicurezza di Windows aggiornamenti e mantiene la parità con il contenuto degli aggiornamenti della sicurezza rilasciati nel normale canale di aggiornamento di Windows (non Hotpatch).

Esistono alcune considerazioni importanti per l'esecuzione di una macchina virtuale di Windows Server Azure Edition con Hotpatch abilitato. I riavvii sono comunque necessari per installare gli aggiornamenti non inclusi nel programma Hotpatch. I riavvii sono necessari periodicamente anche dopo l'installazione di una nuova baseline. Questi riavvii mantengono la macchina virtuale sincronizzata con le patch non di sicurezza incluse nell'aggiornamento cumulativo più recente.
* Le patch attualmente non incluse nel programma Hotpatch includono gli aggiornamenti non di sicurezza rilasciati per Windows e gli aggiornamenti non Windows ,ad esempio le patch .NET.  Questi tipi di patch devono essere installati durante un mese di base e richiederanno un riavvio.

## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="what-is-hotpatching"></a>Che cos'è il hotpatching?

* La funzionalità di hotpatching è un nuovo modo per installare gli aggiornamenti in una macchina virtuale Windows Server 2019 Datacenter: Azure Edition in Azure che non richiede un riavvio dopo l'installazione. Funziona tramite l'applicazione di patch al codice in memoria dei processi in esecuzione senza la necessità di riavviare il processo.

### <a name="how-does-hotpatching-work"></a>Come funziona il hotpatching?

* La funzionalità di patch a caldo funziona stabilendo una baseline con un aggiornamento cumulativo più recente di Windows Update, quindi si basa su tale baseline con aggiornamenti che non richiedono un riavvio per avere effetto.  La baseline viene aggiornata periodicamente con un nuovo aggiornamento cumulativo. L'aggiornamento cumulativo include tutti gli aggiornamenti di sicurezza e qualità e richiede un riavvio.

### <a name="why-should-i-use-hotpatch"></a>Perché usare hotpatch?

* Quando si usa hotpatch in Windows Server 2019 Datacenter: Azure Edition, la macchina virtuale avrà una disponibilità più elevata (meno riavvii) e aggiornamenti più veloci (pacchetti più piccoli che vengono installati più velocemente senza la necessità di riavviare i processi). Questo processo comporta una macchina virtuale sempre aggiornata e sicura.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>Quali tipi di aggiornamenti sono coperti da Hotpatch?

* La patch a caldo attualmente copre gli aggiornamenti della sicurezza di Windows.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>Quando si riceverà il primo aggiornamento della hotpatch?

* Gli aggiornamenti con hotpatch vengono in genere rilasciati il secondo martedì di ogni mese. Per altre informazioni, vedere sotto.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>Quale sarà l'aspetto della pianificazione della hotpatch?

* La funzionalità di patch a caldo funziona stabilendo una baseline con un aggiornamento cumulativo più recente di Windows Update, quindi si basa su tale baseline con gli aggiornamenti di patch a caldo rilasciati ogni mese.  Durante l'anteprima, le baseline verranno rilasciate a partire da ogni tre mesi. Vedere l'immagine seguente per un esempio di pianificazione annuale di tre mesi (incluse le baseline non pianificate di esempio a causa di correzioni di zero giorni).

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="Pianificazione dell'esempio di hotpatch.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>I riavvii sono ancora necessari per una macchina virtuale registrato in Hotpatch?

* I riavvii sono ancora necessari per installare gli aggiornamenti non inclusi nel programma hotpatch e sono necessari periodicamente dopo l'installazione di una baseline (Windows Update Aggiornamento cumulativo più recente). Questo riavvio manterà la macchina virtuale sincronizzata con tutte le patch incluse nell'aggiornamento cumulativo. Le baseline (che richiedono un riavvio) inizieranno a cadenza di tre mesi e aumenteranno nel tempo.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>Le applicazioni sono interessate quando viene installato un aggiornamento hotpatch?

* Poiché Hotpatch patch il codice in memoria dei processi in esecuzione senza la necessità di riavviare il processo, le applicazioni non saranno interessate dal processo di applicazione delle patch. Si noti che questo è separato da qualsiasi potenziale impatto sulle prestazioni e sulle funzionalità della patch stessa.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>È possibile disattivare Hotpatch nella macchina virtuale?

* È possibile disattivare Hotpatch in una macchina virtuale tramite il portale di Azure.  La disattivazione di Hotpatch comporta l'annullamento della registrazione della macchina virtuale da Hotpatch, che ripristina il comportamento di aggiornamento tipico della macchina virtuale per Windows Server.  Dopo aver rimosso la registrazione da Hotpatch in una macchina virtuale, è possibile registrarla di nuovo quando viene rilasciata la baseline hotpatch successiva.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>È possibile eseguire l'aggiornamento dal sistema operativo Windows Server esistente?

* L'aggiornamento da versioni esistenti di Windows Server (ovvero Windows Server 2016 o 2019 edizioni non di Azure) non è attualmente supportato. Sarà supportato l'aggiornamento alle versioni future di Windows Server Azure Edition.

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>È possibile usare Hotpatch per i carichi di lavoro di produzione durante l'anteprima?

* Le anteprime sono destinate solo a scopo di test e non per l'uso negli ambienti di produzione.

### <a name="will-i-be-charged-during-the-preview"></a>L'addebito verrà addebitato durante l'anteprima?

* La licenza per Windows Server Azure Edition è gratuita durante l'anteprima. Tuttavia, il costo di qualsiasi infrastruttura sottostante impostata per la macchina virtuale (archiviazione, calcolo, rete e così via) verrà comunque addebitato per la sottoscrizione.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>Come si ottiene il supporto per la risoluzione dei problemi per l'hotpatching?

* È possibile stendare [un ticket del caso di supporto tecnico.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Per l'opzione Servizio cercare e selezionare Macchina virtuale **che esegue Windows** in Calcolo. Selezionare **Funzionalità di Azure** per il tipo di problema e Applicazione automatica delle patch guest **per** il sottotipo del problema.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su Azure Gestione aggiornamenti [qui.](../automation/update-management/overview.md)
* Per altre informazioni sull'applicazione automatica delle patch guest alle macchine [virtuali, vedere qui](../virtual-machines/automatic-vm-guest-patching.md)
