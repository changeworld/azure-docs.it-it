---
title: Applicazione automatica di patch guest alle macchine virtuali di Azure
description: Informazioni su come applicare automaticamente patch alle macchine virtuali in Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 1a6a67fe43d4e0a6086154d71e61fe51680dbcd0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762588"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>Anteprima: Applicazione automatica di patch guest alle macchine virtuali per le macchine virtuali di Azure

L'abilitazione dell'applicazione automatica di patch guest alle macchine virtuali di Azure consente di semplificare la gestione degli aggiornamenti tramite l'applicazione automatica di patch alle macchine virtuali per mantenere la conformità alla sicurezza.

L'applicazione automatica di patch guest alle macchine virtuali presenta le caratteristiche seguenti:
- Le patch classificate *come Critiche* o *Sicurezza vengono* scaricate e applicate automaticamente nella macchina virtuale.
- Le patch vengono applicate durante le ore di minore attività nel fuso orario della macchina virtuale.
- L'orchestrazione delle patch viene gestita da Azure e le patch vengono applicate in [base ai principi di disponibilità first.](#availability-first-patching)
- L'integrità della macchina virtuale, come determinato tramite i segnali di integrità della piattaforma, viene monitorata per rilevare gli errori di applicazione di patch.
- È adatto per le macchine virtuali di qualsiasi dimensione.

> [!IMPORTANT]
> L'applicazione automatica delle patch guest alle macchine virtuali è attualmente disponibile in anteprima pubblica. Per usare la funzionalità di anteprima pubblica descritta di seguito, è necessaria una procedura di consenso esplicito.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Come funziona l'applicazione automatica delle patch guest alle macchine virtuali?

Se l'applicazione automatica di patch guest alle  macchine virtuali è abilitata in una macchina virtuale, le *patch* critiche e di sicurezza disponibili vengono scaricate e applicate automaticamente nella macchina virtuale. Questo processo si avvia automaticamente ogni mese quando vengono rilasciate nuove patch. La valutazione e l'installazione delle patch sono automatiche e il processo include il riavvio della macchina virtuale in base alle esigenze.

La macchina virtuale viene valutata periodicamente ogni pochi giorni e più volte entro un periodo di 30 giorni per determinare le patch applicabili per tale macchina virtuale. Le patch possono essere installate in qualsiasi giorno nella macchina virtuale durante gli orari di minore attività per la macchina virtuale. Questa valutazione automatica garantisce che eventuali patch mancanti siano individuate al più presto.

Le patch vengono installate entro 30 giorni dalla versione mensile delle patch, dopo l'orchestrazione availability-first descritta di seguito. Le patch vengono installate solo durante gli orari di minore attività per la macchina virtuale, a seconda del fuso orario della macchina virtuale. La macchina virtuale deve essere in esecuzione durante le ore di minore attività per l'installazione automatica delle patch. Se una macchina virtuale viene spenta durante una valutazione periodica, la macchina virtuale verrà valutata automaticamente e le patch applicabili verranno installate automaticamente durante la valutazione periodica successiva (in genere entro pochi giorni) quando la macchina virtuale è accesa.

Gli aggiornamenti delle definizioni e altre patch non classificate *come critiche* o *di* sicurezza non verranno installati tramite l'applicazione automatica di patch guest alle macchine virtuali. Per installare le patch con altre classificazioni delle patch o pianificare l'installazione delle patch all'interno della finestra di [manutenzione](./windows/tutorial-config-management.md#manage-windows-updates)personalizzata, è possibile usare Gestione aggiornamenti .

### <a name="availability-first-patching"></a>Applicazione di patch a livello di disponibilità

Il processo di installazione delle patch viene orchestrato a livello globale da Azure per tutte le macchine virtuali in cui è abilitata l'applicazione automatica delle patch guest alle macchine virtuali. Questa orchestrazione segue i principi della disponibilità in base ai diversi livelli di disponibilità forniti da Azure.

Per un gruppo di macchine virtuali in fase di aggiornamento, la piattaforma Azure orchestra gli aggiornamenti:

