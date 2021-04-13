---
title: Aumentare automaticamente lo spazio di portale di Azure - Database di Azure per MySQL
description: Questo articolo descrive come abilitare l'aumentare automaticamente l'archiviazione per Database di Azure per MySQL usando portale di Azure
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: afa62d6e3ec93c18596ee5870361bf3b657619d1
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365026"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-using-the-azure-portal"></a>Aumentare automaticamente l'archiviazione in Database di Azure per MySQL usando il portale di Azure
Questo articolo descrive come configurare l'aumento dell'archiviazione del server di Database di Azure per MySQL senza influire sul carico di lavoro.

Quando un server raggiunge il limite di archiviazione allocato, il server viene contrassegnato come di sola lettura. Tuttavia, se si abilita l'aumento automatico dell'archiviazione, l'archiviazione del server aumenta per supportare i dati in crescita. Per i server con meno di 100 GB di archiviazione con provisioning, le dimensioni di archiviazione di cui è stato effettuato il provisioning vengono aumentate di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o al 10% dell'archiviazione di cui è stato effettuato il provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, le dimensioni di archiviazione con provisioning vengono aumentate del 5% quando lo spazio di archiviazione disponibile è inferiore a 10 GB delle dimensioni di archiviazione di cui è stato effettuato il provisioning. Si applicano i limiti massimi di [archiviazione specificati qui.](./concepts-pricing-tiers.md#storage)

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [server di Database di Azure per MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Abilitare la crescita automatica dello spazio di archiviazione 

Seguire questa procedura per impostare l'aumentare automatico dell'archiviazione del server MySQL:

1. Nel [portale di Azure](https://portal.azure.com/)selezionare il server di Database di Azure per MySQL esistente.

2. Nella pagina Server MySQL, **nell'intestazione Impostazioni,** fare clic su **Piano tariffario** per aprire la pagina Piano tariffario.

3. Nella sezione Aumento automatico selezionare **Sì** per abilitare l'aumento automatico dell'archiviazione.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/3-auto-grow.png" alt-text="Database di Azure per MySQL - Settings_Pricing_tier - Aumento automatico":::

4. Fare clic su **OK** per salvare le modifiche.

5. Una notifica conferma che la crescita automatica è stata abilitata correttamente.

    :::image type="content" source="./media/howto-auto-grow-storage-portal/5-auto-grow-success.png" alt-text="Database di Azure per MySQL : successo della crescita automatica":::

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi sulle metriche](howto-alert-on-metric.md).
