---
title: Come analizzare Esplora dati di Azure
description: Questa guida descrive i dettagli su come analizzare Esplora dati di Azure.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 01a1ded570d20d175b5e8eadb3e6cc8556155a85
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554968"
---
# <a name="register-and-scan-azure-data-explorer"></a>Registrare e analizzare Esplora dati di Azure

Questo articolo illustra come registrare un account di Azure Esplora dati in Azure e come configurare un'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

Azure Esplora dati supporta le analisi complete e incrementali per acquisire i metadati e lo schema. Le analisi classificano anche i dati automaticamente in base alle regole di classificazione personalizzate e di sistema.

## <a name="prerequisites"></a>Prerequisiti

- Prima di registrare le origini dati, creare un account Azure. Per altre informazioni sulla creazione di un account di competenza, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md).
- È necessario essere un amministratore dell'origine dati di competenza di Azure

## <a name="setting-up-authentication-for-a-scan"></a>Impostazione dell'autenticazione per un'analisi

È disponibile un solo modo per configurare l'autenticazione per Esplora dati di Azure:

- Entità servizio

### <a name="service-principal"></a>Entità servizio

Per utilizzare l'autenticazione basata su entità servizio per le analisi, è possibile utilizzarne una esistente o crearne una nuova. 

> [!Note]
> Se è necessario creare una nuova entità servizio, attenersi alla seguente procedura:
> 1. Passare al [portale di Azure](https://portal.azure.com).
> 1. Selezionare **Azure Active Directory** dal menu a sinistra.
> 1. Selezionare **Registrazioni per l'app**.
> 1. Selezionare **+ Registrazione nuova applicazione**.
> 1. Immettere un nome per l' **applicazione** (il nome dell'entità servizio).
> 1. Selezionare **account solo in questa directory aziendale**.
> 1. Per URI di reindirizzamento selezionare **Web** e immettere l'URL desiderato; non è necessario che sia reale o funzionante.
> 1. Selezionare **Registra**.

È necessario ottenere l'ID applicazione e il segreto dell'entità servizio:

1. Passare all'entità servizio nel [portale di Azure](https://portal.azure.com)
1. Copiare i valori **ID dell'applicazione (client)** da **Panoramica** e **segreto client** da **certificati & segreti**.
1. Passare a Key Vault
1. Selezionare **le impostazioni > segreti**
1. Selezionare **+ genera/importa** e immettere il **nome** della scelta e il **valore** come **segreto client** dall'entità servizio
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso alla propria competenza, sarà necessario [creare una nuova connessione](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) dell'insieme di credenziali delle chiavi
1. Infine, [creare una nuova credenziale](manage-credentials.md#create-a-new-credential) usando l'entità servizio per configurare l'analisi

#### <a name="granting-the-service-principal-access-to-your-azure-data-explorer-instance"></a>Concessione dell'accesso dell'entità servizio all'istanza di Esplora dati di Azure

1. Passare al portale di Azure. Quindi passare all'istanza di Azure Data Explorer.

1. Aggiungere l'entità servizio al ruolo **AllDatabasesViewer** nella scheda **autorizzazioni** , come illustrato nella schermata seguente.

    :::image type="content" source="./media/register-scan-azure-data-explorer/permissions-auth.png" alt-text="Screenshot per aggiungere un'entità servizio nelle autorizzazioni" border="true":::

## <a name="register-an-azure-data-explorer-account"></a>Registrare un account di Azure Esplora dati

Per registrare un nuovo account Azure Esplora dati (kusto) nel Catalogo dati, seguire questa procedura:

1. Passa all'account di competenza
1. Selezionare le **origini** nel percorso di spostamento a sinistra
1. Selezionare **Registra**
1. In **registra origini** selezionare **Azure Esplora dati**
1. Selezionare **Continua**

:::image type="content" source="media/register-scan-azure-data-explorer/register-new-data-source.png" alt-text="Registra nuova origine dati" border="true":::

Nella schermata **Register Sources (Azure Esplora dati (kusto))** eseguire le operazioni seguenti:

1. Immettere un **nome** con cui l'origine dati verrà elencata nel catalogo.
1. Scegliere il modo in cui si desidera puntare all'account di archiviazione desiderato:
   1. Selezionare una **sottoscrizione di Azure**, selezionare la sottoscrizione appropriata nella casella di riepilogo a discesa **sottoscrizione di Azure** e il cluster appropriato dalla casella di riepilogo a discesa **cluster** .
   1. In alternativa, è possibile selezionare **invio manualmente** e immettere un endpoint servizio (URL).
1. **Completare** la registrazione dell'origine dati.

:::image type="content" source="media/register-scan-azure-data-explorer/register-sources.png" alt-text="opzioni registra origini" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare il Catalogo dati di Azure per le competenze](how-to-browse-catalog.md)
- [Cerca nel Data Catalog di competenza di Azure](how-to-search-catalog.md)