**Tra aree:**
- Un aggiornamento mensile viene orchestrato in Azure a livello globale in modo graduale per evitare errori di distribuzione globale.
- Una fase può avere una o più aree e un aggiornamento passa alle fasi seguenti solo se le macchine virtuali idonee in una fase vengono aggiornate correttamente.
- Le aree con associazione geografica non vengono aggiornate contemporaneamente e non possono essere nella stessa fase a livello di area.
- L'esito positivo di un aggiornamento viene misurato tramite il rilevamento dell'integrità della macchina virtuale dopo l'aggiornamento. L'integrità della macchina virtuale viene monitorata tramite gli indicatori di integrità della piattaforma per la macchina virtuale.

**All'interno di un'area:**
- Le macchine virtuali in zone di disponibilità non vengono aggiornate contemporaneamente.
- Le macchine virtuali che non fanno parte di un set di disponibilità vengono eseguite in batch in batch per evitare aggiornamenti simultanei per tutte le macchine virtuali in una sottoscrizione.

**All'interno di un set di disponibilità:**
- Tutte le macchine virtuali in un set di disponibilità comune non vengono aggiornate contemporaneamente.
-   Le macchine virtuali in un set di disponibilità comune vengono aggiornate entro i limiti del dominio di aggiornamento e le macchine virtuali in più domini di aggiornamento non vengono aggiornate contemporaneamente.

La data di installazione della patch per una determinata macchina virtuale può variare da un mese all'altro, perché una macchina virtuale specifica può essere prelevata in un batch diverso tra cicli di applicazione di patch mensili.

### <a name="which-patches-are-installed"></a>Quali patch sono installate?
Le patch installate dipendono dalla fase di implementazione per la macchina virtuale. Ogni mese viene avviata una nuova implementazione globale in cui tutte le patch critiche e di sicurezza valutate per una singola macchina virtuale vengono installate per tale macchina virtuale. L'implementazione è orchestrata in tutte le aree di Azure in batch (descritta nella sezione precedente sull'applicazione di patch availability-first).

Il set esatto di patch da installare varia in base alla configurazione della macchina virtuale, inclusi il tipo di sistema operativo e la tempistica di valutazione. È possibile installare patch diverse per due macchine virtuali identiche in aree diverse se sono disponibili più o meno patch quando l'orchestrazione delle patch raggiunge aree diverse in momenti diversi. Analogamente, ma meno frequentemente, le macchine virtuali all'interno della stessa area ma valutate in momenti diversi (a causa di diversi batch di zone di disponibilità o set di disponibilità) potrebbero ottenere patch diverse.

Poiché l'applicazione automatica delle patch guest alle macchine virtuali non configura l'origine della patch, anche due macchine virtuali simili configurate per origini di patch diverse, ad esempio repository pubblico e repository privato, possono vedere una differenza nel set esatto di patch installate.

Per i tipi di sistema operativo che rilasciano patch a cadenza fissa, le macchine virtuali configurate nel repository pubblico per il sistema operativo possono prevedere di ricevere lo stesso set di patch nelle diverse fasi di implementazione in un mese. Ad esempio, le macchine virtuali Windows configurate per il repository Windows Update pubblico.

Quando viene attivata una nuova implementazione ogni mese, una macchina virtuale riceverà almeno un'implementazione di patch ogni mese se la macchina virtuale viene attivata durante gli orari di minore attività. In questo modo si garantisce che alla macchina virtuale siano associate le patch di sicurezza e critiche più recenti su base mensile. Per garantire la coerenza nel set di patch installate, è possibile configurare le macchine virtuali per valutare e scaricare le patch dai propri repository privati.

## <a name="supported-os-images"></a>Immagini del sistema operativo supportate
Nell'anteprima sono attualmente supportate solo le macchine virtuali create da determinate immagini della piattaforma del sistema operativo. Le immagini personalizzate non sono attualmente supportate nell'anteprima.

Gli SKU della piattaforma seguenti sono attualmente supportati (e altri vengono aggiunti periodicamente):

| Publisher               | Offerta sistema operativo      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| Redhat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

## <a name="patch-orchestration-modes"></a>Modalità di orchestrazione delle patch
Le macchine virtuali in Azure supportano ora le modalità di orchestrazione delle patch seguenti:

