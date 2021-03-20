---
title: Disabilitare i backup geografici
description: Guida alle procedure per disabilitare i backup geografici per un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics
services: synapse-analytics
author: joannapea
manager: igorstan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 01/06/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 395d5f0697138155b0bb0c629461aada9e9c18c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98739117"
---
# <a name="disable-geo-backups-for-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Disabilitare i backup geografici per un pool SQL dedicato (in precedenza SQL DW) in Azure sinapsi Analytics

In questo articolo si apprenderà come disabilitare i backup geografici per il pool SQL dedicato (in precedenza SQL DW) portale di Azure.

## <a name="disable-geo-backups-through-azure-portal"></a>Disabilitare i backup geografici tramite portale di Azure

Per disabilitare i backup geografici per il pool SQL dedicato (in precedenza SQL DW), seguire questa procedura:

> [!NOTE]
> Se si disabilitano i backup geografici, non sarà più possibile ripristinare il pool SQL dedicato (in precedenza SQL DW) in un'altra area di Azure. 
>

1. Accedere al proprio account di [portale di Azure](https://portal.azure.com/) .
1. Selezionare la risorsa pool SQL dedicato (in precedenza SQL DW) per la quale si desidera disabilitare i backup geografici. 
1. In **Impostazioni** nel pannello di navigazione a sinistra selezionare criteri di **backup geografico**.

   ![Disabilitare il backup geografico](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-1.png)

1. Per disabilitare i backup geografici, selezionare **disattivato**. 

   ![Backup geografico disabilitato](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-2.png)

1. Selezionare *Save (Salva* ) per assicurarsi che le impostazioni vengano salvate. 

   ![Salvare le impostazioni di backup geografico](./media/sql-data-warehouse-restore-from-geo-backup/disable-geo-backup-3.png)

## <a name="next-steps"></a>Passaggi successivi

- [Ripristinare un pool SQL dedicato esistente (in precedenza SQL DW)](sql-data-warehouse-restore-active-paused-dw.md)
- [Ripristinare un pool SQL dedicato eliminato (in precedenza SQL DW)](sql-data-warehouse-restore-deleted-dw.md)
