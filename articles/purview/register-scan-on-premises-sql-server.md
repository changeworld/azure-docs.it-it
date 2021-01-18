---
title: Registrare e analizzare un'istanza di SQL Server locale
description: Questa esercitazione descrive come eseguire l'analisi di SQL Server locale usando un runtime di integrazione self-hosted.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/18/2020
ms.openlocfilehash: 0d282ee805ac61ba17ceb3ecc6a3d8179ea7b319
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2021
ms.locfileid: "98555900"
---
# <a name="register-and-scan-an-on-premises-sql-server"></a>Registrare e analizzare un'istanza di SQL Server locale

Questo articolo illustra come registrare un'origine dati di SQL Server in ambito e come configurarne un'analisi.

## <a name="supported-capabilities"></a>Funzionalità supportate

L'origine dati locale di SQL Server supporta le funzionalità seguenti:

- **Analisi complete e incrementali** per acquisire metadati e classificazione in una rete locale o in un'istanza di SQL Server installata in una macchina virtuale di Azure.

- Derivazione **tra gli** asset di dati per le attività di copia/flusso di dati ADF

L'origine dati locale di SQL Server supporta:

- ogni versione di SQL da SQL Server 2019 a SQL Server 2000

- Metodo di autenticazione: autenticazione SQL

### <a name="known-limitations"></a>Limitazioni note

Azure non supporta l'analisi delle [visualizzazioni](/sql/relational-databases/views/views) in SQL Server.

## <a name="prerequisites"></a>Prerequisiti

- Prima di registrare le origini dati, creare un account Azure. Per altre informazioni sulla creazione di un account di competenza, vedere [Guida introduttiva: creare un account Azure](create-catalog-portal.md).

- Configurare un [runtime di integrazione self-hosted](manage-integration-runtimes.md) per analizzare l'origine dati.

## <a name="setting-up-authentication-for-a-scan"></a>Configurazione dell'autenticazione per un'analisi

È disponibile un solo modo per configurare l'autenticazione per SQL Server locale:

- Autenticazione SQL

### <a name="sql-authentication"></a>Autenticazione SQL

L'identità SQL deve avere accesso al database primario. Questo percorso è `sys.databases` archiviato. Lo scanner di competenza deve enumerare per `sys.databases` trovare tutte le istanze di database SQL nel server.

#### <a name="using-an-existing-server-administrator"></a>Uso di un amministratore server esistente

Se si prevede di usare un utente amministratore del server esistente per analizzare l'istanza di SQL Server locale, verificare quanto segue:

1. `sa` non è un tipo di autenticazione di Windows.

2. L'utente a livello di server che si intende utilizzare deve disporre dei ruoli server public e sysadmin. Per verificarlo, passare a SQL Server Management Studio (SSMS), connettersi al server, passare alla sicurezza, selezionare l'account di accesso che si intende utilizzare, fare clic con il pulsante destro del mouse su **Proprietà** e quindi scegliere **ruoli server**.

   :::image type="content" source="media/register-scan-on-premises-sql-server/server-level-login.png" alt-text="Account di accesso a livello di server.":::

3. Per i database viene eseguito il mapping a un utente che dispone almeno dell'accesso a livello di db_datareader per ogni database.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping-sa.png" alt-text="mapping utente per SA.":::

#### <a name="creating-a-new-login-and-user"></a>Creazione di un nuovo account di accesso e di un utente

Se si desidera creare un nuovo account di accesso e un nuovo utente per poter eseguire la scansione di SQL Server, attenersi alla procedura seguente:

1. Passare a SQL Server Management Studio (SSMS), connettersi al server, passare a sicurezza, fare clic con il pulsante destro del mouse su account di accesso e creare un nuovo account di accesso. Assicurarsi di selezionare autenticazione SQL.

   :::image type="content" source="media/register-scan-on-premises-sql-server/create-new-login-user.png" alt-text="Creare un nuovo account di accesso e un nuovo utente.":::

2. Selezionare ruoli server nel percorso di spostamento a sinistra e selezionare sia public che sysadmin.

3. Selezionare mapping utenti nel percorso di spostamento a sinistra e selezionare tutti i database nella mappa.

   :::image type="content" source="media/register-scan-on-premises-sql-server/user-mapping.png" alt-text="mapping utente.":::

4. Fare clic su OK per salvare.

5. Passare di nuovo all'utente creato facendo clic con il pulsante destro del mouse e scegliendo **Proprietà**. Immettere una nuova password e confermarla. Selezionare "Specifica vecchia password" e immettere la vecchia password. **È necessario modificare la password non appena si crea un nuovo account di accesso.**

   :::image type="content" source="media/register-scan-on-premises-sql-server/change-password.png" alt-text="modificare la password.":::

#### <a name="storing-your-sql-login-password-in-a-key-vault-and-creating-a-credential-in-purview"></a>Archiviazione della password dell'account di accesso di SQL in un insieme di credenziali delle chiavi e creazione di credenziali in ambito

1. Passare all'insieme di credenziali delle chiavi nel portale di Azure
1. Selezionare **Impostazioni > Segreti**
1. Selezionare **+ genera/importa** e immettere il **nome** e il **valore** come *password* dell'account di accesso di SQL Server
1. Selezionare **Crea** per completare
1. Se l'insieme di credenziali delle chiavi non è ancora connesso a Purview, sarà necessario [creare una nuova connessione dell'insieme di credenziali delle chiavi](manage-credentials.md#create-azure-key-vaults-connections-in-your-azure-purview-account)
1. Infine, [creare nuove credenziali](manage-credentials.md#create-a-new-credential) usando il **nome utente** e la **password** per configurare l'analisi

## <a name="register-a-sql-server-data-source"></a>Registrare un'origine dati di SQL Server

1. Passare all'account Purview

1. In origini e analisi nel percorso di spostamento a sinistra selezionare **runtime di integrazione**. Assicurasi che sia configurato un runtime di integrazione self-hosted. Se non è configurata, seguire la procedura descritta [qui](manage-integration-runtimes.md) per creare un runtime di integrazione self-hosted per l'analisi in una macchina virtuale locale o di Azure che ha accesso alla rete locale.

1. Selezionare **Origini** nel riquadro di spostamento sinistro

1. Selezionare **Registra**

1. Selezionare **SQL Server** e **continuare**

   :::image type="content" source="media/register-scan-on-premises-sql-server/set-up-sql-data-source.png" alt-text="Configurare l'origine dati SQL.":::

5. Specificare un nome descrittivo e un endpoint server, quindi selezionare **fine** per registrare l'origine dati. Se, ad esempio, il nome di dominio completo di SQL Server è **foobar.database.Windows.NET**, immettere *foobar* come endpoint server.

[!INCLUDE [create and manage scans](includes/manage-scans.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare Azure Purview Data Catalog](how-to-browse-catalog.md)
- [Eseguire ricerche in Azure Purview Data Catalog](how-to-search-catalog.md)
