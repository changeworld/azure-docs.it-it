---
title: Usare un pool dedicato per eseguire attività - Attività
description: Configurare un pool di calcolo dedicato (pool di agenti) nel Registro di sistema per eseguire un'Registro Azure Container attività.
ms.topic: article
ms.date: 10/12/2020
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 21db066b3f18106938d11fbd8e2cfe688c1ef276
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389554"
---
# <a name="run-an-acr-task-on-a-dedicated-agent-pool"></a>Eseguire un'attività ACR in un pool di agenti dedicato

Configurare un pool di macchine virtuali gestite da Azure (*pool* di agenti ) per abilitare l'esecuzione [delle][acr-tasks] attività Registro Azure Container in un ambiente di calcolo dedicato. Dopo aver configurato uno o più pool nel Registro di sistema, è possibile scegliere un pool per eseguire un'attività al posto dell'ambiente di calcolo predefinito del servizio.

Un pool di agenti offre:

- **Supporto della rete virtuale:** assegnare un pool di agenti a una rete virtuale di Azure, fornendo l'accesso alle risorse nella rete virtuale, ad esempio un registro contenitori, un insieme di credenziali delle chiavi o un archivio.
- **Scalabilità in base alle** esigenze: aumentare il numero di istanze in un pool di agenti per attività a elevato utilizzo di calcolo o a zero. La fatturazione si basa sull'allocazione del pool. Per informazioni dettagliate, vedere [Prezzi.](https://azure.microsoft.com/pricing/details/container-registry/)
- **Opzioni flessibili: scegliere** tra diversi livelli [di pool](#pool-tiers) e opzioni di scalabilità per soddisfare le esigenze del carico di lavoro delle attività.
- **Gestione di Azure:** i pool di attività vengono patchati e gestiti da Azure, fornendo l'allocazione riservata senza la necessità di gestire le singole macchine virtuali.

Questa funzionalità è disponibile per il livello di servizio **Premium** del registro contenitori. Per informazioni sui livelli di servizio e sui limiti del Registro di sistema, vedere Registro Azure Container [SKU][acr-tiers].

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima e [si applicano alcune limitazioni](#preview-limitations). Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive][terms-of-use]. Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.
>

## <a name="preview-limitations"></a>Limiti di anteprima

- I pool di agenti di attività supportano attualmente i nodi Linux. I nodi di Windows non sono attualmente supportati.
- I pool di agenti attività sono disponibili in anteprima nelle aree seguenti: Stati Uniti occidentali 2, Stati Uniti centro-meridionali, Stati Uniti orientali 2, Stati Uniti orientali, Stati Uniti centrali, Europa occidentale, Europa settentrionale, Canada centrale, USGov Arizona, USGov Texas e USGov Virginia.
- Per ogni registro, la quota totale predefinita di vCPU (core) è 16 per tutti i pool di agenti standard ed è 0 per i pool di agenti isolati. Aprire una richiesta [di supporto per][open-support-ticket] l'allocazione aggiuntiva.
- Non è attualmente possibile annullare l'esecuzione di un'attività in un pool di agenti.

## <a name="prerequisites"></a>Prerequisiti

* Per usare i passaggi dell'interfaccia della riga di comando di Azure in questo articolo, è necessaria la versione 2.3.1 o successiva dell'interfaccia della riga di comando di Azure. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli]. In alternativa, eseguire la procedura in [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Se non si ha già un registro contenitori, crearne [uno][create-reg-cli] (è necessario il livello Premium) in un'area di anteprima.

## <a name="pool-tiers"></a>Livelli del pool

I livelli del pool di agenti forniscono le risorse seguenti per ogni istanza nel pool.

|Livello    | Tipo  |  CPU  |Memoria (GB)  |
|---------|---------|---------|---------|
|S1     |  standard    | 2       |    3     |
|S2     |  standard    | 4       |    8     |
|S3     |  standard    | 8       |   16     |
|I6     |  isolati    | 64     |   216     |


## <a name="create-and-manage-a-task-agent-pool"></a>Creare e gestire un pool di agenti di attività

### <a name="set-default-registry-optional"></a>Impostare il Registro di sistema predefinito (facoltativo)

Per semplificare i comandi dell'interfaccia della riga di comando di Azure seguenti, impostare il registro predefinito eseguendo il [comando az configure:][az-configure]

```azurecli
az configure --defaults acr=<registryName>
```

Gli esempi seguenti presuppongono che sia stato impostato il Registro di sistema predefinito. In caso contrario, passare `--registry <registryName>` un parametro in ogni `az acr` comando.

### <a name="create-agent-pool"></a>Creare un pool di agenti

Creare un pool di agenti usando il [comando az acr agentpool create.][az-acr-agentpool-create] L'esempio seguente crea un pool S2 di livello (4 CPU/istanza). Per impostazione predefinita, il pool contiene 1 istanza.

```azurecli
az acr agentpool create \
    --name myagentpool \
    --tier S2
```

> [!NOTE]
> La creazione di un pool di agenti e di altre operazioni di gestione del pool può richiedere alcuni minuti.

### <a name="scale-pool"></a>Pool di scalabilità

Aumentare o disattivare le dimensioni del pool con [il comando az acr agentpool update.][az-acr-agentpool-update] Nell'esempio seguente il pool viene ridimensionato a 2 istanze. È possibile ridimensionare fino a 0 istanze.

```azurecli
az acr agentpool update \
    --name myagentpool \
    --count 2
```

## <a name="create-pool-in-a-virtual-network"></a>Creare un pool in una rete virtuale

### <a name="add-firewall-rules"></a>Aggiungere regole di firewall

I pool di agenti di attività richiedono l'accesso ai servizi di Azure seguenti. Le regole del firewall seguenti devono essere aggiunte a qualsiasi gruppo di sicurezza di rete esistente o route definite dall'utente.

| Direzione | Protocollo | Source (Sorgente)         | Porta di origine | Destination          | Porta Dest | Usata    |
|-----------|----------|----------------|-------------|----------------------|-----------|---------|
| In uscita  | TCP      | VirtualNetwork | Qualsiasi         | AzureKeyVault        | 443       | Predefinito |
| In uscita  | TCP      | VirtualNetwork | Qualsiasi         | Archiviazione              | 443       | Predefinito |
| In uscita  | TCP      | VirtualNetwork | Qualsiasi         | Hub eventi             | 443       | Predefinito |
| In uscita  | TCP      | VirtualNetwork | Qualsiasi         | AzureActiveDirectory | 443       | Predefinito |
| In uscita  | TCP      | VirtualNetwork | Qualsiasi         | AzureMonitor         | 443       | Predefinito |

> [!NOTE]
> Se le attività richiedono risorse aggiuntive dalla rete Internet pubblica, aggiungere le regole corrispondenti. Ad esempio, sono necessarie regole aggiuntive per eseguire un'attività di compilazione Docker che estrae le immagini di base Docker Hub o ripristina un pacchetto NuGet.

### <a name="create-pool-in-vnet"></a>Creare un pool nella rete virtuale

L'esempio seguente crea un pool di agenti nella subnet *mysubnet* della *rete myvnet*:

```azurecli
# Get the subnet ID
subnetId=$(az network vnet subnet show \
        --resource-grop myresourcegroup \
        --vnet-name myvnet \
        --name mysubnetname \
        --query id --output tsv)

az acr agentpool create \
    --name myagentpool \
    --tier S2 \
    --subnet-id $subnetId
```

## <a name="run-task-on-agent-pool"></a>Eseguire l'attività nel pool di agenti

Negli esempi seguenti viene illustrato come specificare un pool di agenti quando si esegue l'accodamento di un'attività.

> [!NOTE]
> Per usare un pool di agenti in un'attività ACR, assicurarsi che il pool contenga almeno 1 istanza.
>

### <a name="quick-task"></a>Attività rapida

Accodare un'attività rapida nel pool di agenti usando il [comando az acr build][az-acr-build] e passare il `--agent-pool` parametro :

```azurecli
az acr build \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --file Dockerfile \
    https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
```

### <a name="automatically-triggered-task"></a>Attività attivata automaticamente

Ad esempio, creare un'attività pianificata nel pool di agenti [con az acr task create,][az-acr-task-create]passando il `--agent-pool` parametro .

```azurecli
az acr task create \
    --name mytask \
    --agent-pool myagentpool \
    --image myimage:mytag \
    --schedule "0 21 * * *" \
    --file Dockerfile \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --commit-trigger-enabled false
```

Per verificare l'installazione dell'attività, eseguire [az acr task run][az-acr-task-run]:

```azurecli
az acr task run \
    --name mytask
```

### <a name="query-pool-status"></a>Stato del pool di query

Per trovare il numero di esecuzioni attualmente pianificate nel pool di agenti, eseguire [az acr agentpool show][az-acr-agentpool-show].

```azurecli
az acr agentpool show \
    --name myagentpool \
    --queue-count
```

## <a name="next-steps"></a>Passaggi successivi

Per altri esempi di compilazione e manutenzione di immagini del contenitore nel cloud, vedere la serie di esercitazioni [ACR Tasks .](container-registry-tutorial-quick-task.md)



[acr-tasks]:           container-registry-tasks-overview.md
[acr-tiers]:           container-registry-skus.md
[azure-cli]:           /cli/azure/install-azure-cli
[open-support-ticket]: https://aka.ms/acr/support/create-ticket
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[az-configure]: /cli/azure#az-configure
[az-acr-agentpool-create]: /cli/azure/acr/agentpool#az-acr-agentpool-create
[az-acr-agentpool-update]: /cli/azure/acr/agentpool#az-acr-agentpool-update
[az-acr-agentpool-show]: /cli/azure/acr/agentpool#az-acr-agentpool-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[create-reg-cli]: container-registry-get-started-azure-cli.md
