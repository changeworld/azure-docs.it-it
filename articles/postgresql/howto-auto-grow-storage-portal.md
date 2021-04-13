---
title: Aumentare automaticamente lo spazio di portale di Azure - Database di Azure per PostgreSQL - Server singolo
description: Questo articolo descrive come configurare l'aumentare automatico delle dimensioni di archiviazione usando l'portale di Azure in Database di Azure per PostgreSQL - Server singolo
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 5/29/2019
ms.openlocfilehash: 21cdde40730a037b9d535dfe3c608d6bbc90276f
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366131"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Aumentare automaticamente lo spazio di archiviazione portale di Azure database di Azure per PostgreSQL - Server singolo
Questo articolo descrive come configurare l'aumento dell'archiviazione di un server di Database di Azure per PostgreSQL senza alcun impatto sul carico di lavoro.

Quando un server raggiunge il limite di archiviazione allocato, il server viene contrassegnato come di sola lettura. Tuttavia, se si abilita l'aumento automatico delle dimensioni di archiviazione, lo spazio di archiviazione del server aumenta per contenere i dati in crescita. Per i server con meno di 100 GB di spazio di archiviazione con provisioning, le dimensioni di archiviazione di cui è stato effettuato il provisioning vengono aumentate di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o al 10% dello spazio di archiviazione di cui è stato effettuato il provisioning. Per i server con più di 100 GB di spazio di archiviazione con provisioning, le dimensioni di archiviazione di cui è stato effettuato il provisioning aumentano del 5% quando lo spazio di archiviazione disponibile è inferiore a 10 GB delle dimensioni di archiviazione di cui è stato effettuato il provisioning. Si applicano i limiti di archiviazione massimi [specificati qui.](./concepts-pricing-tiers.md#storage)

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [server di Database di Azure per PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="enable-storage-auto-grow"></a>Abilitare l'aumentare automatico delle dimensioni di archiviazione 

Seguire questa procedura per impostare l'aumentare automatico delle dimensioni di archiviazione del server PostgreSQL:

1. Nella finestra [portale di Azure](https://portal.azure.com/)selezionare il server di Database di Azure per PostgreSQL esistente.

2. Nella pagina del server PostgreSQL, in **Impostazioni,** fare clic su **Piano tariffario** per aprire la pagina del piano tariffario.

3. Nella sezione **Aumento automatico selezionare** Sì per abilitare l'aumento automatico delle dimensioni di archiviazione. 

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Database di Azure per PostgreSQL - Settings_Pricing_tier - Aumento automatico":::

4. Fare clic su **OK** per salvare le modifiche.

5. Una notifica conferma che l'opzione Aumenta automaticamente è stata abilitata correttamente.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png" alt-text="Database di Azure per PostgreSQL : successo della crescita automatica":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi per le metriche.](howto-alert-on-metric.md)
