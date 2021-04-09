---
title: Creare un account nel portale di Azure
description: Informazioni su come creare un account Azure Batch nel portale di Azure per eseguire carichi di lavoro paralleli su larga scala nel cloud
ms.topic: how-to
ms.date: 02/23/2021
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 36759a0caef41af9307bf621a1b6b634ddf586cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703665"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Creare un account Batch nel portale di Azure

In questo argomento viene illustrato come creare un [account Azure batch](accounts.md) nel [portale di Azure](https://portal.azure.com), scegliendo le proprietà dell'account adatte allo scenario di calcolo. Si apprenderà anche dove trovare importanti proprietà dell'account, come le chiavi di accesso e gli URL degli account.

Per informazioni sugli account e gli scenari Batch, vedere [Flusso di lavoro del servizio Batch e risorse](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Creare un account Batch

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Dal home page selezionare **Crea una risorsa**.

1. Nella casella di ricerca immettere **servizio batch**. Selezionare **servizio batch** nei risultati, quindi selezionare **Crea**.

1. Immettere i dettagli seguenti.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="Screenshot della schermata nuovo account batch.":::

    a. **Sottoscrizione** sottoscrizione in cui creare l'account Batch. Se è presente solo una sottoscrizione, è selezionata per impostazione predefinita.

    b. **Gruppo di risorse**: selezionare un gruppo di risorse esistente per il nuovo account Batch. È possibile crearne facoltativamente uno nuovo.

    c. **Account name** (Nome dell'account): il nome scelto deve essere univoco all'interno dell'area di Azure in cui viene creato l'account. Vedere **Località** di seguito. Il nome dell'account può contenere solo caratteri minuscoli o numeri e deve avere una lunghezza di 3-24 caratteri.

    d. **Località**: area di Azure in cui creare l'account Batch. Solo le aree supportate dalla sottoscrizione e dal gruppo di risorse vengono visualizzate come opzioni.

    e. **Account di archiviazione**: un [account di archiviazione di Azure](accounts.md#azure-storage-accounts) facoltativo associato all'account batch. È possibile selezionare un account di archiviazione esistente o crearne uno nuovo. Per prestazioni ottimali, è consigliabile configurare un account di archiviazione per utilizzo generico v2.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="Screenshot delle opzioni durante la creazione di un account di archiviazione.":::

1. Se necessario, selezionare **Avanzate** per specificare il **tipo di identità**, **l'accesso alla rete pubblica** o la **modalità di allocazione pool**. Per la maggior parte degli scenari, le opzioni predefinite sono belle.

1. Selezionare **Verifica + crea**, quindi selezionare **Crea** per creare l'account.

## <a name="view-batch-account-properties"></a>Visualizzare le proprietà dell'account Batch

Dopo aver creato l'account, selezionarlo per accedere alle impostazioni e proprietà associate. È possibile accedere a tutte le impostazioni e proprietà dell'account usando il menu a sinistra.

> [!NOTE]
> Il nome dell'account Batch è il relativo ID e non può essere modificato. Per modificare il nome di un account Batch, è necessario eliminare l'account e crearne uno nuovo con il nome desiderato.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Screenshot della pagina dell'account batch nel portale di Azure.":::

quando si sviluppa un'applicazione con le [API Batch](batch-apis-tools.md#azure-accounts-for-batch-development), sono necessari un URL e una chiave dell'account per accedere alle risorse di Batch. Batch supporta anche l'autenticazione Azure Active Directory. Per visualizzare le informazioni di accesso all'account batch, selezionare **chiavi**.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Screenshot delle chiavi dell'account batch nel portale di Azure.":::

Per visualizzare il nome e le chiavi dell'account di archiviazione associato all'account Batch, selezionare **Account di archiviazione**.

Per visualizzare le [quote delle risorse](batch-quota-limit.md) valide per l'account batch, selezionare **quote**.

## <a name="additional-configuration-for-user-subscription-mode"></a>Configurazione aggiuntiva per la modalità Sottoscrizione utente

Se si sceglie di creare un account Batch in modalità Sottoscrizione utente, prima di creare l'account eseguire questi passaggi aggiuntivi.

> [!IMPORTANT]
> L'utente che crea l'account batch in modalità sottoscrizione utente deve avere l'assegnazione di ruolo Collaboratore o proprietario per la sottoscrizione in cui verrà creato l'account batch.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Consentire ad Azure Batch di accedere alla sottoscrizione (operazione occasionale)

Quando si crea il primo account Batch in modalità Sottoscrizione utente, è necessario registrare la sottoscrizione in Batch. Se è già stato fatto, passare alla sezione successiva.

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Selezionare **Altri servizi** > **Sottoscrizioni** e quindi fare clic sulla sottoscrizione che si intende usare per l'account Batch.

1. Nella pagina **Sottoscrizione** selezionare **Provider di risorse** e cercare **Microsoft.Batch**. Verificare che il provider di risorse **Microsoft.Batch** sia registrato nella sottoscrizione. In caso contrario, selezionare il collegamento **registra** nella parte superiore della schermata.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Screenshot che illustra il provider di risorse Microsoft.Batch.":::

1. Tornare alla pagina **sottoscrizione** , quindi selezionare **controllo di accesso (IAM)**  >  **assegnazioni di ruolo**  >  **Aggiungi**  >  **Aggiungi assegnazione di ruolo**.

    :::image type="content" source="media/batch-account-create-portal/subscription_iam.png" alt-text="Screenshot della pagina assegnazioni di ruolo per una sottoscrizione.":::

1. Nella pagina **Aggiungi assegnazione ruolo** selezionare il ruolo **collaboratore** o **proprietario** , quindi cercare l'API batch. Cercare **Microsoft Azure batch** o **MicrosoftAzureBatch** per trovare l'API. **ddbf3205-c6bd-46AE-8127-60eb93363864** è l'ID applicazione per l'API batch.

1. Dopo aver trovato l'API Batch, selezionarla e fare clic su **Salva**.

### <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

In modalità di sottoscrizione utente è necessario un [Azure Key Vault](../key-vault/general/overview.md) . Il Key Vault deve trovarsi nella stessa sottoscrizione e nella stessa area dell'account batch da creare.

1. Dalla home page della [portale di Azure](https://portal.azure.com)selezionare **Crea una risorsa**.
1. Nella casella di ricerca immettere **Key Vault**. Selezionare **Key Vault** nei risultati, quindi selezionare **Crea**.
1. Nella pagina **Crea** insieme di credenziali delle chiavi immettere un nome per il Key Vault e creare un nuovo gruppo di risorse nella stessa area desiderata per l'account batch. Lasciare i valori predefiniti per le impostazioni rimanenti, quindi selezionare **Crea**.

Quando si crea l'account batch in modalità sottoscrizione utente, specificare **sottoscrizione utente** come modalità di allocazione pool, selezionare il Key Vault creato e selezionare la casella per concedere l'accesso Azure Batch al Key Vault.

Se si preferisce concedere l'accesso alla Key Vault manualmente, passare alla sezione **criteri di accesso** del Key Vault e selezionare **Aggiungi criteri di accesso**. Selezionare il collegamento accanto a **Seleziona entità** e cercare **Microsoft Azure batch** (ID applicazione **ddbf3205-c6bd-46AE-8127-60eb93363864**). Selezionare tale entità, quindi configurare le **autorizzazioni segrete** usando il menu a discesa. In Azure Batch deve essere specificato un numero minimo di autorizzazioni **Get**, **List**, **Set** e **Delete**. Per gli insiemi di credenziali delle [chiavi con l'eliminazione temporanea abilitata](../key-vault/general/soft-delete-overview.md), è necessario assegnare all'Azure batch anche l'autorizzazione di **ripristino** .

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Screenshot delle selezioni delle autorizzazioni segrete per Azure Batch":::

Selezionare **Aggiungi**, quindi assicurarsi che le caselle di controllo **macchine virtuali di Azure per la distribuzione** e **Azure Resource Manager per distribuzione modello** siano selezionate per la risorsa **Key Vault** collegata. Selezionare **Salva** per eseguire il commit delle modifiche.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="Screenshot della schermata dei criteri di accesso.":::

### <a name="configure-subscription-quotas"></a>Configurare le quote di sottoscrizione

Per gli account batch di sottoscrizione utente, le [quote Core](batch-quota-limit.md) devono essere impostate manualmente. Batch Standard le quote Core non si applicano agli account in modalità di sottoscrizione utente e le [quote nella sottoscrizione](../azure-resource-manager/management/azure-subscription-service-limits.md) per i core di calcolo regionali, i core di calcolo per serie e altre risorse vengono usate e applicate.

1. Nel [portale di Azure](https://portal.azure.com) selezionare l'account Batch in modalità sottoscrizione utente per visualizzare le relative impostazioni e proprietà.
1. Scegliere **Quote** dal menu a sinistra per visualizzare e configurare le quote di core associate all'account Batch.

## <a name="other-batch-account-management-options"></a>Altre opzioni di gestione dell'account Batch

Oltre a usare il portale di Azure, è possibile creare e gestire gli account Batch con alcuni strumenti, tra cui:

- [Cmdlet di PowerShell per Batch](batch-powershell-cmdlets-get-started.md)
- [Interfaccia della riga di comando di Azure](batch-cli-get-started.md)
- [.NET per la gestione di Batch](batch-management-dotnet.md)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [Flusso di lavoro del servizio Batch e risorse primarie](batch-service-workflow-features.md), ad esempio pool, nodi, processi e attività.
- Apprendere le nozioni di base dello sviluppo di un'applicazione abilitata per Batch con la [libreria client Batch .NET](quick-run-dotnet.md) o con [Python](quick-run-python.md). Queste guide introduttive consentono di eseguire un'applicazione di esempio che usa il servizio batch per eseguire un carico di lavoro in più nodi di calcolo, usando archiviazione di Azure per la gestione temporanea e il recupero dei file del carico di lavoro.