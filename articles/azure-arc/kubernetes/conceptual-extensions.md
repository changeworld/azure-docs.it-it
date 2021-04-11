---
title: Estensioni del cluster-Azure Arc abilitato Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Questo articolo fornisce una panoramica concettuale della funzionalità estensioni cluster di Azure Arc Enabled Kubernetes
ms.openlocfilehash: 4b9a3991b51ec45e7a64acd546c031d4241c97af
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451172"
---
# <a name="cluster-extensions-on-azure-arc-enabled-kubernetes"></a>Estensioni del cluster in Azure Arc abilitate Kubernetes

I [grafici Helm](https://helm.sh/) consentono di gestire le applicazioni Kubernetes fornendo i blocchi predefiniti necessari per definire, installare e aggiornare anche le applicazioni Kubernetes più complesse. La funzionalità di estensione del cluster cerca di basarsi sui componenti di packaging di Helm. Questa operazione fornisce un'esperienza Azure Resource Manager basata sull'installazione e la gestione del ciclo di vita delle estensioni del cluster, ad esempio monitoraggio di Azure e Azure Defender per Kubernetes. La funzionalità estensioni cluster offre i vantaggi aggiuntivi seguenti rispetto a quelli già disponibili in modalità nativa con i grafici Helm:

- Ottenere un inventario di tutti i cluster e delle estensioni installate in tali cluster.
- Usare i criteri di Azure per automatizzare la distribuzione su larga scala delle estensioni del cluster.
- Sottoscrivere i treni di rilascio di ogni estensione.
- Configurare l'aggiornamento automatico per le estensioni.
- Supporto per gli eventi di creazione dell'istanza di estensione e di gestione del ciclo di vita di Update ed Delete.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Architettura

[Architettura delle estensioni del ![ ](./media/conceptual-extensions.png) cluster ](./media/conceptual-extensions.png#lightbox)

L'istanza dell'estensione del cluster viene creata come estensione Azure Resource Manager risorsa ( `Microsoft.KubernetesConfiguration/extensions` ) nella parte superiore della risorsa Kubernetes abilitata per Azure Arc (rappresentata da `Microsoft.Kubernetes/connectedClusters` ) nell'Azure Resource Manager. La rappresentazione in Azure Resource Manager consente di creare un criterio che controlla tutte le risorse Kubernetes abilitate per Azure Arc con o senza un'estensione del cluster specifica. Una volta determinati i cluster privi di estensioni cluster con i valori di proprietà desiderati, è possibile correggere tali risorse non conformi usando criteri di Azure.

Il `config-agent` in esecuzione nel cluster tiene traccia delle risorse di estensione nuove o aggiornate nella risorsa Kubernetes abilitata per Azure Arc. Il servizio `extensions-manager` in esecuzione nel cluster estrae il grafico Helm da Azure container Registry o Microsoft container Registry e lo installa nel cluster. 

Entrambi i `config-agent` `extensions-manager` componenti e in esecuzione nel cluster gestiscono gli aggiornamenti della versione e l'eliminazione dell'istanza di estensione.

> [!NOTE]
> * `config-agent` monitora la disponibilità di risorse di estensione nuove o aggiornate nella risorsa Kubernetes abilitata per l'arco. Pertanto, gli agenti richiedono la connettività per lo stato desiderato da estrarre al cluster. Se gli agenti non sono in grado di connettersi ad Azure, la propagazione dello stato desiderato al cluster viene posticipata.
> * Le impostazioni di configurazione protette per un'estensione vengono archiviate per un massimo di 48 ore nei servizi Kubernetes abilitati per Azure Arc. Di conseguenza, se il cluster rimane disconnesso durante 48 ore dopo la creazione della risorsa di estensione in Azure, l'estensione passa dallo `Pending` stato allo `Failed` stato. Si consiglia di portare online i cluster nel modo più regolare possibile.

## <a name="next-steps"></a>Passaggi successivi

* Usare la Guida introduttiva per [connettere un cluster Kubernetes ad Azure Arc](./quickstart-connect-cluster.md).
* [Distribuire le estensioni del cluster](./extensions.md) nel cluster Kubernetes abilitato per Azure Arc.