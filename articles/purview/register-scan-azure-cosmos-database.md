---
title: Come analizzare Azure Cosmos database (API SQL)
description: Questa guida descrive i dettagli su come eseguire la scansione di Azure Cosmos database (API SQL).
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: e1d2035b787380d9b93943b92fbe81c09fc6a527
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554992"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Registrare e analizzare Azure Cosmos database (API SQL)

Questo articolo illustra come registrare un account Azure Cosmos database (API SQL) in Azure e come configurare un'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

Azure Cosmos database (API SQL) supporta le analisi complete e incrementali per l'acquisizione dei metadati e dello schema. Le analisi classificano anche i dati automaticamente in base alle regole di classificazione personalizzate e di sistema.

## <a name="prerequisites"></a>Prerequisiti

- Prima di registrare le origini dati, creare un account Azure. Per altre informazioni sulla creazione di un account di competenza, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md).
- È necessario essere un amministratore dell'origine dati di competenza di Azure

## <a name="setting-up-authentication-for-a-scan"></a>Impostazione dell'autenticazione per un'analisi

È disponibile un solo modo per configurare l'autenticazione per Azure Cosmos database (API SQL):

- Chiave account
 
### <a name="account-key"></a>Chiave account

Quando il metodo di autenticazione selezionato è la **chiave dell'account**, è necessario ottenere la chiave di accesso e archiviare nell'insieme di credenziali delle chiavi:

1. Passare all'account di Cosmos DB nel portale di Azure 
1. Selezione **Settings**  >  **chiavi** impostazioni 
1. Copiare la *chiave* e salvarla in un punto qualsiasi per i passaggi successivi
1. Passare a Key Vault
1. Selezionare **le impostazioni > segreti**
1. Selezionare **+ genera/importa** e immettere il **nome** e il **valore** come *chiave* dell'account di archiviazione
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso alla propria competenza, sarà necessario [creare una nuova connessione](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account) dell'insieme di credenziali delle chiavi
1. Infine, [creare una nuova credenziale](manage-credentials.md#create-a-new-credential) usando la chiave per configurare l'analisi

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Registrare un account di Azure Cosmos database (API SQL)

Per registrare un nuovo account Azure Cosmos database (API SQL) nel Catalogo dati, seguire questa procedura:

1. Passa all'account di competenza
1. Selezionare le **origini** nel percorso di spostamento a sinistra
1. Selezionare **Registra**
1. In **registra origini** selezionare **Azure Cosmos DB (API SQL)**
1. Selezionare **Continua**

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="Registra nuova origine dati" border="true":::

Nella schermata **Register Sources (Azure Cosmos DB (API SQL))** eseguire le operazioni seguenti:

1. Immettere un **nome** con cui l'origine dati verrà elencata nel catalogo.
1. Scegliere il modo in cui si desidera puntare all'account di archiviazione desiderato:
   1. Selezionare una **sottoscrizione di Azure**, selezionare la sottoscrizione appropriata nella casella di riepilogo a discesa **sottoscrizione di Azure** e l'account cosmosDB appropriato nella casella di riepilogo a discesa **nome account Cosmos DB** .
   1. In alternativa, è possibile selezionare **invio manualmente** e immettere un endpoint servizio (URL).
1. **Completare** la registrazione dell'origine dati.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="opzioni registra origini" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare il Catalogo dati di Azure per le competenze](how-to-browse-catalog.md)
- [Cerca nel Data Catalog di competenza di Azure](how-to-search-catalog.md)
