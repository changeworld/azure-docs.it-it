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
ms.openlocfilehash: e0d8a8e3320b49b6fbe3e8ab66c0b4569fac9afd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587934"
---
# <a name="azure-synapse-analytics-connectivity-settings"></a>Impostazioni di connettività di Azure sinapsi Analytics

Questo articolo illustra le impostazioni di connettività in Azure sinapsi Analytics e come configurarle laddove applicabile.


## <a name="connection-policy"></a>Criteri di connessione
Il criterio di connessione per sinapsi SQL in Azure sinapsi Analytics è impostato sul *valore predefinito*. Non è possibile modificare questa operazione in Azure sinapsi Analytics. Per altre informazioni sul modo in cui influiscono sulle connessioni a sinapsi SQL in Azure sinapsi Analytics, vedere [qui](../../azure-sql/database/connectivity-architecture.md#connection-policy). 

## <a name="minimal-tls-version"></a>Versione minima di TLS
Sinapsi SQL in Azure sinapsi Analytics consente le connessioni usando tutte le versioni di TLS. Non è possibile impostare la versione minima di TLS per la sinapsi SQL in Azure sinapsi Analytics.

## <a name="next-steps"></a>Passaggi successivi

Creare un'[area di lavoro di Azure Synapse](./synapse-workspace-ip-firewall.md)