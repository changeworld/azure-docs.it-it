---
title: Come analizzare file di Azure
description: Questa guida descrive i dettagli su come eseguire l'analisi dei file di Azure.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/01/2020
ms.openlocfilehash: a0bd7a4cd8afafc16f05b4a37cd5723304ad931e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96554885"
---
# <a name="register-and-scan-azure-files"></a>Registra e analizza File di Azure

## <a name="supported-capabilities"></a>Funzionalità supportate

File di Azure supporta le analisi complete e incrementali per acquisire i metadati e applicare le classificazioni ai metadati, in base alle classificazioni del sistema e dei clienti.

## <a name="prerequisites"></a>Prerequisiti

- Prima di registrare le origini dati, creare un account Azure. Per altre informazioni sulla creazione di un account di competenza, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md).
- Per configurare e pianificare le analisi, è necessario essere un amministratore dell'origine dati. per informazioni dettagliate, vedere autorizzazioni per il [Catalogo](catalog-permissions.md) .

## <a name="register-an-azure-files-storage-account"></a>Registrare un account di archiviazione File di Azure

Per registrare un nuovo account di File di Azure nel Catalogo dati, eseguire le operazioni seguenti:

1. Passa alla tua Data Catalog di competenza.
1. Selezionare **centro di gestione** nel percorso di spostamento a sinistra.
1. Selezionare **origini dati** in **origini e analisi**.
1. Selezionare **+ Nuovo**.
1. In **registra origini** selezionare **file di Azure**. Selezionare **Continua**.

:::image type="content" source="media/register-scan-azure-files/register-new-data-source.png" alt-text="Registrare una nuova origine dati" border="true":::

Nella schermata **Register Sources (file di Azure)** eseguire le operazioni seguenti:

1. Immettere un **Nome** con il quale l'origine dati sarà elencata nel catalogo.
1. Scegliere come puntare all'account di archiviazione desiderato:
   1. Selezionare una **sottoscrizione di Azure**, selezionare la sottoscrizione appropriata nella casella di riepilogo a discesa **sottoscrizione di Azure** e l'account di archiviazione appropriato nella casella di riepilogo a discesa **nome account di archiviazione** .
   1. In alternativa, è possibile selezionare **invio manualmente** e immettere un endpoint servizio (URL).
1. Selezionare **Fine** per completare la registrazione dell'origine dati.

:::image type="content" source="media/register-scan-azure-files/register-sources.png" alt-text="Opzioni di registrazione delle origini" border="true":::

## <a name="set-up-authentication-for-a-scan"></a>Configurare l'autenticazione per un'analisi

Per configurare l'autenticazione per l'archiviazione File di Azure usando una chiave dell'account, seguire questa procedura:

1. Selezionare metodo di autenticazione come **chiave dell'account**.
2. Opzione seleziona **da sottoscrizione di Azure** .
3. Selezionare la sottoscrizione di Azure in cui è presente l'account File di Azure.
4. Selezionare il nome dell'account di archiviazione dall'elenco.
5. Fare clic su **Fine**.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)