---
title: Register and Scan Azure Data Lake Storage (ADLS) Gen1
description: Questa esercitazione descrive come analizzare i dati da Azure Data Lake Storage Gen1 in Azure.
author: shsandeep123
ms.author: sandeepshah
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: a831681f892de9f6aae50fa9a2fcf71e883fe6ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97693720"
---
# <a name="register-and-scan-azure-data-lake-storage-gen1"></a>Registra e analizza Azure Data Lake Storage Gen1

Questo articolo illustra come registrare Azure Data Lake Storage Gen1 come origine dati in Azure e come impostarne l'analisi.

> [!Note]
> Azure Data Lake Storage Gen2 è ora disponibile a livello generale. È consigliabile iniziare a usarlo oggi stesso. Per altre informazioni, vedere la [pagina del prodotto](https://azure.microsoft.com/services/storage/data-lake-storage/).

## <a name="supported-capabilities"></a>Funzionalità supportate

L'origine dati Azure Data Lake Storage Gen1 supporta le funzionalità seguenti:

- **Analisi complete e incrementali** per l'acquisizione dei metadati e della classificazione in Azure Data Lake storage Gen1

- Derivazione **tra gli** asset di dati per le attività di copia/flusso di dati ADF

## <a name="prerequisites"></a>Prerequisiti

- Prima di registrare le origini dati, creare un account Azure. Per altre informazioni sulla creazione di un account di competenza, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md).
- È necessario essere un amministratore dell'origine dati di competenza di Azure

## <a name="setting-up-authentication-for-a-scan"></a>Configurazione dell'autenticazione per un'analisi

Per Azure Data Lake Storage Gen1 sono supportati i metodi di autenticazione seguenti:

- Identità gestita
- Entità servizio

### <a name="managed-identity-recommended"></a>Identità gestita (scelta consigliata)

Per semplicità e sicurezza, è consigliabile usare l'identità del servizio gestito di competenza per l'autenticazione con l'archivio dati.

Per configurare un'analisi utilizzando l'identità del servizio gestito di Data Catalog, è innanzitutto necessario concedere all'account di competenza l'autorizzazione per l'analisi dell'origine dati. Questo passaggio deve essere eseguito *prima* di configurare l'analisi o l'analisi avrà esito negativo.

#### <a name="adding-the-data-catalog-msi-to-an-azure-data-lake-storage-gen1-account"></a>Aggiunta del Data Catalog MSI a un account di Azure Data Lake Storage Gen1

È possibile aggiungere l'identità del servizio gestito del catalogo a livello di sottoscrizione, gruppo di risorse o risorsa, a seconda di ciò che si desidera disponga di autorizzazioni di analisi.

> [!Note]
> È necessario essere un proprietario della sottoscrizione per poter aggiungere un'identità gestita in una risorsa di Azure.

