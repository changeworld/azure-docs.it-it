---
title: Impostazioni di connettività di Azure sinapsi
description: Un articolo che illustra come configurare le impostazioni di connettività in Azure sinapsi Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 03/15/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ce1a4808833cbd897da17f9ad75af346538d23d1
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103473088"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Impostazioni di connettività di Azure sinapsi Analytics

Questo articolo illustra le impostazioni di connettività in Azure sinapsi Analytics e come configurarle laddove applicabile.


## <a name="connection-policy"></a>Criteri di connessione
Il criterio di connessione per sinapsi SQL in Azure sinapsi Analytics è impostato sul *valore predefinito*. Non è possibile modificare questa operazione in Azure sinapsi Analytics. Per altre informazioni sul modo in cui influiscono sulle connessioni a sinapsi SQL in Azure sinapsi Analytics, vedere [qui](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy). 

## <a name="minimal-tls-version"></a>Versione minima di TLS
Sinapsi SQL in Azure sinapsi Analytics consente le connessioni usando tutte le versioni di TLS. Non è possibile impostare la versione minima di TLS per la sinapsi SQL in Azure sinapsi Analytics.

## <a name="next-steps"></a>Passaggi successivi

Creare un'[area di lavoro di Azure Synapse](./synapse-workspace-ip-firewall.md)
