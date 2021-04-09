---
title: Applicazione automatica delle patch Guest per VM di Azure
description: Informazioni su come applicare patch automaticamente alle macchine virtuali in Azure.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 276762bc2b8624f687cbb77e1af771478791a57b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679799"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>Anteprima: applicazione automatica di patch per guest VM per macchine virtuali di Azure

L'abilitazione dell'applicazione automatica delle patch Guest per le VM di Azure consente di semplificare la gestione degli aggiornamenti con l'applicazione di patch alle macchine virtuali in modo sicuro e automatico per garantire la conformità

L'applicazione automatica di patch per guest VM presenta le caratteristiche seguenti:
- Le patch classificate come *critiche* o la *sicurezza* vengono scaricate e applicate automaticamente nella macchina virtuale.
- Le patch vengono applicate durante gli orari di minore traffico nel fuso orario della macchina virtuale.
- Patch Orchestration è gestito da Azure e le patch vengono applicate in base ai [principi di disponibilità](#availability-first-patching).
- L'integrità della macchina virtuale, come determinato dai segnali di integrità della piattaforma, viene monitorata per rilevare errori di applicazione delle patch.
- È adatto per le macchine virtuali di qualsiasi dimensione.

> [!IMPORTANT]
> L'applicazione automatica delle patch Guest per macchine virtuali è attualmente disponibile in anteprima pubblica. Per usare la funzionalità di anteprima pubblica descritta di seguito, è necessaria una procedura di consenso esplicito.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="how-does-automatic-vm-guest-patching-work"></a>Come funziona l'applicazione automatica delle patch per guest VM?

Se l'applicazione automatica delle patch Guest per le VM è abilitata in una macchina virtuale, le patch di *sicurezza* e *critiche* disponibili vengono scaricate e applicate automaticamente nella macchina virtuale. Questo processo viene automaticamente interrotto ogni mese quando vengono rilasciate nuove patch. La valutazione e l'installazione delle patch sono automatiche e il processo include il riavvio della macchina virtuale secondo le esigenze.

La macchina virtuale viene valutata periodicamente ogni pochi giorni e più volte entro un periodo di 30 giorni per determinare le patch applicabili per tale macchina virtuale. Le patch possono essere installate ogni giorno nella macchina virtuale durante gli orari di minore traffico per la macchina virtuale. Questa valutazione automatica garantisce che tutte le patch mancanti vengano individuate alla prima possibile opportunità.

Le patch vengono installate entro 30 giorni dalle versioni mensili della patch, che seguono la prima orchestrazione di disponibilità descritta di seguito. Le patch vengono installate solo durante gli orari di minore traffico per la macchina virtuale, a seconda del fuso orario della macchina virtuale. Per installare automaticamente le patch, la macchina virtuale deve essere in esecuzione durante gli orari di minore traffico. Se una macchina virtuale viene spenta durante una valutazione periodica, la macchina virtuale verrà automaticamente valutata e le patch applicabili verranno installate automaticamente durante la successiva valutazione periodica (in genere entro pochi giorni) quando la macchina virtuale è accesa.

Gli aggiornamenti delle definizioni e altre patch non classificate come *critiche* o la *sicurezza* non verranno installate tramite l'applicazione automatica di patch per guest VM. Per installare patch con altre classificazioni patch o pianificare l'installazione di patch all'interno della finestra di manutenzione personalizzata, è possibile usare [Gestione aggiornamenti](./windows/tutorial-config-management.md#manage-windows-updates).

### <a name="availability-first-patching"></a>Disponibilità-prima applicazione di patch

Il processo di installazione della patch viene orchestrato a livello globale da Azure per tutte le macchine virtuali in cui è abilitata l'applicazione automatica delle patch per guest VM. Questa orchestrazione segue i principi di disponibilità in diversi livelli di disponibilità offerti da Azure.

Per un gruppo di macchine virtuali in fase di aggiornamento, la piattaforma Azure eseguirà l'orchestrazione degli aggiornamenti:

**Tra le aree:**
- Un aggiornamento mensile viene orchestrato in Azure a livello globale in modo graduale per evitare errori di distribuzione globali.
- Una fase può avere una o più aree e un aggiornamento passa alle fasi successive solo se le VM idonee in una fase vengono aggiornate correttamente.
- Le aree geografiche abbinate non vengono aggiornate simultaneamente e non possono trovarsi nella stessa fase regionale.
- Il completamento di un aggiornamento viene misurato tenendo traccia dell'aggiornamento del post di integrità della macchina virtuale. L'integrità della macchina virtuale viene rilevata tramite indicatori di integrità della piattaforma per la macchina virtuale.

**All'interno di un'area:**
- Le macchine virtuali in zone di disponibilità diverse non vengono aggiornate simultaneamente.
- Le macchine virtuali che non fanno parte di un set di disponibilità vengono eseguite in batch in base al massimo sforzo per evitare aggiornamenti simultanei per tutte le macchine virtuali in una sottoscrizione.

**All'interno di un set di disponibilità:**
- Tutte le macchine virtuali in un set di disponibilità comune non vengono aggiornate simultaneamente.
-   Le macchine virtuali in un set di disponibilità comune vengono aggiornate all'interno dei limiti del dominio di aggiornamento e le macchine virtuali in più domini di aggiornamento non vengono aggiornate simultaneamente.

La data di installazione della patch per una determinata macchina virtuale può variare da un mese all'altro, perché una macchina virtuale specifica può essere prelevata in un altro batch tra cicli di patch mensili.

### <a name="which-patches-are-installed"></a>Quali patch sono installate?
Le patch installate dipendono dalla fase di implementazione per la macchina virtuale. Ogni mese viene avviata una nuova implementazione globale in cui vengono installate tutte le patch di sicurezza e critici per una singola macchina virtuale. L'implementazione viene orchestrata in tutte le aree di Azure in batch, descritta nella sezione precedente relativa alla disponibilità delle patch.

Il set esatto di patch da installare varia in base alla configurazione della macchina virtuale, inclusi il tipo di sistema operativo e la durata della valutazione. È possibile che due macchine virtuali identiche in aree diverse ottengano l'installazione di patch diverse se sono disponibili più o meno patch quando l'orchestrazione patch raggiunge aree diverse in momenti diversi. Analogamente, ma con minore frequenza, le macchine virtuali all'interno della stessa area, ma valutate in momenti diversi (a causa di diversi batch di zone di disponibilità o set di disponibilità) potrebbero ottenere patch diverse.

Poiché l'applicazione automatica delle patch Guest per le macchine virtuali non configura l'origine della patch, due macchine virtuali simili configurate per diverse origini patch, ad esempio repository pubblico e repository privato, potrebbero vedere anche una differenza nell'esatto set di patch installate.

Per i tipi di sistema operativo che rilascia patch a cadenza fissa, le macchine virtuali configurate per il repository pubblico per il sistema operativo possono prevedere di ricevere lo stesso set di patch tra le diverse fasi di implementazione di un mese. Ad esempio, le macchine virtuali Windows configurate per il repository Windows Update pubblico.

Quando viene attivata una nuova implementazione ogni mese, una macchina virtuale riceverà almeno un'implementazione di patch ogni mese se la macchina virtuale viene accesa durante gli orari di minore traffico. In questo modo si garantisce che la macchina virtuale venga applicata con le patch di sicurezza e critici più recenti su base mensile. Per garantire la coerenza nel set di patch installate, è possibile configurare le macchine virtuali per valutare e scaricare le patch dai repository privati.

## <a name="supported-os-images"></a>Immagini del sistema operativo supportate
Nell'anteprima sono attualmente supportate solo le macchine virtuali create da determinate immagini della piattaforma del sistema operativo. Le immagini personalizzate non sono attualmente supportate nell'anteprima.

Gli SKU di piattaforma seguenti sono attualmente supportati e altri vengono aggiunti periodicamente:

| Publisher               | Offerta sistema operativo      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| Redhat  | RHEL | 7. x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

## <a name="patch-orchestration-modes"></a>Patch Orchestration Mode
Le macchine virtuali in Azure supportano ora le modalità di orchestrazione delle patch seguenti:

**AutomaticByPlatform:**
- Questa modalità è supportata per le macchine virtuali Linux e Windows.
- Questa modalità consente l'applicazione automatica delle patch Guest per la macchina virtuale e l'installazione della patch successiva è orchestrata da Azure.
- Questa modalità è obbligatoria per la prima applicazione di patch.
- Questa modalità è supportata solo per le macchine virtuali create con le immagini della piattaforma del sistema operativo supportate sopra.
- Per le macchine virtuali Windows, l'impostazione di questa modalità disabilita anche la Aggiornamenti automatici nativa nella macchina virtuale Windows per evitare la duplicazione.
- Per usare questa modalità nelle VM Linux, impostare la proprietà `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` nel modello di macchina virtuale.
- Per usare questa modalità nelle VM Windows, impostare la proprietà `osProfile.windowsConfiguration.enableAutomaticUpdates=true` e impostare la proprietà  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` nel modello di macchina virtuale.

**AutomaticByOS:**
- Questa modalità è supportata solo per le macchine virtuali Windows.
- Questa modalità Abilita Aggiornamenti automatici nella macchina virtuale Windows e le patch vengono installate nella VM tramite Aggiornamenti automatici.
- Questa modalità non supporta l'applicazione di patch prima della disponibilità.
- Questa modalità è impostata per impostazione predefinita se non viene specificata nessun'altra modalità patch per una macchina virtuale Windows.
- Per usare questa modalità nelle VM Windows, impostare la proprietà `osProfile.windowsConfiguration.enableAutomaticUpdates=true` e impostare la proprietà  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` nel modello di macchina virtuale.

**Manuale:**
- Questa modalità è supportata solo per le macchine virtuali Windows.
- Questa modalità Disabilita Aggiornamenti automatici nella macchina virtuale Windows.
- Questa modalità non supporta l'applicazione di patch prima della disponibilità.
- Questa modalità deve essere impostata quando si utilizzano soluzioni di applicazione di patch personalizzate.
- Per usare questa modalità nelle VM Windows, impostare la proprietà `osProfile.windowsConfiguration.enableAutomaticUpdates=false` e impostare la proprietà  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` nel modello di macchina virtuale.

**ImageDefault:**
- Questa modalità è supportata solo per le macchine virtuali Linux.
- Questa modalità non supporta l'applicazione di patch prima della disponibilità.
- Questa modalità rispetta la configurazione di patch predefinita nell'immagine usata per creare la macchina virtuale.
- Questa modalità è impostata per impostazione predefinita se non viene specificata nessun'altra modalità patch per una VM Linux.
- Per usare questa modalità nelle VM Linux, impostare la proprietà `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` nel modello di macchina virtuale.

> [!NOTE]
>Per le macchine virtuali Windows, `osProfile.windowsConfiguration.enableAutomaticUpdates` attualmente la proprietà può essere impostata solo quando la VM viene creata per la prima volta. Il cambio da manuale a modalità automatica o da una modalità automatica a una modalità manuale non è attualmente supportato. Il cambio dalla modalità AutomaticByOS alla modalità AutomaticByPlatfom è supportato.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>Requisiti per l'abilitazione dell'applicazione automatica delle patch per guest VM

- Per la macchina virtuale deve essere installato l'agente VM di Azure per [Windows](./extensions/agent-windows.md) o [Linux](./extensions/agent-linux.md) .
- Per le macchine virtuali Linux, l'agente Linux di Azure deve avere la versione 2.2.53.1 o successiva. [Aggiornare l'agente Linux](./extensions/update-linux-agent.md) se la versione corrente è precedente alla versione richiesta.
- Per le macchine virtuali Windows, è necessario che il servizio Windows Update sia in esecuzione nella macchina virtuale.
- La macchina virtuale deve essere in grado di accedere agli endpoint di aggiornamento configurati. Se la macchina virtuale è configurata per l'uso di repository privati per Linux o Windows Server Update Services (WSUS) per le macchine virtuali Windows, è necessario che gli endpoint di aggiornamento pertinenti siano accessibili.
- Usare l'API di calcolo versione 2020-12-01 o successiva. L'API di calcolo versione 2020-06-01 può essere usata per le macchine virtuali Windows con funzionalità limitate.

Per abilitare la funzionalità di anteprima, è necessario un unico consenso esplicito per le funzionalità **InGuestAutoPatchVMPreview** e **InGuestPatchVMPreview** per ogni sottoscrizione, come descritto nella sezione seguente.

### <a name="rest-api"></a>API REST
Nell'esempio seguente viene descritto come abilitare l'anteprima per la sottoscrizione:

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
Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

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

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Interfaccia della riga di comando di Azure 2.0
Usare [AZ feature Register](/cli/azure/feature#az-feature-register) per abilitare l'anteprima per la sottoscrizione.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

La registrazione delle funzionalità può richiedere fino a 15 minuti. Per verificare lo stato della registrazione:

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

Una volta registrata la funzionalità per la sottoscrizione, completare il processo di consenso esplicito propagando la modifica nel provider di risorse di calcolo.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>Abilitare l'applicazione automatica di patch guest alle macchine virtuali
Per abilitare l'applicazione automatica delle patch guest VM in una macchina virtuale Windows, assicurarsi che la proprietà *osProfile. windowsConfiguration. enableAutomaticUpdates* sia impostata su *true* nella definizione del modello di macchina virtuale. Questa proprietà può essere impostata solo quando si crea la macchina virtuale. Questa proprietà aggiuntiva non è applicabile per le macchine virtuali Linux.

### <a name="rest-api-for-linux-vms"></a>API REST per macchine virtuali Linux
Nell'esempio seguente viene descritto come abilitare l'applicazione automatica di patch per guest VM:

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
Nell'esempio seguente viene descritto come abilitare l'applicazione automatica di patch per guest VM:

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
Usare il cmdlet [set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) per abilitare l'applicazione automatica delle patch per guest VM durante la creazione o l'aggiornamento di una macchina virtuale.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>INTERFACCIA della riga di comando di Azure per macchine virtuali Windows
Usare [AZ VM create](/cli/azure/vm#az-vm-create) per abilitare l'applicazione automatica delle patch Guest per le VM quando si crea una nuova macchina virtuale. Nell'esempio seguente viene configurata l'applicazione automatica delle patch Guest per una macchina virtuale denominata *myVM* nel gruppo di risorse denominato *myResourceGroup*:

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

Per modificare una macchina virtuale esistente, usare il comando [AZ VM Update](/cli/azure/vm#az-vm-update)

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>Abilitazione e valutazione

> [!NOTE]
>Potrebbero essere necessarie più di tre ore per abilitare gli aggiornamenti automatici dei guest VM in una macchina virtuale, in quanto l'abilitazione viene completata durante le ore di minore traffico della macchina virtuale. Poiché la valutazione e l'installazione delle patch si verificano solo durante le ore non di punta, la macchina virtuale deve essere in esecuzione durante gli orari di minore traffico per applicare le patch.

Quando è abilitata l'applicazione automatica delle patch Guest per una macchina virtuale, un'estensione della macchina virtuale di tipo `Microsoft.CPlat.Core.LinuxPatchExtension` viene installata in una macchina virtuale Linux o un'estensione di macchina virtuale di tipo `Microsoft.CPlat.Core.WindowsPatchExtension` viene installata in una macchina virtuale Windows. Questa estensione non deve essere installata o aggiornata manualmente, perché questa estensione viene gestita dalla piattaforma Azure come parte del processo di applicazione automatica di patch per i guest VM.

Potrebbero essere necessarie più di tre ore per abilitare gli aggiornamenti automatici dei guest VM in una macchina virtuale, in quanto l'abilitazione viene completata durante le ore di minore traffico della macchina virtuale. L'estensione viene inoltre installata e aggiornata durante gli orari di minore traffico per la macchina virtuale. Se le ore di minore traffico della macchina virtuale terminano prima del completamento dell'abilitazione, il processo di abilitazione verrà ripreso durante il successivo periodo di minore disponibilità.

Gli aggiornamenti automatici sono disabilitati nella maggior parte degli scenari e l'installazione delle patch viene eseguita tramite l'estensione in futuro. Si applicano le condizioni seguenti.
- Se una macchina virtuale Windows aveva in precedenza Windows Update attivata automaticamente tramite la modalità patch AutomaticByOS, il Windows Update automatico viene disattivato per la macchina virtuale al momento dell'installazione dell'estensione.
- Per le VM Ubuntu, gli aggiornamenti automatici predefiniti vengono disabilitati automaticamente quando l'applicazione automatica delle patch Guest di VM completa l'abilitazione.
- Per RHEL, gli aggiornamenti automatici devono essere disabilitati manualmente (si tratta di un limite di anteprima). Eseguire:

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

Per verificare se l'applicazione automatica delle patch Guest per le macchine virtuali è stata completata e l'estensione per l'applicazione di patch è installata nella macchina virtuale, è possibile esaminare la visualizzazione dell'istanza della macchina virtuale. Se il processo di abilitazione è completo, l'estensione verrà installata e i risultati della valutazione per la VM saranno disponibili con `patchStatus` . È possibile accedere alla visualizzazione dell'istanza della macchina virtuale in diversi modi, come descritto di seguito.

### <a name="rest-api"></a>API REST

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
Usare il cmdlet [Get-AzVM](/powershell/module/az.compute/get-azvm) con il `-Status` parametro per accedere alla visualizzazione dell'istanza per la macchina virtuale.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell fornisce attualmente solo informazioni sull'estensione della patch. Le informazioni su `patchStatus` saranno presto disponibili anche tramite PowerShell.

### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
Usare il comando [AZ VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view) per accedere alla visualizzazione dell'istanza della macchina virtuale.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>Informazioni sullo stato delle patch per la macchina virtuale

La `patchStatus` sezione della risposta alla visualizzazione dell'istanza fornisce informazioni dettagliate sulla valutazione più recente e sull'ultima installazione della patch per la macchina virtuale.

I risultati della valutazione per la VM possono essere esaminati nella `availablePatchSummary` sezione. Una valutazione viene eseguita periodicamente per una macchina virtuale in cui è abilitata l'applicazione automatica delle patch per guest VM. Il numero di patch disponibili dopo una valutazione viene fornito con `criticalAndSecurityPatchCount` `otherPatchCount` i risultati. L'applicazione automatica delle patch Guest di VM installerà tutte le patch valutate in base alle classificazioni delle patch *critiche* e di *sicurezza* . Qualsiasi altra patch valutata verrà ignorata.

I risultati dell'installazione della patch per la macchina virtuale possono essere esaminati nella `lastPatchInstallationSummary` sezione. Questa sezione fornisce informazioni dettagliate sull'ultimo tentativo di installazione della patch nella VM, incluso il numero di patch installate, in sospeso, non riuscite o ignorate. Le patch vengono installate solo durante la finestra di manutenzione per le ore non di punta per la macchina virtuale. Le patch in sospeso e non riuscite vengono ritentate automaticamente durante la successiva finestra di manutenzione degli orari di minore traffico.

## <a name="on-demand-patch-assessment"></a>Valutazione patch su richiesta
Se l'applicazione automatica delle patch Guest per la VM è già abilitata per la macchina virtuale, viene eseguita una valutazione periodica delle patch sulla macchina virtuale durante le ore di minore traffico della macchina virtuale. Questo processo è automatico e i risultati della valutazione più recente possono essere esaminati tramite la visualizzazione dell'istanza della macchina virtuale, come descritto in precedenza in questo documento. È anche possibile attivare una valutazione delle patch su richiesta per la macchina virtuale in qualsiasi momento. La valutazione della patch può richiedere alcuni minuti per il completamento e lo stato dell'ultima valutazione viene aggiornato nella visualizzazione dell'istanza della macchina virtuale.

Per abilitare la funzionalità di anteprima, è necessario un unico consenso esplicito per la funzionalità **InGuestPatchVMPreview** per sottoscrizione. Questa funzionalità di anteprima è diversa dalla registrazione automatica delle funzionalità di applicazione delle patch per le VM effettuate in precedenza per **InGuestAutoPatchVMPreview**. L'abilitazione dell'anteprima della funzionalità aggiuntiva è un requisito separato e aggiuntivo. L'anteprima delle funzionalità per la valutazione delle patch su richiesta può essere abilitata seguendo il [processo di abilitazione dell'anteprima](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) descritto in precedenza per l'applicazione automatica delle patch Guest per macchine virtuali.

> [!NOTE]
>La valutazione patch su richiesta non attiva automaticamente l'installazione della patch. Se è stata abilitata l'applicazione automatica di patch per guest VM, le patch valutate e applicabili per la macchina virtuale verranno installate durante le ore non di punta della macchina virtuale, in seguito al processo di applicazione delle patch prima della disponibilità descritto in precedenza in questo documento.

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
Usare il comando [AZ VM valuti-patches](/cli/azure/vm#az-vm-assess-patches) per valutare le patch disponibili per la macchina virtuale.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Altre informazioni sulla creazione e la gestione di macchine virtuali Windows](./windows/tutorial-manage-vm.md)
