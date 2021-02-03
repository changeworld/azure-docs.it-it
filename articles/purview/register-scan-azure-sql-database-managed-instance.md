---
title: Registrare e analizzare Istanza gestita di database SQL di Azure
description: Questa esercitazione descrive come analizzare Istanza gestita di database SQL di Azure
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: f9791fa8932792398efdea39fe0d39a967420596
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491724"
---
# <a name="register-and-scan-an-azure-sql-database-managed-instance"></a>Registrare e analizzare un'istanza gestita di database SQL di Azure

Questo articolo illustra come registrare un'origine dati Istanza gestita di database SQL di Azure in Purview e come configurare un'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

L'origine dati Istanza gestita di database SQL di Azure supporta le funzionalità seguenti:

- **Analisi complete e incrementali** per acquisire metadati e classificazioni in Istanza gestita di database SQL di Azure.

- **Derivazione** tra asset di dati per le attività di copia e del flusso di dati di Azure Data Factory.

### <a name="known-limitations"></a>Limitazioni note

Azure Purview non supporta l'analisi di [viste](/sql/relational-databases/views/views?view=azuresqldb-mi-current&preserve-view=true) in Istanza gestita di SQL di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Creare un nuovo account Purview se non se ne ha già uno.

- [Configurare l'endpoint pubblico nell'istanza gestita di SQL di Azure](../azure-sql/managed-instance/public-endpoint-configure.md)
    > [!Note]
    > L'organizzazione deve essere in grado di consentire un endpoint pubblico, perché **gli endpoint privati non sono ancora supportati** da Purview. Se si usa un endpoint privato, l'analisi non verrà eseguita.

### <a name="setting-up-authentication-for-a-scan"></a>Configurazione dell'autenticazione per un'analisi

Configurare l'autenticazione per analizzare Istanza gestita di database SQL di Azure. Se è necessario creare una nuova autenticazione, bisogna [autorizzare l'accesso del database a Istanza gestita di database SQL](../azure-sql/database/logins-create-manage.md). Attualmente Purview supporta tre metodi di autenticazione:

- Autenticazione SQL
- Entità servizio
- Identità gestita

#### <a name="sql-authentication"></a>Autenticazione SQL

> [!Note]
> Solo l'account di accesso dell'entità di livello server (creato dal processo di provisioning) o i membri del ruolo del database `loginmanager` nel database master possono creare nuovi account di accesso. Circa **15 minuti** dopo aver concesso l'autorizzazione, l'account Purview dovrebbe avere le autorizzazioni appropriate per analizzare le risorse.

È possibile seguire le istruzioni riportate nell'articolo [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current&preserve-view=true#examples-1) per creare un account di accesso per Istanza gestita di database SQL di Azure, se non è disponibile. Per i passaggi successivi, sarà necessario specificare **nome utente** e **password**.

1. Passare all'insieme di credenziali delle chiavi nel portale di Azure
1. Selezionare **Impostazioni > Segreti**
1. Selezionare **+ Genera/Importa** e immettere il **Nome** e il **Valore** come *password* di Istanza gestita di database SQL di Azure
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso a Purview, sarà necessario [creare una nuova connessione dell'insieme di credenziali delle chiavi](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Infine, [creare nuove credenziali](manage-credentials.md#create-a-new-credential) usando il **nome utente** e la **password** per configurare l'analisi

#### <a name="service-principal-and-managed-identity"></a>Entità servizio e identità gestita

Per consentire a Purview di usare l'entità servizio per analizzare Istanza gestita di database SQL di Azure, è necessario completare diversi passaggi

##### <a name="create-or-use-an-existing-service-principal"></a>Creare un'entità servizio o usarne una esistente

> [!Note]
> Ignorare questo passaggio se si usa un'**identità gestita**

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

##### <a name="configure-azure-ad-authentication-in-the-database-account"></a>Configurare l'autenticazione di Azure AD nell'account del database

L'entità servizio o l'identità gestita deve avere l'autorizzazione per ottenere metadati per il database, gli schemi e le tabelle. Deve inoltre essere in grado di eseguire query sulle tabelle per campionare la classificazione.
- [Configurare e gestire l'autenticazione di Azure AD con Azure SQL](../azure-sql/database/authentication-aad-configure.md)
- Creare un utente di Azure AD in Istanza gestita di database SQL di Azure seguendo i prerequisiti e l'esercitazione [Creare utenti contenuti mappati a identità di Azure AD](../azure-sql/database/authentication-aad-configure.md?tabs=azure-powershell#create-contained-users-mapped-to-azure-ad-identities)
- Assegnare l'autorizzazione `db_owner` (**scelta consigliata**) all'identità

##### <a name="add-service-principal-to-key-vault-and-purviews-credential"></a>Aggiungere l'entità servizio all'insieme di credenziali delle chiavi e alle credenziali di Purview

> [!Note]
> Se si prevede di usare l'**identità gestita**, è possibile ignorare questo passaggio, perché l'identità gestita di Purview è già presente in **Purview-MSI**

È necessario ottenere l'ID applicazione e il segreto dell'entità servizio:

1. Passare all'entità servizio nel [portale di Azure](https://portal.azure.com)
1. Copiare il valore di **ID applicazione (client)** da **Panoramica** e quello di **Segreto client** da **Certificati e segreti**.
1. Passare a Key Vault
1. Selezionare **Impostazioni > Segreti**
1. Selezionare **+ Genera/Importa** e immettere il **Nome** scelto e il **Valore** come **Segreto client** dell'entità servizio
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso a Purview, sarà necessario [creare una nuova connessione dell'insieme di credenziali delle chiavi](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Infine, [creare nuove credenziali](manage-credentials.md#create-a-new-credential) usando l'entità servizio per configurare l'analisi

## <a name="register-an-azure-sql-database-managed-instance-data-source"></a>Registrare e analizzare un'origine dati Istanza gestita di database SQL di Azure

1. Passare all'account Purview

1. Selezionare **Origini** nel riquadro di spostamento sinistro

1. Selezionare **Registra**

1. Selezionare **Istanza gestita di database SQL di Azure** e quindi **Continua**

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/set-up-the-sql-data-source.png" alt-text="Configurare l'origine dati SQL":::

1. Selezionare **Immetti manualmente**

1. Specificare il **nome di dominio completo dell'endpoint pubblico** e il **numero di porta**. Quindi selezionare **Fine** per registrare l'origine dati.

    :::image type="content" source="media/register-scan-azure-sql-database-managed-instance/add-azure-sql-database-managed-instance.png" alt-text="Aggiungere Istanza gestita di database SQL di Azure":::

    ad esempio `foobar.public.123.database.windows.net,3342`

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

> [!NOTE]
> L'eliminazione dell'analisi non rimuove gli asset dalle precedenti analisi di Istanza gestita di database SQL di Azure.

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)