1. Dal [portale di Azure](https://portal.azure.com)trovare la sottoscrizione, il gruppo di risorse o la risorsa, ad esempio un account di archiviazione Azure Data Lake storage Gen1, che si desidera consentire al catalogo di eseguire l'analisi.

2. Fare clic su **Panoramica** e quindi selezionare **Esplora dati**

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Scegli criteri di controllo di accesso":::

3. Fare clic su **accesso** nella parte superiore della finestra di spostamento

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Fare clic su accesso":::

4. Fare clic su **Aggiungi**. Aggiungere il **Catalogo di competenza** nella selezione Seleziona utente o gruppo. Selezionare autorizzazioni di **lettura** ed **esecuzione** . Assicurarsi di scegliere **questa cartella e tutti gli elementi figlio** nell'opzione Aggiungi a come illustrato nello screenshot seguente e fare clic su **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/managed-instance-authentication.png" alt-text="MSI Authentication Details"::: .

5. Se l'insieme di credenziali delle chiavi non è ancora connesso alla propria competenza, sarà necessario [creare una nuova connessione di Key Vault](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Infine, [creare nuove credenziali](manage-credentials.md#create-a-new-credential) usando l'entità servizio per configurare l'analisi
> [!Note]
> Dopo aver aggiunto l'identità del servizio gestito del catalogo nell'origine dati, attendere fino a 15 minuti per la propagazione delle autorizzazioni prima di configurare un'analisi.

Dopo circa 15 minuti, il catalogo deve avere le autorizzazioni appropriate per poter eseguire la scansione delle risorse.

### <a name="service-principal"></a>Entità servizio

Per usare un'entità servizio, è innanzitutto necessario crearne una seguendo questa procedura:

1. Passare al [portale di Azure](https://portal.azure.com).

2. Selezionare **Azure Active Directory** nel menu a sinistra.

3. Selezionare **Registrazioni per l'app**.

4. Selezionare **+ Registrazione nuova applicazione**.

5. Immettere un nome per l'**applicazione** (il nome dell'entità servizio).

6. Selezionare **Account solo in questa directory dell'organizzazione**.

7. Per **URI di reindirizzamento** selezionare **Web** e immettere l'URL desiderato; non è necessario che sia reale o funzionante.

8. Selezionare **Registra**.

9. Copiare i valori sia dal nome visualizzato che dall'ID applicazione.

#### <a name="adding-the-data-catalog-service-principal-to-an-azure-data-lake-storage-gen1-account"></a>Aggiunta dell'entità servizio Data Catalog a un account di Azure Data Lake Storage Gen1
1. Dal [portale di Azure](https://portal.azure.com)trovare la sottoscrizione, il gruppo di risorse o la risorsa, ad esempio un account di archiviazione Azure Data Lake storage Gen1, che si desidera consentire al catalogo di eseguire l'analisi.

2. Fare clic su **Panoramica** e quindi selezionare **Esplora dati**

   :::image type="content" source="./media/register-scan-adls-gen1/access-control.png" alt-text="Scegli criteri di controllo di accesso":::

3. Fare clic su **accesso** nella parte superiore della finestra di spostamento

   :::image type="content" source="./media/register-scan-adls-gen1/access.png" alt-text="Fare clic su accesso":::

4. Fare clic su **Aggiungi**. Aggiungere l' **applicazione dell'entità servizio** nella selezione Seleziona utente o gruppo. Selezionare autorizzazioni di **lettura** ed **esecuzione** . Assicurarsi di scegliere **questa cartella e tutti gli elementi figlio** nell'opzione Aggiungi a come illustrato nello screenshot seguente e fare clic su **OK** 
    :::image type="content" source="./media/register-scan-adls-gen1/service-principal-authentication.png" alt-text="Dettagli autenticazione entità servizio":::

5. Se l'insieme di credenziali delle chiavi non è ancora connesso alla propria competenza, sarà necessario [creare una nuova connessione di Key Vault](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account).

6. Infine, [creare una nuova credenziale](manage-credentials.md#create-a-new-credential) usando l'entità servizio per configurare l'analisi.

## <a name="register-azure-data-lake-storage-gen1-data-source"></a>Registra Azure Data Lake Storage Gen1 origine dati

Per registrare un nuovo account di ADLS Gen1 nel Catalogo dati, eseguire le operazioni seguenti:

1. Passa alla tua Data Catalog di competenza.
2. Selezionare **origini** nel percorso di spostamento a sinistra.
3. Selezionare **Registra**
4. In **registra origini** selezionare **Azure Data Lake storage Gen1**. 
5. Selezionare **Continua**.

Nella schermata Register Sources (Azure Data Lake Storage Gen1) eseguire le operazioni seguenti:

1. Immettere un **Nome** con il quale l'origine dati sarà elencata nel catalogo.
2. Scegliere la sottoscrizione per filtrare gli account di archiviazione
3. Select a storage account (Selezionare un account di archiviazione)
4. Selezionare una raccolta o crearne una nuova (facoltativo)
5. Completare la registrazione dell'origine dati.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)
