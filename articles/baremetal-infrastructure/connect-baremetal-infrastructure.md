---
title: Connettere istanze dell'infrastruttura BareMetal in Azure
description: Informazioni su come identificare e interagire con le istanze di BareMetal nell'portale di Azure o nell'interfaccia della riga di comando di Azure.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: e67ede85608f2a33dcc179005f0090ca2ce6a640
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107871652"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>Connettere istanze dell'infrastruttura BareMetal in Azure

Questo articolo illustra come il [portale di Azure](https://portal.azure.com/) visualizza [le istanze BareMetal.](concepts-baremetal-infrastructure-overview.md) Questo articolo illustra anche le azioni che è possibile eseguire nell'portale di Azure con le istanze dell'infrastruttura BareMetal distribuite. 
 
## <a name="register-the-resource-provider"></a>Registrare il provider di risorse
Un provider di risorse di Azure per le istanze BareMetal fornisce visibilità delle istanze nel portale di Azure. Per impostazione predefinita, la sottoscrizione di Azure in uso per le distribuzioni di istanze BareMetal registra il provider di risorse *BareMetalInfrastructure.* Se le istanze di BareMetal distribuite non vengono visualizzati, è necessario registrare il provider di risorse con la sottoscrizione. 

È possibile registrare il provider di risorse dell'istanza di BareMetal usando l'portale di Azure o l'interfaccia della riga di comando di Azure.

### <a name="portal"></a>[Portale](#tab/azure-portal)
 
È necessario elencare la sottoscrizione nel portale di Azure e quindi fare doppio clic sulla sottoscrizione usata per distribuire le istanze di BareMetal.
 
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** nel menu del portale di Azure.

1. Nella casella **Tutti i servizi** immettere **sottoscrizione** e quindi selezionare **Sottoscrizioni**.

1. Selezionare la sottoscrizione nell'elenco delle sottoscrizioni.

1. Selezionare **Provider di** risorse e immettere **BareMetalInfrastructure** nella ricerca. Il provider di risorse deve essere **Registrato**, come illustrato nell'immagine.
 
>[!NOTE]
>Se il provider di risorse non è registrato, selezionare **Registra**.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Screenshot che mostra le istanze di BareMetal registrate.":::

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per iniziare a usare l'interfaccia della riga di comando di Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Accedere alla sottoscrizione di Azure utilizzata per la distribuzione dell'istanza di BareMetal tramite l'interfaccia della riga di comando di Azure. Registrare `BareMetalInfrastructure` il provider di risorse con il comando [az provider register:](/cli/azure/provider#az_provider_register)

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

È possibile usare il [comando az provider list](/cli/azure/provider#az_provider_list) per visualizzare tutti i provider disponibili.

---

Per altre informazioni sui provider di risorse, vedere Provider [di risorse di Azure e tipi](../azure-resource-manager/management/resource-providers-and-types.md).  

## <a name="baremetal-instances-in-the-azure-portal"></a>Istanze BareMetal nel portale di Azure
 
Quando si invia una richiesta di distribuzione dell'istanza BareMetal, si specificherà la sottoscrizione di Azure che si sta connettendo alle istanze di BareMetal. Usare la stessa sottoscrizione che si usa per distribuire il livello dell'applicazione che funziona con le istanze BareMetal.
 
Durante la distribuzione delle istanze di BareMetal, viene creato un nuovo gruppo di risorse di [Azure](../azure-resource-manager/management/manage-resources-portal.md) nella sottoscrizione di Azure usata nella richiesta di distribuzione. Questo nuovo gruppo di risorse elenca tutte le istanze BareMetal distribuite in tale sottoscrizione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nella sottoscrizione BareMetal, nel portale di Azure selezionare **Gruppi di risorse.**
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Screenshot che mostra l'elenco dei gruppi di risorse.":::

1. Nell'elenco individuare il nuovo gruppo di risorse.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Screenshot che mostra l'istanza di BareMetal in un elenco di gruppi di risorse filtrato." lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >È possibile filtrare in base alla sottoscrizione usata per distribuire l'istanza di BareMetal. Dopo aver filtrato in base alla sottoscrizione appropriata, potrebbe essere visualizzato un lungo elenco di gruppi di risorse. Cercarne uno con una correzione **post-correzione di -Txxx dove** xxx è a tre cifre, ad esempio **-T250**.

1. Selezionare il nuovo gruppo di risorse per visualizzarne i dettagli. L'immagine mostra un'istanza BareMetal distribuita.
   
   >[!NOTE]
   >Se sono stati distribuiti più tenant dell'istanza BareMetal nella stessa sottoscrizione di Azure, verranno visualizzati più gruppi di risorse di Azure.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per visualizzare tutte le istanze di BareMetal, eseguire il [comando az baremetalinstance list](/cli/azure/baremetalinstance#az_baremetalinstance_list) per il gruppo di risorse:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> Il `--output` parametro è un parametro globale, disponibile per tutti i comandi. Il **valore della** tabella presenta l'output in un formato descrittivo. Per altre informazioni, vedere Formati di output per i [comandi dell'interfaccia della riga di comando di Azure.](/cli/azure/format-output-azure-cli)

---

## <a name="view-the-attributes-of-a-single-instance"></a>Visualizzare gli attributi di una singola istanza

È possibile visualizzare i dettagli di una singola istanza.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Nell'elenco delle istanze di BareMetal selezionare la singola istanza che si vuole visualizzare.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Screenshot che mostra gli attributi dell'istanza BareMetal di una singola istanza." lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
Gli attributi nell'immagine non hanno un aspetto molto diverso rispetto agli attributi delle macchine virtuali di Azure. A sinistra verranno visualizzati il gruppo di risorse, l'area di Azure e il nome e l'ID della sottoscrizione. Se sono stati assegnati tag, verranno visualizzati anche qui. Per impostazione predefinita, alle istanze BareMetal non sono assegnati tag.
 
A destra verranno visualizzati il nome dell'istanza di BareMetal, del sistema operativo( OS), dell'indirizzo IP e dello SKU che mostra il numero di thread della CPU e la memoria. Verranno visualizzati anche lo stato di alimentazione e la versione hardware (revisione del timbro dell'istanza BareMetal). Lo stato di alimentazione indica se l'unità hardware è acceso o spento. I dettagli del sistema operativo, tuttavia, non indicano se è in esecuzione.
 
Le possibili revisioni hardware sono:

* Revisione 3 (versione 3)

* Revisione 4 (versione 4)
 
* Revisione 4.2 (Versione 4.2)
 
>[!NOTE]
>Rev 4.2 è la versione più recente dell'infrastruttura BareMetal ridefinito usando l'architettura Rev 4 esistente. Rev 4 offre una prossimità più vicina agli host di macchine virtuali (VM) di Azure. Ha miglioramenti significativi nella latenza di rete tra le macchine virtuali di Azure e SAP HANA istanze. È possibile accedere e gestire le istanze di BareMetal tramite il portale di Azure. Per altre informazioni, vedere [Infrastruttura BareMetal in Azure.](concepts-baremetal-infrastructure-overview.md)

 
Sul lato destro è anche disponibile il nome del gruppo di posizionamento di prossimità di [Azure,](../virtual-machines/co-location.md) che viene creato automaticamente per ogni istanza di BareMetal distribuita. Fare riferimento al gruppo di posizionamento di prossimità quando si distribuiscono le macchine virtuali di Azure che ospitano il livello dell'applicazione. Quando si usa il gruppo di posizionamento di prossimità associato all'istanza di BareMetal, assicurarsi che le macchine virtuali di Azure siano distribuite vicino all'istanza di BareMetal.
 
>[!TIP]
>Per individuare il livello dell'applicazione nello stesso data center di Azure della revisione 4.x, vedere Gruppi di posizionamento di prossimità di Azure per una [latenza di rete ottimale.](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per visualizzare i dettagli di un'istanza BareMetal, eseguire il [comando az baremetalinstance show:](/cli/azure/baremetalinstance#az_baremetalinstance_show)

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Se non si è incerti del nome dell'istanza, eseguire il `az baremetalinstance list` comando descritto in precedenza.

---
 
## <a name="check-activities-of-a-single-instance"></a>Controllare le attività di una singola istanza
 
È possibile controllare le attività di una singola istanza BareMetal. Una delle attività principali registrate è il riavvio dell'istanza. I dati elencati includono lo stato dell'attività, il timestamp dell'attività attivata, l'ID sottoscrizione e l'utente di Azure che ha attivato l'attività.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="Screenshot che mostra le attività dell'istanza BareMetal." lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
Anche le modifiche ai metadati dell'istanza in Azure vengono registrate nel log attività. Oltre al riavvio avviato, è possibile visualizzare l'attività di **Scrittura bareMetallnstances.** Questa attività non apporta modifiche all'istanza di BareMetal, ma documenta le modifiche ai metadati dell'unità in Azure.
 
Un'altra attività che viene registrata è quando si aggiunge o si elimina un [tag](../azure-resource-manager/management/tag-resources.md) a un'istanza.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Aggiungere ed eliminare un tag di Azure a un'istanza

### <a name="portal"></a>[Portale](#tab/azure-portal)
 
È possibile aggiungere tag di Azure a un'istanza di BareMetal o eliminarli. I tag vengono assegnati come quando si assegnano tag alle macchine virtuali. Come per le macchine virtuali, i tag sono presenti nei metadati di Azure. I tag hanno le stesse restrizioni per le istanze BareMetal delle macchine virtuali.
 
Anche l'eliminazione dei tag funziona allo stesso modo delle macchine virtuali. L'applicazione e l'eliminazione di un tag sono elencate nel log attività dell'istanza di BareMetal.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

L'assegnazione di tag alle istanze BareMetal funziona come l'assegnazione di tag per le macchine virtuali. Come per le macchine virtuali, i tag sono presenti nei metadati di Azure. I tag hanno le stesse restrizioni per le istanze BareMetal delle macchine virtuali.

Per aggiungere tag a un'istanza di BareMetal, eseguire il [comando az baremetalinstance update:](/cli/azure/baremetalinstance#az_baremetalinstance_update)

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Usare lo stesso comando per rimuovere un tag:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Controllare le proprietà di un'istanza
 
Quando si acquisiscono le istanze, è possibile passare alla sezione Proprietà per visualizzare i dati raccolti sulle istanze. I dati raccolti includono la connettività di Azure, il back-end di archiviazione, l'ID circuito ExpressRoute, l'ID risorsa univoco e l'ID sottoscrizione. Queste informazioni verranno usate nelle richieste di supporto o durante la configurazione dello snapshot di archiviazione.
 
Un'altra informazione critica che verrà visualizzata è l'indirizzo IP NFS di archiviazione. Isola l'archiviazione nel **tenant nello** stack di istanze BareMetal. Questo indirizzo IP verrà utilizzato quando si modifica il file di configurazione per [i backup degli snapshot di archiviazione.](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots)
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Screenshot che mostra le impostazioni delle proprietà dell'istanza BareMetal." lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Riavviare un'istanza di BareMetal tramite il portale di Azure

Esistono diverse situazioni in cui il sistema operativo non completa un riavvio, che richiede un riavvio dell'alimentazione dell'istanza di BareMetal.

### <a name="portal"></a>[Portale](#tab/azure-portal)

È possibile eseguire un riavvio dell'istanza direttamente dal portale di Azure:
 
Selezionare **Riavvia** e quindi **Sì** per confermare il riavvio.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="Screenshot che mostra come riavviare l'istanza di BareMetal.":::
 
Quando si riavvia un'istanza di BareMetal, si verifica un ritardo. Durante questo ritardo, lo stato di alimentazione passa da **Avvio** in corso ad Avviato **,** il che significa che il sistema operativo è stato avviato completamente. Di conseguenza, dopo un riavvio, è possibile accedere all'unità solo quando lo stato passa a **Avviato.**

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per riavviare un'istanza di BareMetal, usare [il comando az baremetalinstance restart:](/cli/azure/baremetalinstance#az_baremetalinstance_restart)

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>A seconda della quantità di memoria nell'istanza di BareMetal, un riavvio e un riavvio dell'hardware e del sistema operativo possono richiedere fino a un'ora.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Aprire una richiesta di supporto per le istanze BareMetal
 
È possibile inviare richieste di supporto specifiche per le istanze BareMetal.
1. In portale di Azure, in **Guida e supporto,** creare una **[nuova richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support)** e specificare le informazioni seguenti per il ticket:
 
   - **Tipo di problema:** Selezionare un tipo di problema.
 
   - **Sottoscrizione:** Selezionare la sottoscrizione.
 
   - **Servizio:** Infrastruttura BareMetal
 
   - **Risorsa:** Specificare il nome dell'istanza.
 
   - **Riepilogo:** Fornire un riepilogo della richiesta.
 
   - **Tipo di problema:** Selezionare un tipo di problema.
 
   - **Sottotipo del problema:** Selezionare un sottotipo per il problema.

1. Selezionare la **scheda** Soluzioni per trovare una soluzione al problema. Se non è possibile trovare una soluzione, andare al passaggio successivo.

1. Selezionare la **scheda** Dettagli e specificare se il problema riguarda le macchine virtuali o le istanze BareMetal. Queste informazioni consentono di indirizzare la richiesta di supporto agli specialisti corretti.

1. Indicare quando è iniziato il problema e selezionare l'area dell'istanza.

1. Specificare altri dettagli sulla richiesta e caricare un file, se necessario.

1. Selezionare **Rivedi e crea** per inviare la richiesta.
 
Per confermare la richiesta, un rappresentante del supporto richiede fino a cinque giorni lavorativi.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui carichi di lavoro:

- [Che cos'SAP HANA in Azure (istanze Large)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
