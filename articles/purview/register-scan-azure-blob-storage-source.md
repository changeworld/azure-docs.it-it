---
title: Come analizzare un BLOB di archiviazione di Azure
description: Informazioni su come eseguire l'analisi dell'archiviazione BLOB di Azure nel Catalogo dati di Azure.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: b27b46c68d018d2ddf79d284b20cc05b51640891
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880642"
---
# <a name="register-and-scan-azure-blob-storage"></a>Registrare e analizzare Archiviazione BLOB di Azure

Questo articolo illustra come registrare un account di archiviazione BLOB di Azure in ambito di competenza e configurare un'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

Archiviazione BLOB di Azure supporta analisi complete e incrementali per l'acquisizione dei metadati e dello schema. Inoltre, i dati vengono classificati automaticamente in base alle regole di classificazione personalizzate e di sistema.

## <a name="prerequisites"></a>Prerequisiti

- Prima di registrare le origini dati, creare un account Azure. Per altre informazioni sulla creazione di un account di competenza, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md).
- È necessario essere un amministratore dell'origine dati di competenza di Azure

## <a name="setting-up-authentication-for-a-scan"></a>Configurazione dell'autenticazione per un'analisi

Esistono tre modi per configurare l'autenticazione per l'archiviazione BLOB di Azure:

- Identità gestita
- Chiave account
- Entità servizio

### <a name="managed-identity-recommended"></a>Identità gestita (scelta consigliata)

Quando si sceglie **identità gestita**, per configurare la connessione, è prima necessario concedere all'account di competenza l'autorizzazione per analizzare l'origine dati:

1. Passare all'account di archiviazione.
1. Selezionare **Controllo di accesso (IAM)** nel menu di spostamento sinistro. 
1. Selezionare **+ Aggiungi**.
1. Impostare il **ruolo** su **lettore dati BLOB di archiviazione** e immettere il nome dell'account Azure per la competenza in **Seleziona** casella di input. Quindi selezionare **Salva** per fornire questa assegnazione di ruolo all'account Purview.

> [!Note]
> Per altri dettagli, vedere la procedura in [autorizzare l'accesso a BLOB e code usando Azure Active Directory](../storage/common/storage-auth-aad.md)

### <a name="account-key"></a>Chiave account

Quando il metodo di autenticazione selezionato è la **chiave dell'account**, è necessario ottenere la chiave di accesso e archiviare nell'insieme di credenziali delle chiavi:

1. Passare all'account di archiviazione
1. Selezionare **le impostazioni > chiavi di accesso**
1. Copiare la *chiave* e salvarla in un punto qualsiasi per i passaggi successivi
1. Passare a Key Vault
1. Selezionare **Impostazioni > Segreti**
1. Selezionare **+ genera/importa** e immettere il **nome** e il **valore** come *chiave* dell'account di archiviazione
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso a Purview, sarà necessario [creare una nuova connessione dell'insieme di credenziali delle chiavi](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Infine, [creare una nuova credenziale](manage-credentials.md#create-a-new-credential) usando la chiave per configurare l'analisi

### <a name="service-principal"></a>Entità servizio

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

#### <a name="granting-the-service-principal-access-to-your-blob-storage"></a>Concessione dell'accesso all'entità servizio all'archiviazione BLOB

1. Passare all'account di archiviazione.
1. Selezionare **Controllo di accesso (IAM)** nel menu di spostamento sinistro. 
1. Selezionare **+ Aggiungi**.
1. Impostare il **ruolo** su **lettore dati BLOB di archiviazione** e immettere il nome dell'entità servizio o l'ID oggetto nella casella **Seleziona** input. Quindi selezionare **Save (Salva** ) per assegnare l'assegnazione di ruolo all'entità servizio.

## <a name="firewall-settings"></a>Impostazioni del firewall

> [!NOTE]
> Se il firewall è abilitato per l'account di archiviazione, è necessario usare il metodo di autenticazione dell' **identità gestita** durante la configurazione di un'analisi.

1. Accedere all'account di archiviazione in [portale di Azure](https://portal.azure.com)
1. Passare a **impostazioni > rete** e
1. Scegliere le **reti selezionate** in **Consenti accesso da**
1. Nella sezione **Firewall** selezionare **Consenti ai servizi Microsoft attendibili di accedere a questo account di archiviazione e fare clic su** **Salva**

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="Screenshot che mostra l'impostazione del firewall":::

## <a name="register-an-azure-blob-storage-account"></a>Registrare un account di archiviazione BLOB di Azure

Per registrare un nuovo account BLOB nel Catalogo dati, eseguire le operazioni seguenti:

1. Passare all'account Purview
1. Selezionare **Origini** nel riquadro di spostamento sinistro
1. Selezionare **Registra**
1. In **registra origini** selezionare **archiviazione BLOB di Azure**
1. Selezionare **Continua**

Nella schermata **registra origini (archiviazione BLOB di Azure)** eseguire le operazioni seguenti:

1. Immettere un **Nome** con il quale l'origine dati sarà elencata nel catalogo. 
1. Scegliere la sottoscrizione per filtrare gli account di archiviazione
1. Select a storage account (Selezionare un account di archiviazione)
1. Selezionare una raccolta o crearne una nuova (facoltativo)
1. **Completare** la registrazione dell'origine dati.

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="Opzioni di registrazione delle origini" border="true":::

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)