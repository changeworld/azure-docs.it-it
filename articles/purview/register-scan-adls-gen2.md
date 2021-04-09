---
title: Register and Scan Azure Data Lake Storage (ADLS) Gen2
description: In questa esercitazione viene descritto come eseguire l'analisi Azure Data Lake Storage Gen2.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 4b7f71b5405708cc1988fafa5ca9c4628fe0d80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98882400"
---
# <a name="register-and-scan-azure-data-lake-storage-gen2"></a>Registra e analizza Azure Data Lake Storage Gen2

Questo articolo illustra come registrare Azure Data Lake Storage Gen2 come origine dati in Azure e come configurare un'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

L'origine dati Azure Data Lake Storage Gen2 supporta le funzionalità seguenti:

- **Analisi complete e incrementali** per l'acquisizione dei metadati e della classificazione in Azure Data Lake storage Gen2

- Derivazione **tra gli** asset di dati per le attività di copia/flusso di dati ADF

## <a name="prerequisites"></a>Prerequisiti

Prima di registrare le origini dati, creare un account Azure. Per altre informazioni sulla creazione di un account di competenza, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md).

### <a name="setting-up-authentication-for-a-scan"></a>Configurazione dell'autenticazione per un'analisi

Per Azure Data Lake Storage Gen2 sono supportati i metodi di autenticazione seguenti:

- Identità gestita
- Entità servizio
- Chiave account

#### <a name="managed-identity-recommended"></a>Identità gestita (scelta consigliata)

Quando si sceglie **identità gestita**, per configurare la connessione, è prima necessario concedere all'account di competenza l'autorizzazione per analizzare l'origine dati:

1. Passare all'account di archiviazione ADLS Gen2.
1. Selezionare **Controllo di accesso (IAM)** nel menu di spostamento sinistro. 
1. Selezionare **+ Aggiungi**.
1. Impostare il **ruolo** su **lettore dati BLOB di archiviazione** e immettere il nome dell'account Azure per la competenza in **Seleziona** casella di input. Quindi selezionare **Salva** per fornire questa assegnazione di ruolo all'account Purview.

> [!Note]
> Per altri dettagli, vedere la procedura in [autorizzare l'accesso a BLOB e code usando Azure Active Directory](../storage/common/storage-auth-aad.md)

#### <a name="account-key"></a>Chiave account

Quando il metodo di autenticazione selezionato è la **chiave dell'account**, è necessario ottenere la chiave di accesso e archiviare nell'insieme di credenziali delle chiavi:

1. Passare all'account di archiviazione ADLS Gne2
1. Selezionare **le impostazioni > chiavi di accesso**
1. Copiare la *chiave* e salvarla in un punto qualsiasi per i passaggi successivi
1. Passare a Key Vault
1. Selezionare **Impostazioni > Segreti**
1. Selezionare **+ genera/importa** e immettere il **nome** e il **valore** come *chiave* dell'account di archiviazione
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso a Purview, sarà necessario [creare una nuova connessione dell'insieme di credenziali delle chiavi](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Infine, [creare una nuova credenziale](manage-credentials.md#create-a-new-credential) usando la chiave per configurare l'analisi

#### <a name="service-principal"></a>Entità servizio

È possibile usare un'entità servizio esistente o crearne una nuova. 

> [!Note]
> Se è necessario creare una nuova entità servizio, seguire questa procedura:
> 1. Passare al [portale di Azure](https://portal.azure.com).
> 1. Selezionare **Azure Active Directory** nel menu a sinistra.
> 1. Selezionare **Registrazioni per l'app**.
> 1. Selezionare **+ Registrazione nuova applicazione**.
> 1. Immettere un nome per l'**applicazione** (il nome dell'entità servizio).
> 1. Selezionare **Account solo in questa directory dell'organizzazione**.
> 1. Per l'URI di reindirizzamento selezionare **Web** e immettere un URL qualsiasi. Non deve necessariamente essere reale o funzionante.
> 1. Selezionare **Registra**.

È necessario ottenere l'ID applicazione e il segreto dell'entità servizio:

1. Passare all'entità servizio nel [portale di Azure](https://portal.azure.com)
1. Copiare il valore di **ID applicazione (client)** da **Panoramica** e quello di **Segreto client** da **Certificati e segreti**.
1. Passare a Key Vault
1. Selezionare **Impostazioni > Segreti**
1. Selezionare **+ Genera/Importa** e immettere il **Nome** scelto e il **Valore** come **Segreto client** dell'entità servizio
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso a Purview, sarà necessario [creare una nuova connessione dell'insieme di credenziali delle chiavi](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Infine, [creare nuove credenziali](manage-credentials.md#create-a-new-credential) usando l'entità servizio per configurare l'analisi

##### <a name="granting-the-service-principal-access-to-your-adls-gen2-account"></a>Concessione dell'accesso dell'entità servizio all'account ADLS Gen2

1. Passare all'account di archiviazione.
1. Selezionare **Controllo di accesso (IAM)** nel menu di spostamento sinistro. 
1. Selezionare **+ Aggiungi**.
1. Impostare il **ruolo** su **lettore dati BLOB di archiviazione** e immettere il nome dell'entità servizio o l'ID oggetto nella casella **Seleziona** input. Quindi selezionare **Save (Salva** ) per assegnare l'assegnazione di ruolo all'entità servizio.
### <a name="firewall-settings"></a>Impostazioni del firewall

> [!NOTE]
> Se il firewall è abilitato per l'account di archiviazione, è necessario usare il metodo di autenticazione dell' **identità gestita** durante la configurazione di un'analisi.

1. Passare all'account di archiviazione locali ASLs Gen2 in [portale di Azure](https://portal.azure.com)
1. Passare a **impostazioni > rete** e
1. Scegliere le **reti selezionate** in **Consenti accesso da**
1. Nella sezione **eccezioni** selezionare **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione e fare** clic su **Salva**

:::image type="content" source="./media/register-scan-adls-gen2/firewall-setting.png" alt-text="Screenshot che mostra l'impostazione del firewall":::

## <a name="register-azure-data-lake-storage-gen2-data-source"></a>Registra Azure Data Lake Storage Gen2 origine dati

Per registrare un nuovo account di ADLS Gen2 nel Catalogo dati, eseguire le operazioni seguenti:

1. Passare all'account Purview
2. Selezionare **Origini** nel riquadro di spostamento sinistro
3. Selezionare **Registra**
4. In **registra origini** selezionare **Azure Data Lake storage Gen2**
5. Selezionare **Continua**

Nella schermata **Register Sources (Azure Data Lake storage Gen2)** eseguire le operazioni seguenti:

1. Immettere un **Nome** con il quale l'origine dati sarà elencata nel catalogo.
2. Scegliere la sottoscrizione per filtrare gli account di archiviazione
3. Select a storage account (Selezionare un account di archiviazione)
4. Selezionare una raccolta o crearne una nuova (facoltativo)
5. **Completare** la registrazione dell'origine dati.

:::image type="content" source="media/register-scan-adls-gen2/register-sources.png" alt-text="Opzioni di registrazione delle origini" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)