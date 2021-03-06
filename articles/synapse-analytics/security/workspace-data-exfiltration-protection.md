---
title: Protezione Exfiltration dati per le aree di lavoro di Azure sinapsi Analytics
description: Questo articolo illustra la protezione dei dati exfiltration in Azure sinapsi Analytics
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: 5fed7649353c0ef6cb222c0b0c91a5203665e9fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104598049"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Protezione Exfiltration dati per le aree di lavoro di Azure sinapsi Analytics
Questo articolo illustra la protezione dei dati exfiltration in Azure sinapsi Analytics

## <a name="securing-data-egress-from-synapse-workspaces"></a>Protezione dei dati in uscita dalle aree di lavoro sinapsi
Le aree di lavoro di Azure sinapsi Analytics supportano l'abilitazione della protezione Exfiltration dati per le aree di lavoro. Con la protezione Exfiltration, è possibile proteggersi da utenti malintenzionati che accedono alle risorse di Azure e divulgare dati sensibili a posizioni esterne all'ambito dell'organizzazione. Al momento della creazione dell'area di lavoro, è possibile scegliere di configurare l'area di lavoro con una rete virtuale gestita e una protezione aggiuntiva per i dati exfiltration. Quando si crea un'area di lavoro con una [rete virtuale gestita](./synapse-workspace-managed-vnet.md), l'integrazione dei dati e le risorse Spark vengono distribuite nella rete virtuale gestita. I pool SQL dedicati all'area di lavoro e i pool SQL senza server hanno funzionalità multi-tenant e, di conseguenza, devono esistere al di fuori della rete virtuale gestita. Per le aree di lavoro con protezione Exfiltration dati, le risorse all'interno della rete virtuale gestita comunicano sempre tramite [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md) e le risorse SQL sinapsi possono connettersi solo a risorse di Azure autorizzate (destinazioni delle connessioni di endpoint privati gestiti approvate dall'area di lavoro). 

> [!Note]
> Dopo la creazione dell'area di lavoro, non è possibile modificare la configurazione dell'area di lavoro per la rete virtuale gestita e la protezione Exfiltration dati.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>Gestione dei dati dell'area di lavoro sinapsi in uscita per le destinazioni
Dopo aver creato l'area di lavoro con la protezione Exfiltration dati abilitata, i proprietari della risorsa dell'area di lavoro possono gestire l'elenco dei tenant Azure AD approvati per l'area di lavoro. Gli utenti con le [autorizzazioni appropriate](./synapse-workspace-access-control-overview.md) per l'area di lavoro possono usare sinapsi Studio per creare richieste di connessione all'endpoint privato gestito alle risorse nei tenant Azure ad approvati dell'area di lavoro. La creazione dell'endpoint privato gestito verrà bloccata se l'utente tenta di creare una connessione all'endpoint privato a una risorsa in un tenant non approvato.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>Area di lavoro di esempio con protezione Exfiltration dati abilitata
Viene ora usato un esempio per illustrare la protezione Exfiltration dati per le aree di lavoro sinapsi. Contoso dispone di risorse di Azure nel tenant A e nel tenant B ed è necessario che queste risorse si connettano in modo sicuro. Un'area di lavoro sinapsi è stata creata nel tenant A con il tenant B aggiunto come tenant Azure AD approvato. Il diagramma mostra le connessioni agli endpoint privati negli account di archiviazione di Azure nel tenant A e nel tenant B approvati dai proprietari dell'account di archiviazione. Il diagramma mostra anche la creazione di endpoint privati bloccati. La creazione di questo endpoint privato è stata bloccata perché è destinata a un account di archiviazione di Azure nel tenant del Azure AD Fabrikam, che non è un tenant di Azure AD approvato per l'area di lavoro di contoso.

:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="Questo diagramma mostra il modo in cui viene implementata la protezione Exfiltration dati per le aree di lavoro sinapsi":::

>[!IMPORTANT]
>
> - Le risorse in tenant diversi dal tenant dell'area di lavoro non devono avere regole firewall di blocco per i pool SQL per la connessione. Le risorse all'interno della rete virtuale gestita dell'area di lavoro, ad esempio i cluster Spark, possono connettersi attraverso collegamenti privati gestiti a risorse protette da firewall.
>
> - Metastore è disabilitato nelle aree di lavoro sinapsi con rete virtuale gestita con la protezione Exfiltration dati abilitata. Non è possibile usare Spark SQL in queste aree di lavoro.
> >

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un'area di lavoro con la protezione Exfiltration dati abilitata](./how-to-create-a-workspace-with-data-exfiltration-protection.md)

Altre informazioni sulla [rete virtuale dell'area di lavoro gestita](./synapse-workspace-managed-vnet.md)

Altre informazioni sugli [endpoint privati gestiti](./synapse-workspace-managed-private-endpoints.md)

[Creare endpoint privati gestiti per le origini dati](./how-to-create-managed-private-endpoints.md)
