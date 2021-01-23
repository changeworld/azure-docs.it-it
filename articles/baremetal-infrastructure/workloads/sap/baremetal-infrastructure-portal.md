---
title: Unità di istanza di BareMetal in Azure
description: Informazioni su come identificare e interagire con le unità di istanza di BareMetal tramite il portale di Azure.
ms.topic: how-to
ms.date: 1/4/2021
ms.openlocfilehash: b089b45c35ff05f10ae59f8ce793645361be1e9b
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98733264"
---
# <a name="manage-baremetal-instances-through-the-azure-portal"></a>Gestire istanze bare metal tramite il portale di Azure
 
Questo articolo illustra come il [portale di Azure](https://portal.azure.com/) Visualizza le [istanze Baremetal](baremetal-overview-architecture.md). Questo articolo illustra anche le attività che è possibile eseguire nel portale di Azure con le unità di istanza BareMetal distribuite. 
 
## <a name="register-the-resource-provider"></a>Registrare il provider di risorse
Un provider di risorse di Azure per le istanze di BareMetal offre visibilità delle istanze nel portale di Azure, attualmente in anteprima pubblica. Per impostazione predefinita, la sottoscrizione di Azure usata per le distribuzioni di istanze di BareMetal registra il provider di risorse *BareMetalInfrastructure* . Se non vengono visualizzate le unità di istanza BareMetal distribuite, è necessario registrare il provider di risorse con la sottoscrizione. 

È possibile registrare il provider di risorse dell'istanza di BareMetal in due modi:
 
* [Interfaccia della riga di comando di Azure](#azure-cli)
 
* [Azure portal](#azure-portal)
 
### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure
 
Accedere alla sottoscrizione di Azure usata per la distribuzione dell'istanza di BareMetal tramite l'interfaccia della riga di comando di Azure. È possibile registrare il provider di risorse BareMetalInfrastructure con:

```azurecli-interactive
az provider register --namespace Microsoft.BareMetalInfrastructure
```
 
Per altre informazioni, vedere l'articolo relativo ai [provider e ai tipi di risorse di Azure](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell).
 
### <a name="azure-portal"></a>Portale di Azure
 
È possibile registrare il provider di risorse BareMetalInfrastructure tramite il portale di Azure.
 
È necessario elencare la sottoscrizione nella portale di Azure e quindi fare doppio clic sulla sottoscrizione usata per distribuire le unità di istanza di BareMetal.
 
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Tutti i servizi** nel menu del portale di Azure.

1. Nella casella **Tutti i servizi** immettere **sottoscrizione** e quindi selezionare **Sottoscrizioni**.

1. Selezionare la sottoscrizione dall'elenco per visualizzarla.

1. Selezionare **provider di risorse** e immettere **BareMetalInfrastructure** nella ricerca. Il provider di risorse deve essere **registrato**, come illustrato nell'immagine.
 
>[!NOTE]
>Se il provider di risorse non è registrato, selezionare **Registra**.
 
:::image type="content" source="media/baremetal-infrastructure-portal/register-resource-provider-azure-portal.png" alt-text="Screenshot che mostra l'unità dell'istanza di BareMetal registrata":::
 
## <a name="baremetal-instance-units-in-the-azure-portal"></a>Unità di istanza di BareMetal nel portale di Azure
 
Quando si invia una richiesta di distribuzione dell'istanza di BareMetal, è necessario specificare la sottoscrizione di Azure a cui si sta eseguendo la connessione alle istanze di BareMetal. Usare la stessa sottoscrizione usata per distribuire il livello dell'applicazione che funziona con le unità di istanza di BareMetal.
 
Durante la distribuzione delle istanze di BareMetal, viene creato un nuovo [gruppo di risorse di Azure](../../../azure-resource-manager/management/manage-resources-portal.md) nella sottoscrizione di Azure usata nella richiesta di distribuzione. Questo nuovo gruppo di risorse elenca tutte le unità di istanza di BareMetal distribuite nella sottoscrizione specifica.

1. Nella portale di Azure della sottoscrizione BareMetal selezionare **gruppi di risorse**.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/view-baremetal-instance-units-azure-portal.png" alt-text="Screenshot che mostra l'elenco dei gruppi di risorse":::

1. Nell'elenco individuare il nuovo gruppo di risorse.
 
   :::image type="content" source="media/baremetal-infrastructure-portal/filter-resource-groups.png" alt-text="Screenshot che mostra l'unità di istanza di BareMetal in un elenco di gruppi di risorse filtrati" lightbox="media/baremetal-infrastructure-portal/filter-resource-groups.png":::
   
   >[!TIP]
   >È possibile filtrare la sottoscrizione usata per distribuire l'istanza di BareMetal. Dopo aver filtrato la sottoscrizione appropriata, è possibile che si disponga di un lungo elenco di gruppi di risorse. Cercarne uno con una post-correzione di **-tXXX** , dove xxx è costituito da tre cifre, ad esempio **-T250**.

1. Selezionare il nuovo gruppo di risorse per visualizzarne i dettagli. L'immagine mostra un'unità di istanza di BareMetal distribuita.
   
   >[!NOTE]
   >Se sono stati distribuiti più tenant di istanza di BareMetal nella stessa sottoscrizione di Azure, verranno visualizzati più gruppi di risorse di Azure.
 
## <a name="view-the-attributes-of-a-single-instance"></a>Visualizzare gli attributi di una singola istanza
 
È possibile visualizzare i dettagli di una singola unità. Nell'elenco dell'istanza di BareMetal selezionare la singola istanza che si desidera visualizzare.
 
:::image type="content" source="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png" alt-text="Screenshot che Mostra gli attributi di unità dell'istanza di BareMetal di una singola istanza" lightbox="media/baremetal-infrastructure-portal/view-attributes-single-baremetal-instance.png":::
 
Gli attributi nell'immagine non hanno un aspetto molto diverso rispetto agli attributi della macchina virtuale (VM) di Azure. A sinistra verranno visualizzati il gruppo di risorse, l'area di Azure e il nome e l'ID della sottoscrizione. Se sono stati assegnati tag, verranno visualizzati anche qui. Per impostazione predefinita, alle unità di istanza di BareMetal non sono assegnati tag.
 
A destra, verranno visualizzati il nome, il sistema operativo, l'indirizzo IP e lo SKU dell'unità che mostra il numero di thread CPU e la memoria. Verrà visualizzata anche la versione di Power State and hardware (revisione del timbro dell'istanza di BareMetal). Lo stato di alimentazione indica se l'unità hardware è accesa o spenta. I dettagli del sistema operativo, tuttavia, non indicano se è in esecuzione.
 
Le revisioni hardware possibili sono:

* Revisione 3 (Rev 3)

* Revisione 4 (Rev 4)
 
* Revisione 4,2 (Rev 4,2)
 
>[!NOTE]
>Rev 4,2 è la versione più recente dell'infrastruttura BareMetal con l'architettura Rev 4 esistente. Il Rev 4 fornisce una prossimità più vicina agli host della macchina virtuale (VM) di Azure. Offre miglioramenti significativi nella latenza di rete tra le macchine virtuali di Azure e le unità di istanza BareMetal distribuite in indicatori o righe Rev 4. È possibile accedere alle istanze di BareMetal e gestirle tramite il portale di Azure. Per altre informazioni, vedere [infrastruttura Baremetal in Azure](baremetal-overview-architecture.md).
 
Sul lato destro è anche disponibile il nome del [gruppo di posizionamento di prossimità di Azure](../../../virtual-machines/co-location.md) , creato automaticamente per ogni unità di istanza Baremetal distribuita. Quando si distribuiscono le macchine virtuali di Azure che ospitano il livello dell'applicazione, fare riferimento al gruppo di posizionamento di prossimità. Quando si usa il gruppo di posizionamento di prossimità associato all'unità di istanza di BareMetal, si garantisce che le macchine virtuali di Azure vengano distribuite vicino all'unità di istanza di BareMetal.
 
>[!TIP]
>Per individuare il livello dell'applicazione nello stesso data center di Azure di revisione 4. x, vedere [gruppi di posizionamento di prossimità di Azure per una latenza di rete ottimale](../../../virtual-machines/workloads/sap/sap-proximity-placement-scenarios.md).
 
## <a name="check-activities-of-a-single-instance"></a>Controllare le attività di una singola istanza
 
È possibile controllare le attività di una singola unità. Una delle attività principali registrate è il riavvio dell'unità. I dati elencati includono lo stato dell'attività, il timestamp dell'attività attivata, l'ID sottoscrizione e l'utente di Azure che ha attivato l'attività.
 
:::image type="content" source="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png" alt-text="Screenshot che mostra le attività dell'unità dell'istanza di BareMetal" lightbox="media/baremetal-infrastructure-portal/check-activities-single-baremetal-instance.png":::
 
Anche le modifiche apportate ai metadati dell'unità in Azure vengono registrate nel log attività. Oltre al riavvio avviato, è possibile visualizzare l'attività di **scrittura BareMetallnstances**. Questa attività non apporta alcuna modifica all'unità dell'istanza di BareMetal, ma documenta le modifiche apportate ai metadati dell'unità in Azure.
 
Un'altra attività che viene registrata è quando si aggiunge o si elimina un [tag](../../../azure-resource-manager/management/tag-resources.md) a un'istanza di.
 
## <a name="add-and-delete-an-azure-tag-to-an-instance"></a>Aggiungere ed eliminare un tag di Azure a un'istanza
 
È possibile aggiungere i tag di Azure a un'unità di istanza di BareMetal o eliminarli. Il modo in cui i tag vengono assegnati non è diverso dall'assegnazione di tag alle macchine virtuali. Come per le macchine virtuali, i tag sono presenti nei metadati di Azure e, per le istanze di BareMetal, hanno le stesse restrizioni dei tag per le macchine virtuali.
 
L'eliminazione dei tag funziona allo stesso modo delle macchine virtuali. L'applicazione e l'eliminazione di un tag sono elencate nel log attività dell'unità dell'istanza di BareMetal.
 
## <a name="check-properties-of-an-instance"></a>Controllare le proprietà di un'istanza
 
Quando si acquisiscono le istanze, è possibile passare alla sezione proprietà per visualizzare i dati raccolti sulle istanze. I dati raccolti includono connettività di Azure, back-end di archiviazione, ID del circuito ExpressRoute, ID di risorsa univoco e ID sottoscrizione. Queste informazioni verranno usate nelle richieste di supporto o quando si configura la configurazione dello snapshot di archiviazione.
 
Un'altra informazione critica che verrà visualizzata è l'indirizzo IP NFS di archiviazione. Isola lo spazio di archiviazione nel **tenant** nello stack dell'istanza di Baremetal. Questo indirizzo IP verrà usato quando si modifica il [file di configurazione per i backup degli snapshot di archiviazione](../../../virtual-machines/workloads/sap/hana-backup-restore.md#set-up-storage-snapshots).
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-properties.png" alt-text="Screenshot che mostra le impostazioni delle proprietà dell'istanza di BareMetal" lightbox="media/baremetal-infrastructure-portal/baremetal-instance-properties.png":::
 
## <a name="restart-a-unit-through-the-azure-portal"></a>Riavviare un'unità tramite il portale di Azure
 
Esistono diverse situazioni in cui il sistema operativo non termina il riavvio, operazione che richiede il riavvio dell'alimentazione dell'unità di istanza di BareMetal. È possibile eseguire un riavvio dell'alimentazione dell'unità direttamente dalla portale di Azure:
 
Selezionare **Riavvia** , quindi **Sì** per confermare il riavvio dell'unità.
 
:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-instance-restart.png" alt-text="Screenshot che illustra come riavviare l'unità di istanza di BareMetal":::
 
Quando si riavvia un'unità di istanza di BareMetal, si verificherà un ritardo. Durante questo ritardo, lo **stato di alimentazione** passa dall' **avvio** all'avvio, il che significa che il sistema operativo è stato avviato completamente. Di conseguenza, dopo un riavvio non è possibile accedere all'unità non appena lo stato passa a **avviato**.
 
>[!IMPORTANT]
>A seconda della quantità di memoria nell'unità dell'istanza di BareMetal, un riavvio e un riavvio dell'hardware e del sistema operativo possono richiedere fino a un'ora.
 
## <a name="open-a-support-request-for-baremetal-instances"></a>Aprire una richiesta di supporto per le istanze di BareMetal
 
È possibile inviare richieste di supporto in modo specifico per un'unità di istanza di BareMetal.
1. In portale di Azure in **Guida e supporto tecnico** creare una **[nuova richiesta di supporto](https://rc.portal.azure.com/#create/Microsoft.Support)** e fornire le informazioni seguenti per il ticket:
 
   - **Tipo di problema:** Selezionare un tipo di problema
 
   - **Sottoscrizione:** Selezionare la sottoscrizione
 
   - **Servizio:** Infrastruttura BareMetal
 
   - **Risorsa:** Specificare il nome dell'istanza
 
   - **Riepilogo:** Fornire un riepilogo della richiesta
 
   - **Tipo di problema:** Selezionare un tipo di problema
 
   - **Sottotipo di problema:** Selezionare un sottotipo per il problema

1. Selezionare la scheda **soluzioni** per trovare una soluzione al problema. Se non si riesce a trovare una soluzione, andare al passaggio successivo.

1. Selezionare la scheda **Dettagli** e indicare se il problema è con le macchine virtuali o le unità di istanza di Baremetal. Queste informazioni consentono di indirizzare la richiesta di supporto agli specialisti corretti.

1. Indicare quando è iniziato il problema e selezionare l'area dell'istanza.

1. Fornire altri dettagli sulla richiesta e caricare un file, se necessario.

1. Selezionare **Verifica + crea** per inviare la richiesta.
 
Sono necessari fino a cinque giorni lavorativi per confermare la richiesta da un rappresentante del supporto.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui carichi di lavoro, vedere [tipi di carico di lavoro Baremetal](../../../virtual-machines/workloads/sap/get-started.md).