**AutomaticByPlatform:**
- Questa modalità è supportata sia per le macchine virtuali Linux che per le macchine virtuali Windows.
- Questa modalità abilita l'applicazione automatica delle patch guest alla macchina virtuale e l'installazione successiva della patch viene orchestrata da Azure.
- Questa modalità è necessaria per l'applicazione di patch a livello di disponibilità.
- Questa modalità è supportata solo per le macchine virtuali create usando le immagini della piattaforma del sistema operativo supportate in precedenza.
- Per le macchine virtuali Windows, l'impostazione di questa modalità disabilita anche l'Aggiornamenti automatici nativa nella macchina virtuale Windows per evitare la duplicazione.
- Per usare questa modalità nelle macchine virtuali Linux, impostare la proprietà `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` nel modello di macchina virtuale.
- Per usare questa modalità nelle macchine virtuali Windows, impostare la proprietà `osProfile.windowsConfiguration.enableAutomaticUpdates=true` e la proprietà nel modello di macchina  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` virtuale.

**AutomaticByOS:**
- Questa modalità è supportata solo per le macchine virtuali Windows.
- Questa modalità consente Aggiornamenti automatici nella macchina virtuale Windows e le patch vengono installate nella macchina virtuale tramite Aggiornamenti automatici.
- Questa modalità non supporta l'applicazione di patch a livello di disponibilità.
- Questa modalità viene impostata per impostazione predefinita se non viene specificata alcuna altra modalità patch per una macchina virtuale Windows.
- Per usare questa modalità nelle macchine virtuali Windows, impostare la proprietà `osProfile.windowsConfiguration.enableAutomaticUpdates=true` e la proprietà nel modello di macchina  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` virtuale.

