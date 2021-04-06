---
title: Come analizzare Azure Cosmos database (API SQL)
description: Questa guida descrive i dettagli su come eseguire la scansione di Azure Cosmos database (API SQL).
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/9/2020
ms.openlocfilehash: 1aaeed1973ebd15af312b722ab61938aa4271947
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97696246"
---
# <a name="register-and-scan-azure-cosmos-database-sql-api"></a>Registrare e analizzare Azure Cosmos database (API SQL)

Questo articolo illustra come registrare un account Azure Cosmos database (API SQL) in Azure e come configurare un'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

Azure Cosmos database (API SQL) supporta le analisi complete e incrementali per l'acquisizione dei metadati e dello schema. Le analisi classificano anche i dati automaticamente in base alle regole di classificazione personalizzate e di sistema.

## <a name="prerequisites"></a>Prerequisiti

- Prima di registrare le origini dati, creare un account Azure. Per altre informazioni sulla creazione di un account di competenza, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md).
- È necessario essere un amministratore dell'origine dati di competenza di Azure

## <a name="setting-up-authentication-for-a-scan"></a>Configurazione dell'autenticazione per un'analisi

È disponibile un solo modo per configurare l'autenticazione per Azure Cosmos database (API SQL):

- Chiave account
 
### <a name="account-key"></a>Chiave account

Quando il metodo di autenticazione selezionato è la **chiave dell'account**, è necessario ottenere la chiave di accesso e archiviare nell'insieme di credenziali delle chiavi:

1. Passare all'account di Cosmos DB nel portale di Azure 
1. Selezione   >  **chiavi** impostazioni 
1. Copiare la *chiave* e salvarla in un punto qualsiasi per i passaggi successivi
1. Passare a Key Vault
1. Selezionare **Impostazioni > Segreti**
1. Selezionare **+ genera/importa** e immettere il **nome** e il **valore** come *chiave* dell'account di archiviazione
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso a Purview, sarà necessario [creare una nuova connessione dell'insieme di credenziali delle chiavi](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Infine, [creare una nuova credenziale](manage-credentials.md#create-a-new-credential) usando la chiave per configurare l'analisi

## <a name="register-an-azure-cosmos-database-sql-api-account"></a>Registrare un account di Azure Cosmos database (API SQL)

Per registrare un nuovo account Azure Cosmos database (API SQL) nel Catalogo dati, seguire questa procedura:

1. Passare all'account Purview
1. Selezionare **Origini** nel riquadro di spostamento sinistro
1. Selezionare **Registra**
1. In **registra origini** selezionare **Azure Cosmos DB (API SQL)**
1. Selezionare **Continua**

:::image type="content" source="media/register-scan-azure-cosmos-database/register-new-data-source.png" alt-text="Registrare una nuova origine dati" border="true":::

Nella schermata **Register Sources (Azure Cosmos DB (API SQL))** eseguire le operazioni seguenti:

1. Immettere un **Nome** con il quale l'origine dati sarà elencata nel catalogo.
1. Scegliere come puntare all'account di archiviazione desiderato:
   1. Selezionare una **sottoscrizione di Azure**, selezionare la sottoscrizione appropriata nella casella di riepilogo a discesa **sottoscrizione di Azure** e l'account cosmosDB appropriato nella casella di riepilogo a discesa **nome account Cosmos DB** .
   1. In alternativa, è possibile selezionare **invio manualmente** e immettere un endpoint servizio (URL).
1. Selezionare **Fine** per completare la registrazione dell'origine dati.

:::image type="content" source="media/register-scan-azure-cosmos-database/register-sources.png" alt-text="Opzioni di registrazione delle origini" border="true":::


[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)
