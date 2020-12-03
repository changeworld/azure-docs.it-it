---
title: Eseguire la scansione del database SQL di Azure dietro un firewall
description: Informazioni su come analizzare le risorse protette da un firewall usando il portale di Azure per le competenze.
author: sudhandkumar
ms.author: kumarsud
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/16/2020
ms.openlocfilehash: 4e1e76aaae2951e6d65fd1ebaaa5798ff417daf9
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96552596"
---
# <a name="scan-azure-sql-db-behind-a-firewall-in-azure-purview"></a>Analizza il database SQL di Azure dietro un firewall in Azure

Questo articolo illustra come usare Azure per analizzare una risorsa dietro un firewall.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Il proprio [tenant Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com) con il proprio account Azure.

## <a name="set-up-sql-storage-behind-a-firewall"></a>Configurare l'archiviazione SQL dietro un firewall

Il primo passaggio consiste nell'aggiungere il file MSI del catalogo a un database SQL di Azure tramite il [registro e l'analisi di un database SQL di Azure](register-scan-azure-sql-database.md).

## <a name="scan-sql-db-from-purview"></a>Analizza database SQL da competenza

1. Passare al home page di competenza.

1. Selezionare **centro di gestione** nel percorso di spostamento a sinistra.

1. Selezionare **origini dati** in **origini e analisi**.

1. Selezionare **+ nuovo** per aggiungere l'origine dati.

1. Selezionare **database SQL di Azure**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-source.png" alt-text="Schermata per la selezione di SQL Server.":::

1. Specificare un nome per la nuova origine dati, selezionare una **sottoscrizione di Azure** e selezionare la sottoscrizione e il nome del server nell'elenco a discesa.

   Selezionare **fine** per completare la registrazione. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-register.png" alt-text="Schermata per completare la selezione":::

1. Selezionare **Configura analisi** per lo spazio di archiviazione dietro il firewall e test connessione. La connessione indica che ha avuto esito positivo. 

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-setscan.png" alt-text="Screenshot della selezione di configurazione T0.":::

1. Configurare la frequenza di analisi e selezionare **continua**.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-once.png" alt-text="Screenshot della selezione per avviare l'analisi SQL.":::

1.  L'analisi viene completata e lo stato viene aggiornato nell'elenco delle origini dati.

   :::image type="content" source="./media/scan-resource-firewall/scan-sql-success.png" alt-text="Screenshot dell'analisi del messaggio completata":::
   
## <a name="next-steps"></a>Passaggi successivi

Successivamente, è possibile configurare un set di regole di analisi [creare un set di regole di analisi](create-a-scan-rule-set.md) per raggruppare le analisi.
