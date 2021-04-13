---
title: Aumentare automaticamente l'archiviazione - portale di Azure - Database di Azure per MariaDB
description: Questo articolo descrive come abilitare l'estensione automatica dell'archiviazione per Database di Azure per MariaDB usando portale di Azure
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 49b87648a425277f29ef2b3ebd8752e01019d3ad
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366114"
---
# <a name="auto-grow-storage-in-azure-database-for-mariadb-using-the-azure-portal"></a>Aumentare automaticamente l'archiviazione in Database di Azure per MariaDB usando il portale di Azure
Questo articolo descrive come configurare l'aumento dell'archiviazione del server di Database di Azure per MariaDB senza influire sul carico di lavoro.

Quando un server raggiunge il limite di archiviazione allocato, il server viene contrassegnato come di sola lettura. Tuttavia, se si abilita l'aumento automatico dell'archiviazione, l'archiviazione del server aumenta per supportare i dati in crescita. Per i server con meno di 100 GB di archiviazione con provisioning, le dimensioni di archiviazione di cui è stato effettuato il provisioning vengono aumentate di 5 GB non appena lo spazio di archiviazione disponibile è inferiore a 1 GB o al 10% dell'archiviazione di cui è stato effettuato il provisioning. Per i server con più di 100 GB di spazio di archiviazione di cui è stato effettuato il provisioning, le dimensioni di archiviazione con provisioning vengono aumentate del 5% quando lo spazio di archiviazione disponibile è inferiore a 10 GB delle dimensioni di archiviazione di cui è stato effettuato il provisioning. Si applicano i limiti massimi di [archiviazione specificati qui.](concepts-pricing-tiers.md#storage)

## <a name="prerequisites"></a>Prerequisiti
Per completare questa guida, è necessario:
- Un [server di Database di Azure per MariaDB](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="enable-storage-auto-grow"></a>Abilitare la crescita automatica dell'archiviazione 

Seguire questa procedura per impostare l'aumentare automatico dell'archiviazione del server MariaDB:

1. Nel [portale di Azure](https://portal.azure.com/)selezionare il server di Database di Azure per MariaDB esistente.

2. Nell'intestazione Impostazioni della pagina del server MariaDB **fare** clic su **Piano tariffario** per aprire la pagina piano tariffario.

3. Nella sezione Aumento automatico selezionare **Sì** per abilitare l'aumento automatico dell'archiviazione.

    ![Database di Azure per MariaDB - Settings_Pricing_tier - Aumento automatico](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Fare clic su **OK** per salvare le modifiche.

5. Una notifica conferma che la crescita automatica è stata abilitata correttamente.

    ![Database di Azure per MariaDB : successo della crescita automatica](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su [come creare avvisi sulle metriche](howto-alert-metric.md).
