---
title: Panoramica dei tag del servizio Firewall di Azure
description: Un tag di servizio rappresenta un gruppo di prefissi di indirizzo IP che consente di ridurre al minimo la complessità nella creazione di regole di sicurezza.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 83e9a96573bbc72e0afff61cc0f151f95b081e30
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031580"
---
# <a name="azure-firewall-service-tags"></a>Tag del servizio Firewall di Azure

Un tag di servizio rappresenta un gruppo di prefissi di indirizzo IP che consente di ridurre al minimo la complessità nella creazione di regole di sicurezza. Non è possibile creare tag di servizio personalizzati, né specificare gli indirizzi IP inclusi in un tag. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che aggiorna automaticamente il tag in caso di modifica degli indirizzi.

I tag del servizio Firewall di Azure possono essere usati nel campo di destinazione delle regole di rete. È possibile usarli al posto di indirizzi IP specifici.

## <a name="supported-service-tags"></a>Tag di servizio supportati

Per un elenco dei tag di servizio disponibili per l'uso nelle regole di rete del firewall di Azure, vedere [tag del servizio di rete virtuale](../virtual-network/service-tags-overview.md#available-service-tags) .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle regole di Firewall di Azure, vedere [Logica di elaborazione delle regole del Firewall di Azure](rule-processing.md).
