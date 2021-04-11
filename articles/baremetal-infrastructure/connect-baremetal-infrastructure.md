---
title: Connettere istanze di infrastruttura BareMetal in Azure
description: Informazioni su come identificare e interagire con le istanze di BareMetal nel portale di Azure o nell'interfaccia della riga di comando di Azure.
ms.topic: how-to
ms.subservice: workloads
ms.date: 04/06/2021
ms.openlocfilehash: a7fdc17aa4271915f7dc02aaa2d7a688016bf892
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106579148"
---
# <a name="connect-baremetal-infrastructure-instances-in-azure"></a>Connettere istanze di infrastruttura BareMetal in Azure

Questo articolo illustra come il [portale di Azure](https://portal.azure.com/) Visualizza le [istanze Baremetal](concepts-baremetal-infrastructure-overview.md). Questo articolo illustra anche le operazioni che è possibile eseguire nel portale di Azure con le istanze di infrastruttura BareMetal distribuite. 
 
## <a name="register-the-resource-provider"></a>Registrare il provider di risorse
Un provider di risorse di Azure per le istanze di BareMetal fornisce la visibilità delle istanze nel portale di Azure. Per impostazione predefinita, la sottoscrizione di Azure usata per le distribuzioni di istanze di BareMetal registra il provider di risorse *BareMetalInfrastructure* . Se non vengono visualizzate le istanze di BareMetal distribuite, è necessario registrare il provider di risorse con la sottoscrizione. 

È possibile registrare il provider di risorse dell'istanza di BareMetal usando l'interfaccia della riga di comando portale di Azure o Azure.

### <a name="portal"></a>[Portale](#tab/azure-portal)
 
È necessario elencare la sottoscrizione nella portale di Azure e quindi fare doppio clic sulla sottoscrizione usata per distribuire le istanze di BareMetal.
 
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** nel menu del portale di Azure.

1. Nella casella **Tutti i servizi** immettere **sottoscrizione** e quindi selezionare **Sottoscrizioni**.

1. Selezionare la sottoscrizione dall'elenco sottoscrizione.

1. Selezionare **provider di risorse** e immettere **BareMetalInfrastructure** nella ricerca. Il provider di risorse deve essere **registrato**, come illustrato nell'immagine.
 
>[!NOTE]
>Se il provider di risorse non è registrato, selezionare **Registra**.
 
:::image type="content" source="media/connect-baremetal-infrastructure/register-resource-provider-azure-portal.png" alt-text="Screenshot che mostra le istanze di BareMetal registrate.":::

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per iniziare a usare interfaccia della riga di comando di Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Accedere alla sottoscrizione di Azure usata per la distribuzione dell'istanza di BareMetal tramite l'interfaccia della riga di comando di Azure. Registrare il `BareMetalInfrastructure` provider di risorse con il comando [AZ provider Register](/cli/azure/provider#az_provider_register) :

```azurecli
az provider register --namespace Microsoft.BareMetalInfrastructure
```

È possibile usare il comando [AZ provider list](/cli/azure/provider#az_provider_list) per visualizzare tutti i provider disponibili.

---

Per altre informazioni sui provider di risorse, vedere [provider e tipi di risorse di Azure](../azure-resource-manager/management/resource-providers-and-types.md).  

## <a name="baremetal-instances-in-the-azure-portal"></a>Istanze di BareMetal nel portale di Azure
 
Quando si invia una richiesta di distribuzione dell'istanza di BareMetal, è necessario specificare la sottoscrizione di Azure a cui si sta eseguendo la connessione alle istanze di BareMetal. Usare la stessa sottoscrizione usata per distribuire il livello dell'applicazione che funziona con le istanze BareMetal.
 
Durante la distribuzione delle istanze di BareMetal, viene creato un nuovo [gruppo di risorse di Azure](../azure-resource-manager/management/manage-resources-portal.md) nella sottoscrizione di Azure usata nella richiesta di distribuzione. Questo nuovo gruppo di risorse elenca tutte le istanze di BareMetal distribuite nella sottoscrizione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Nella portale di Azure della sottoscrizione BareMetal selezionare **gruppi di risorse**.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/view-baremetal-instances-azure-portal.png" alt-text="Screenshot che mostra l'elenco dei gruppi di risorse.":::

1. Nell'elenco individuare il nuovo gruppo di risorse.
 
   :::image type="content" source="media/connect-baremetal-infrastructure/filter-resource-groups.png" alt-text="Screenshot che mostra l'istanza di BareMetal in un elenco di gruppi di risorse filtrati." lightbox="media/connect-baremetal-infrastructure/filter-resource-groups.png":::
   
   >[!TIP]
   >È possibile filtrare la sottoscrizione usata per distribuire l'istanza di BareMetal. Dopo aver filtrato la sottoscrizione appropriata, è possibile che si disponga di un lungo elenco di gruppi di risorse. Cercarne uno con una post-correzione di **-tXXX** , dove xxx è costituito da tre cifre, ad esempio **-T250**.

1. Selezionare il nuovo gruppo di risorse per visualizzarne i dettagli. L'immagine mostra un'istanza di BareMetal distribuita.
   
   >[!NOTE]
   >Se sono stati distribuiti più tenant di istanza di BareMetal nella stessa sottoscrizione di Azure, vengono visualizzati più gruppi di risorse di Azure.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per visualizzare tutte le istanze di BareMetal, eseguire il comando [AZ baremetalinstance list](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_list) per il gruppo di risorse:

```azurecli
az baremetalinstance list --resource-group DSM05A-T550 –output table
```

> [!TIP]
> Il `--output` parametro è un parametro globale, disponibile per tutti i comandi. Il valore della **tabella** presenta l'output in un formato descrittivo. Per altre informazioni, vedere [formati di output per i comandi dell'interfaccia della riga di comando di Azure](/cli/azure/format-output-azure-cli)

---

## <a name="view-the-attributes-of-a-single-instance"></a>Visualizzare gli attributi di una singola istanza

È possibile visualizzare i dettagli di una singola istanza.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Nell'elenco delle istanze di BareMetal selezionare la singola istanza che si desidera visualizzare.
 
:::image type="content" source="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png" alt-text="Screenshot che Mostra gli attributi dell'istanza di BareMetal di una singola istanza." lightbox="media/connect-baremetal-infrastructure/view-attributes-single-baremetal-instance.png":::
 
Gli attributi nell'immagine non hanno un aspetto molto diverso rispetto agli attributi della macchina virtuale (VM) di Azure. A sinistra verranno visualizzati il gruppo di risorse, l'area di Azure e il nome e l'ID della sottoscrizione. Se sono stati assegnati tag, verranno visualizzati anche qui. Per impostazione predefinita, alle istanze di BareMetal non sono assegnati tag.
 
A destra, verrà visualizzato il nome dell'istanza di BareMetal, il sistema operativo, l'indirizzo IP e lo SKU che mostra il numero di thread CPU e la memoria. Verrà visualizzata anche la versione di Power State and hardware (revisione del timbro dell'istanza di BareMetal). Lo stato di alimentazione indica se l'unità hardware è accesa o spenta. I dettagli del sistema operativo, tuttavia, non indicano se è in esecuzione.
 
Le revisioni hardware possibili sono:

* Revisione 3 (Rev 3)

* Revisione 4 (Rev 4)
 
* Revisione 4,2 (Rev 4,2)
 
>[!NOTE]
>Rev 4,2 è la versione più recente dell'infrastruttura BareMetal con l'architettura Rev 4 esistente. Il Rev 4 fornisce una prossimità più vicina agli host della macchina virtuale (VM) di Azure. Offre miglioramenti significativi nella latenza di rete tra le macchine virtuali di Azure e le istanze di SAP HANA. È possibile accedere alle istanze di BareMetal e gestirle tramite il portale di Azure. Per altre informazioni, vedere [infrastruttura Baremetal in Azure](concepts-baremetal-infrastructure-overview.md).

 
Sul lato destro è anche disponibile il nome del [gruppo di posizionamento di prossimità di Azure](../virtual-machines/co-location.md) , che viene creato automaticamente per ogni istanza di Baremetal distribuita. Quando si distribuiscono le macchine virtuali di Azure che ospitano il livello dell'applicazione, fare riferimento al gruppo di posizionamento di prossimità. Quando si usa il gruppo di posizionamento di prossimità associato all'istanza di BareMetal, si garantisce che le macchine virtuali di Azure vengano distribuite vicino all'istanza di BareMetal.
 
>[!TIP]
>Per individuare il livello dell'applicazione nello stesso data center di Azure di revisione 4. x, vedere [gruppi di posizionamento di prossimità di Azure per una latenza di rete ottimale](/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios).

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per visualizzare i dettagli di un'istanza di BareMetal, eseguire il comando [AZ baremetalinstance Show](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_show) :

```azurecli
az baremetalinstance show --resource-group DSM05A-T550 --instance-name orcllabdsm01
```

Se non si è certi del nome dell'istanza, eseguire il `az baremetalinstance list` comando descritto in precedenza.

---
 
## <a name="check-activities-of-a-single-instance"></a>Controllare le attività di una singola istanza
 
È possibile controllare le attività di una singola istanza di BareMetal. Una delle attività principali registrate è il riavvio dell'istanza. I dati elencati includono lo stato dell'attività, il timestamp dell'attività attivata, l'ID sottoscrizione e l'utente di Azure che ha attivato l'attività.
 
:::image type="content" source="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png" alt-text="Screenshot che mostra le attività dell'istanza di BareMetal." lightbox="media/connect-baremetal-infrastructure/check-activities-single-baremetal-instance.png":::
 
Anche le modifiche ai metadati dell'istanza in Azure vengono registrate nel log attività. Oltre al riavvio avviato, è possibile visualizzare l'attività di **scrittura BareMetallnstances**. Questa attività non apporta alcuna modifica all'istanza di BareMetal, ma documenta le modifiche apportate ai metadati dell'unità in Azure.
 
Un'altra attività che viene registrata è quando si aggiunge o si elimina un [tag](../azure-resource-manager/management/tag-resources.md) a un'istanza di.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Aggiungere ed eliminare un tag di Azure a un'istanza

### <a name="portal"></a>[Portale](#tab/azure-portal)
 
È possibile aggiungere i tag di Azure a un'istanza di BareMetal o eliminarli. I tag vengono assegnati in modo analogo a quando si assegnano tag alle macchine virtuali. Come per le macchine virtuali, i tag sono presenti nei metadati di Azure. I tag hanno le stesse restrizioni per le istanze di BareMetal delle macchine virtuali.
 
L'eliminazione dei tag funziona anche in modo analogo alle macchine virtuali. L'applicazione e l'eliminazione di un tag sono elencate nel log attività dell'istanza di BareMetal.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

L'assegnazione di tag alle istanze di BareMetal funziona in modo analogo all'assegnazione di tag per le macchine virtuali. Come per le macchine virtuali, i tag sono presenti nei metadati di Azure. I tag hanno le stesse restrizioni per le istanze di BareMetal delle macchine virtuali.

Per aggiungere tag a un'istanza di BareMetal, eseguire il comando [AZ baremetalinstance Update](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_update) :

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --set tags.Dept=Finance tags.Status=Normal
```

Usare lo stesso comando per rimuovere un tag:

```azurecli
az baremetalinstance update --resource-group DSM05a-T550 --instance-name orcllabdsm01 --remove tags.Dept
```

---

## <a name="check-properties-of-an-instance"></a>Controllare le proprietà di un'istanza
 
Quando si acquisiscono le istanze, è possibile passare alla sezione proprietà per visualizzare i dati raccolti sulle istanze. I dati raccolti includono connettività di Azure, back-end di archiviazione, ID del circuito ExpressRoute, ID di risorsa univoco e ID sottoscrizione. Queste informazioni verranno usate nelle richieste di supporto o quando si configura la configurazione dello snapshot di archiviazione.
 
Un'altra informazione critica che verrà visualizzata è l'indirizzo IP NFS di archiviazione. Isola lo spazio di archiviazione nel **tenant** nello stack dell'istanza di Baremetal. Questo indirizzo IP verrà usato quando si modifica il [file di configurazione per i backup degli snapshot di archiviazione](../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-properties.png" alt-text="Screenshot che mostra le impostazioni delle proprietà dell'istanza di BareMetal." lightbox="media/connect-baremetal-infrastructure/baremetal-instance-properties.png":::
 
## <a name="restart-a-baremetal-instance-through-the-azure-portal"></a>Riavviare un'istanza di BareMetal tramite il portale di Azure

Esistono diverse situazioni in cui il sistema operativo non termina il riavvio, operazione che richiede il riavvio dell'istanza di BareMetal.

### <a name="portal"></a>[Portale](#tab/azure-portal)

È possibile eseguire un riavvio dell'istanza direttamente dall'portale di Azure:
 
Selezionare **Riavvia** , quindi **Sì** per confermare il riavvio.
 
:::image type="content" source="media/connect-baremetal-infrastructure/baremetal-instance-restart.png" alt-text="Screenshot che illustra come riavviare l'istanza di BareMetal.":::
 
Quando si riavvia un'istanza di BareMetal, si verificherà un ritardo. Durante questo ritardo, lo **stato di alimentazione** passa dall' **avvio** all'avvio, il che significa che il sistema operativo è stato avviato completamente. Di conseguenza, dopo il riavvio, è possibile accedere all'unità solo quando lo stato passa a **avviato**.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per riavviare un'istanza di BareMetal, usare il comando [AZ baremetalinstance restart](/cli/azure/ext/baremetal-infrastructure/baremetalinstance#ext_baremetal_infrastructure_az_baremetalinstance_restart) :

```azurecli
az baremetalinstance restart --resource-group DSM05a-T550 --instance-name orcllabdsm01
```

---

>[!IMPORTANT]
>A seconda della quantità di memoria nell'istanza di BareMetal, un riavvio e un riavvio dell'hardware e del sistema operativo possono richiedere fino a un'ora.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Aprire una richiesta di supporto per le istanze di BareMetal
 
È possibile inviare richieste di supporto in modo specifico per le istanze BareMetal.
1. In portale di Azure in **Guida e supporto tecnico** creare una **[nuova richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support)** e fornire le informazioni seguenti per il ticket:
 
   - **Tipo di problema:** Selezionare un tipo di problema.
 
   - **Sottoscrizione:** Selezionare la sottoscrizione.
 
   - **Servizio:** Infrastruttura BareMetal
 
   - **Risorsa:** Consente di specificare il nome dell'istanza.
 
   - **Riepilogo:** Fornire un riepilogo della richiesta.
 
   - **Tipo di problema:** Selezionare un tipo di problema.
 
   - **Sottotipo di problema:** Selezionare un sottotipo per il problema.

1. Selezionare la scheda **soluzioni** per trovare una soluzione al problema. Se non si riesce a trovare una soluzione, andare al passaggio successivo.

1. Selezionare la scheda **Dettagli** e indicare se il problema è con le macchine virtuali o le istanze Baremetal. Queste informazioni consentono di indirizzare la richiesta di supporto agli specialisti corretti.

1. Indicare quando è iniziato il problema e selezionare l'area dell'istanza.

1. Fornire altri dettagli sulla richiesta e caricare un file, se necessario.

1. Selezionare **Verifica + crea** per inviare la richiesta.
 
Sono necessari fino a cinque giorni lavorativi per confermare la richiesta da un rappresentante del supporto.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui carichi di lavoro:

- [Che cos'è SAP HANA in Azure (istanze large)?](../virtual-machines/workloads/sap/hana-overview-architecture.md)
