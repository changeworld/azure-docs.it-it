---
title: Supporto della zona di disponibilità per Azure API Management
description: Informazioni su come migliorare la resilienza dell'istanza del servizio azure API Management in un'area abilitando la ridondanza della zona.
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 04/13/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 817aebab6af8de59071b5d767b24d15cf46d59d9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559164"
---
# <a name="availability-zone-support-for-azure-api-management"></a>Supporto della zona di disponibilità per Azure API Management 

Questo articolo illustra come abilitare la ridondanza della zona per l'istanza API Management usando il portale di Azure. [La ridondanza della](../availability-zones/az-overview.md#availability-zones) zona offre resilienza e disponibilità elevata a un'istanza del servizio in un'area (posizione) di Azure specifica. Con la ridondanza della zona, il gateway e il piano di controllo dell'istanza di API Management (API Gestione, portale per sviluppatori e configurazione Git) vengono replicati tra i data center in zone fisicamente separate, rendendolo resiliente a un errore di zona. 

API Management supporta anche distribuzioni in più [aree,](api-management-howto-deploy-multi-region.md)che consentono di ridurre la latenza delle richieste percepita dai consumer di API distribuite geograficamente e migliora la disponibilità del componente gateway se un'area passa offline. La combinazione di zone di disponibilità per la ridondanza all'interno di un'area e distribuzioni in più aree per migliorare la disponibilità del gateway in caso di interruzione a livello di area consente di migliorare sia l'affidabilità che le prestazioni dell'istanza API Management locale.

[!INCLUDE [premium.md](../../includes/api-management-availability-premium.md)]

## <a name="supported-regions"></a>Aree supportate

La configurazione API Management per la ridondanza della zona è attualmente supportata nelle aree di Azure seguenti.

* Australia orientale
* Brasile meridionale
* Canada centrale
* Central India
* Stati Uniti orientali
* Stati Uniti orientali 2
* Francia centrale
* Giappone orientale
* Stati Uniti centro-meridionali
* Asia sud-orientale
* Regno Unito meridionale
* West US 2
* Stati Uniti occidentali 3

## <a name="prerequisites"></a>Prerequisiti

* Se non è ancora stata creata un'istanza del servizio Gestione API, vedere [Creare un'istanza di Gestione API](get-started-create-service-instance.md). Selezionare il livello di servizio Premium.
* Se l'istanza API Management è distribuita in una rete [virtuale,](api-management-using-with-vnet.md)assicurarsi di configurare una rete virtuale, una subnet e un indirizzo IP pubblico in qualsiasi nuova posizione in cui si prevede di abilitare la ridondanza della zona.

## <a name="enable-zone-redundancy---portal"></a>Abilitare la ridondanza della zona - Portale

Nel portale abilitare facoltativamente la ridondanza della zona quando si aggiunge una posizione al servizio API Management o si aggiorna la configurazione di una località esistente.

1. Nel portale di Azure passare al servizio API Management e selezionare **Località** nel menu.
1. Selezionare una posizione esistente oppure **+ Aggiungi** nella barra superiore. La località deve [supportare le zone di disponibilità](#supported-regions).
1. Selezionare il numero di unità **[di scala](upgrade-and-scale.md)** nella posizione.
1. In **Zone di disponibilità** selezionare una o più zone. Il numero di unità selezionate deve essere distribuito in modo uniforme tra le zone di disponibilità. Ad esempio, se si selezionano 3 unità, selezionare 3 zone in modo che ogni zona ospiti un'unità.
1. Se l'API Management è distribuita in una [rete virtuale,](api-management-using-with-vnet.md)configurare le impostazioni della rete virtuale nel percorso. Selezionare una rete virtuale, una subnet e un indirizzo IP pubblico esistenti disponibili nel percorso.
1. Selezionare **Applica** e quindi **Salva**.

:::image type="content" source="media/zone-redundancy/add-location-zones.png" alt-text="Abilitare la ridondanza della zona":::

> [!IMPORTANT]
> L'indirizzo IP pubblico nella posizione cambia quando si abilitano, aggiungono o rimuovono le zone di disponibilità. Quando si aggiornano le zone di disponibilità in un'area con impostazioni di rete, è necessario configurare una risorsa indirizzo IP pubblico diversa da quella configurata in precedenza.

> [!NOTE]
> L'applicazione della modifica all'istanza di API Management può richiedere da 15 a 45 minuti.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [distribuzione di un'istanza del API Management azure in più aree di Azure.](api-management-howto-deploy-multi-region.md)
* È anche possibile abilitare la ridondanza della zona usando [un Azure Resource Manager .](https://github.com/Azure/azure-quickstart-templates/tree/master/101-api-management-simple-zones)
* Altre informazioni sui [servizi di Azure che supportano le zone di disponibilità.](../availability-zones/az-region.md)
* Altre informazioni sulla creazione per [l'affidabilità](/azure/architecture/framework/resiliency/overview) in Azure.