**Manuale:**
- Questa modalità è supportata solo per le macchine virtuali Windows.
- Questa modalità disabilita Aggiornamenti automatici nella macchina virtuale Windows.
- Questa modalità non supporta l'applicazione di patch availability-first.
- Questa modalità deve essere impostata quando si usano soluzioni di applicazione di patch personalizzate.
- Per usare questa modalità nelle macchine virtuali Windows, impostare la proprietà `osProfile.windowsConfiguration.enableAutomaticUpdates=false` e la proprietà nel modello di macchina  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` virtuale.

**ImageDefault:**
- Questa modalità è supportata solo per le macchine virtuali Linux.
- Questa modalità non supporta l'applicazione di patch availability-first.
- Questa modalità rispetta la configurazione di applicazione di patch predefinita nell'immagine usata per creare la macchina virtuale.
- Questa modalità è impostata per impostazione predefinita se non viene specificata un'altra modalità patch per una macchina virtuale Linux.
- Per usare questa modalità nelle macchine virtuali Linux, impostare la proprietà `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` nel modello di macchina virtuale.

> [!NOTE]
>Per le macchine virtuali Windows, la proprietà attualmente può `osProfile.windowsConfiguration.enableAutomaticUpdates` essere impostata solo quando la macchina virtuale viene creata per la prima volta. Il passaggio dalla modalità manuale alla modalità automatica o dalla modalità automatica alla modalità manuale non è attualmente supportato. È supportato il passaggio dalla modalità AutomaticByOS alla modalità AutomaticByPlatfom.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Requisiti per l'abilitazione dell'applicazione automatica di patch guest alle macchine virtuali

- Nella macchina virtuale deve essere installato l'agente di macchine virtuali di Azure [per Windows](./extensions/agent-windows.md) [o Linux.](./extensions/agent-linux.md)
- Per le macchine virtuali Linux, l'agente Linux di Azure deve essere versione 2.2.53.1 o successiva. [Aggiornare l'agente Linux](./extensions/update-linux-agent.md) se la versione corrente è inferiore alla versione richiesta.
- Per le macchine virtuali Windows, il Windows Update deve essere in esecuzione nella macchina virtuale.
- La macchina virtuale deve essere in grado di accedere agli endpoint di aggiornamento configurati. Se la macchina virtuale è configurata per l'uso di repository privati per Linux o Windows Server Update Services (WSUS) per le macchine virtuali Windows, gli endpoint di aggiornamento pertinenti devono essere accessibili.
- Usare l'API di calcolo versione 2020-12-01 o successiva. L'API di calcolo versione 2020-06-01 può essere usata per le macchine virtuali Windows con funzionalità limitate.

L'abilitazione della funzionalità di anteprima richiede un consenso esplicito una sola volta per le funzionalità **InGuestAutoPatchVMPreview** e **InGuestPatchVMPreview** per sottoscrizione, come descritto in dettaglio nella sezione seguente.

### <a name="rest-api"></a>API REST
L'esempio seguente descrive come abilitare l'anteprima per la sottoscrizione:

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Usare il cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) per abilitare l'anteprima per la sottoscrizione.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Usare [az feature register](/cli/azure/feature#az_feature_register) per abilitare l'anteprima per la sottoscrizione.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Dopo aver registrato la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Abilitare l'applicazione automatica di patch guest alle macchine virtuali
Per abilitare l'applicazione automatica di patch guest alle macchine virtuali Windows, assicurarsi che la proprietà *osProfile.windowsConfiguration.enableAutomaticUpdates* sia impostata su *true* nella definizione del modello di macchina virtuale. Questa proprietà può essere impostata solo durante la creazione della macchina virtuale. Questa proprietà aggiuntiva non è applicabile per le macchine virtuali Linux.

### <a name="rest-api-for-linux-vms"></a>API REST per macchine virtuali Linux
L'esempio seguente descrive come abilitare l'applicazione automatica di patch guest alle macchine virtuali:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>API REST per macchine virtuali Windows
L'esempio seguente descrive come abilitare l'applicazione automatica di patch guest alle macchine virtuali:

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>Azure PowerShell per macchine virtuali Windows
Usare il cmdlet [Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) per abilitare l'applicazione automatica di patch guest alle macchine virtuali durante la creazione o l'aggiornamento di una macchina virtuale.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Interfaccia della riga di comando di Azure per macchine virtuali Windows
Usare [az vm create per](/cli/azure/vm#az_vm_create) abilitare l'applicazione automatica di patch guest alle macchine virtuali durante la creazione di una nuova macchina virtuale. L'esempio seguente configura l'applicazione automatica di patch guest alle macchine virtuali per una macchina virtuale denominata *myVM* nel gruppo di risorse *denominato myResourceGroup:*

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Per modificare una macchina virtuale esistente, usare [az vm update](/cli/azure/vm#az_vm_update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Abilitazione e valutazione

> [!NOTE]
>L'abilitazione degli aggiornamenti guest automatici in una macchina virtuale può richiedere più di tre ore, perché l'abilitazione viene completata durante gli orari di minore attività della macchina virtuale. Poiché la valutazione e l'installazione delle patch vengono eseguite solo durante gli orari di minore attività, la macchina virtuale deve essere in esecuzione anche durante gli orari di minore attività per applicare le patch.

Quando è abilitata l'applicazione automatica delle patch guest per una macchina virtuale, viene installata un'estensione della macchina virtuale di tipo in una macchina virtuale Linux o un'estensione di macchina virtuale di tipo in `Microsoft.CPlat.Core.LinuxPatchExtension` `Microsoft.CPlat.Core.WindowsPatchExtension` una macchina virtuale Windows. Non è necessario installare o aggiornare manualmente questa estensione, perché è gestita dalla piattaforma Azure come parte del processo automatico di applicazione di patch guest alle macchine virtuali.

L'abilitazione degli aggiornamenti guest automatici in una macchina virtuale può richiedere più di tre ore, perché l'abilitazione viene completata durante gli orari di minore attività della macchina virtuale. L'estensione viene installata e aggiornata anche durante gli orari di minore attività per la macchina virtuale. Se le ore di minore attività della macchina virtuale terminano prima del completamento dell'abilitazione, il processo di abilitazione riprenderà durante il successivo orario di minore attività disponibile.

Gli aggiornamenti automatici sono disabilitati nella maggior parte degli scenari e l'installazione delle patch viene eseguita tramite l'estensione in futuro. Si applicano le condizioni seguenti.
- Se una macchina virtuale Windows in precedenza aveva l'Windows Update automatica attivata tramite la modalità patch AutomaticByOS, l'Windows Update automatico viene disattivato per la macchina virtuale quando viene installata l'estensione.
- Per le macchine virtuali Ubuntu, gli aggiornamenti automatici predefiniti vengono disabilitati automaticamente al termine dell'abilitazione dell'applicazione automatica delle patch guest alle macchine virtuali.
- Per RHEL, gli aggiornamenti automatici devono essere disabilitati manualmente (si tratta di una limitazione di anteprima). Eseguire:

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Per verificare se l'applicazione automatica delle patch guest alla macchina virtuale è stata completata e se l'estensione per l'applicazione di patch è installata nella macchina virtuale, è possibile esaminare la visualizzazione dell'istanza della macchina virtuale. Se il processo di abilitazione è completo, l'estensione verrà installata e i risultati della valutazione per la macchina virtuale saranno disponibili in `patchStatus` . È possibile accedere alla vista dell'istanza della macchina virtuale in diversi modi, come descritto di seguito.

### <a name="rest-api"></a>API REST

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Usare il cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) con il `-Status` parametro per accedere alla vista dell'istanza per la macchina virtuale.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell attualmente fornisce solo informazioni sull'estensione della patch. Le informazioni `patchStatus` su saranno presto disponibili anche tramite PowerShell.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Usare [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) per accedere alla vista dell'istanza per la macchina virtuale.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Informazioni sullo stato della patch per la macchina virtuale

La `patchStatus` sezione della risposta di visualizzazione dell'istanza fornisce informazioni dettagliate sulla valutazione più recente e sull'ultima installazione della patch per la macchina virtuale.

I risultati della valutazione per la macchina virtuale possono essere esaminati nella `availablePatchSummary` sezione . Viene eseguita periodicamente una valutazione per una macchina virtuale in cui è abilitata l'applicazione automatica delle patch guest alle macchine virtuali. Numero di patch disponibili dopo la valutazione in `criticalAndSecurityPatchCount` e `otherPatchCount` risultati. L'applicazione automatica delle patch guest alle macchine virtuali installerà tutte le patch valutate nelle classificazioni *delle* *patch* critiche e di sicurezza. Qualsiasi altra patch valutata viene ignorata.

I risultati dell'installazione della patch per la macchina virtuale possono essere esaminati nella `lastPatchInstallationSummary` sezione . Questa sezione fornisce informazioni dettagliate sull'ultimo tentativo di installazione della patch nella macchina virtuale, incluso il numero di patch installate, in sospeso, non riuscite o ignorate. Le patch vengono installate solo durante la finestra di manutenzione degli orari di minore attività per la macchina virtuale. Le patch in sospeso e non riuscite vengono ritentate automaticamente durante la finestra di manutenzione successiva degli orari di minore attività.

## <a name="on-demand-patch-assessment"></a>Valutazione delle patch su richiesta
Se l'applicazione automatica delle patch guest alla macchina virtuale è già abilitata per la macchina virtuale, viene eseguita una valutazione periodica delle patch nella macchina virtuale durante gli orari di minore attività della macchina virtuale. Questo processo è automatico e i risultati della valutazione più recente possono essere esaminati tramite la visualizzazione dell'istanza della macchina virtuale, come descritto in precedenza in questo documento. È anche possibile attivare una valutazione delle patch su richiesta per la macchina virtuale in qualsiasi momento. Il completamento della valutazione della patch può richiedere alcuni minuti e lo stato della valutazione più recente viene aggiornato nella visualizzazione dell'istanza della macchina virtuale.

Per abilitare la funzionalità di anteprima è necessario acconsentire esplicitamente una sola volta alla **funzionalità InGuestPatchVMPreview** per ogni sottoscrizione. Questa funzionalità di anteprima è diversa dalla registrazione automatica della funzionalità di applicazione di patch guest alle macchine virtuali eseguita in precedenza **per InGuestAutoPatchVMPreview.** L'abilitazione dell'anteprima delle funzionalità aggiuntive è un requisito separato e aggiuntivo. L'anteprima delle funzionalità per la valutazione [](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) delle patch su richiesta può essere abilitata seguendo il processo di abilitazione dell'anteprima descritto in precedenza per l'applicazione automatica di patch guest alle macchine virtuali.

> [!NOTE]
>La valutazione delle patch su richiesta non attiva automaticamente l'installazione delle patch. Se è stata abilitata l'applicazione automatica di patch guest alle macchine virtuali, le patch valutate e applicabili per la macchina virtuale verranno installate durante le ore di minore attività della macchina virtuale, seguendo il processo di applicazione di patch availability-first descritto in precedenza in questo documento.

### <a name="rest-api"></a>API REST
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
Usare il cmdlet [Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) per valutare le patch disponibili per la macchina virtuale.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Usare [az vm assess-patches per](/cli/azure/vm#az_vm_assess_patches) valutare le patch disponibili per la macchina virtuale.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Altre informazioni sulla creazione e la gestione di macchine virtuali Windows](./windows/tutorial-manage-vm.md)
