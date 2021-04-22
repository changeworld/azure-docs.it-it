---
title: Trasferire una sottoscrizione di Azure in una directory Azure AD diversa
description: Informazioni su come trasferire una sottoscrizione di Azure e le risorse correlate note in una directory Azure Active Directory (Azure AD).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: how-to
ms.workload: identity
ms.date: 04/06/2021
ms.author: rolyon
ms.openlocfilehash: 72dc92ae211034e2a49bc77f60880f17ab15dec7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2021
ms.locfileid: "107868178"
---
# <a name="transfer-an-azure-subscription-to-a-different-azure-ad-directory"></a>Trasferire una sottoscrizione di Azure in una directory Azure AD diversa

Le organizzazioni potrebbero avere diverse sottoscrizioni di Azure. Ogni sottoscrizione è associata a una Azure Active Directory (Azure AD) specifica. Per semplificare la gestione, è possibile trasferire una sottoscrizione in un'altra Azure AD directory. Quando si trasferisce una sottoscrizione in una directory Azure AD, alcune risorse non vengono trasferite nella directory di destinazione. Ad esempio, tutte le assegnazioni di ruolo e i ruoli personalizzati  nel controllo degli accessi in base al ruolo di Azure vengono eliminati definitivamente dalla directory di origine e non vengono trasferiti nella directory di destinazione.

Questo articolo descrive i passaggi di base che è possibile seguire per trasferire una sottoscrizione in una directory Azure AD diversa e creare di nuovo alcune risorse dopo il trasferimento.

> [!NOTE]
> Per le sottoscrizioni di Azure Cloud Solution Provider (CSP), la modifica della directory Azure AD per la sottoscrizione non è supportata.

## <a name="overview"></a>Panoramica

Il trasferimento di una sottoscrizione di Azure a una directory Azure AD è un processo complesso che deve essere pianificato ed eseguito con attenzione. Molti servizi di Azure richiedono che le entità di sicurezza (identità) funzionino normalmente o gestino anche altre risorse di Azure. Questo articolo tenta di coprire la maggior parte dei servizi di Azure che dipendono principalmente dalle entità di sicurezza, ma non è completo.

> [!IMPORTANT]
> In alcuni scenari, il trasferimento di una sottoscrizione potrebbe richiedere tempi di inattività per completare il processo. È necessaria un'attenta pianificazione per valutare se saranno necessari tempi di inattività per il trasferimento.

Il diagramma seguente illustra i passaggi di base da seguire quando si trasferisce una sottoscrizione in una directory diversa.

1. Preparare il trasferimento

1. Trasferire la sottoscrizione di Azure in una directory diversa

1. Creare nuovamente le risorse nella directory di destinazione, ad esempio le assegnazioni di ruolo, i ruoli personalizzati e le identità gestite

    ![Diagramma del trasferimento della sottoscrizione](./media/transfer-subscription/transfer-subscription.png)

### <a name="deciding-whether-to-transfer-a-subscription-to-a-different-directory"></a>Decidere se trasferire una sottoscrizione in una directory diversa

Di seguito sono riportati alcuni motivi per cui è consigliabile trasferire una sottoscrizione:

- A causa di una fusione o acquisizione di una società, si vuole gestire una sottoscrizione acquisita nella directory di Azure AD principale.
- Un utente dell'organizzazione ha creato una sottoscrizione e si vuole consolidare la gestione in una Azure AD directory.
- Sono disponibili applicazioni che dipendono da un ID sottoscrizione o un URL specifico e non è facile modificare la configurazione o il codice dell'applicazione.
- Una parte dell'azienda è stata suddivisa in una società separata ed è necessario spostare alcune risorse in una directory Azure AD directory.
- Si vogliono gestire alcune risorse in una directory di Azure AD per motivi di isolamento della sicurezza.

### <a name="alternate-approaches"></a>Approcci alternativi

Il trasferimento di una sottoscrizione richiede tempi di inattività per completare il processo. A seconda dello scenario, è possibile prendere in considerazione gli approcci alternativi seguenti:

- Creare nuovamente le risorse e copiare i dati nella directory e nella sottoscrizione di destinazione.
- Adottare un'architettura multi-directory e lasciare la sottoscrizione nella directory di origine. Usare Azure Lighthouse delegare le risorse in modo che gli utenti nella directory di destinazione possano accedere alla sottoscrizione nella directory di origine. Per altre informazioni, vedere [Azure Lighthouse negli scenari aziendali.](../lighthouse/concepts/enterprise.md)

### <a name="understand-the-impact-of-transferring-a-subscription"></a>Comprendere l'impatto del trasferimento di una sottoscrizione

Diverse risorse di Azure hanno una dipendenza da una sottoscrizione o da una directory. A seconda della situazione, la tabella seguente elenca l'impatto noto del trasferimento di una sottoscrizione. Eseguendo i passaggi descritti in questo articolo, è possibile creare nuovamente alcune delle risorse esistenti prima del trasferimento della sottoscrizione.

> [!IMPORTANT]
> Questa sezione elenca i servizi o le risorse di Azure noti che dipendono dalla sottoscrizione. Poiché i tipi di risorse in Azure sono in continua evoluzione, potrebbero essere presenti dipendenze aggiuntive non elencate qui che possono causare una modifica di rilievo all'ambiente. 

| Servizio o risorsa | Influenzato | Recuperabile | Si è stati influenzati? | Operazioni possibili |
| --------- | --------- | --------- | --------- | --------- |
| Assegnazioni di ruoli | Sì | Sì | [Elencare le assegnazioni di ruolo](#save-all-role-assignments) | Tutte le assegnazioni di ruolo vengono eliminate definitivamente. È necessario eseguire il mapping di utenti, gruppi ed entità servizio agli oggetti corrispondenti nella directory di destinazione. È necessario creare nuovamente le assegnazioni di ruolo. |
| Ruoli personalizzati | Sì | Sì | [Elencare ruoli personalizzati](#save-custom-roles) | Tutti i ruoli personalizzati vengono eliminati definitivamente. È necessario creare nuovamente i ruoli personalizzati ed eventuali assegnazioni di ruolo. |
| Identità gestite assegnate dal sistema | Sì | Sì | [Elencare le identità gestite](#list-role-assignments-for-managed-identities) | È necessario disabilitare e riattivare le identità gestite. È necessario creare nuovamente le assegnazioni di ruolo. |
| Identità gestite assegnate dall'utente | Sì | Sì | [Elencare le identità gestite](#list-role-assignments-for-managed-identities) | È necessario eliminare, creare di nuovo e collegare le identità gestite alla risorsa appropriata. È necessario creare nuovamente le assegnazioni di ruolo. |
| Insieme di credenziali chiave di Azure | Sì | Sì | [Elencare Key Vault di accesso remoto](#list-key-vaults) | È necessario aggiornare l'ID tenant associato agli insiemi di credenziali delle chiavi. È necessario rimuovere e aggiungere nuovi criteri di accesso. |
| Azure SQL database con l'Azure AD di autenticazione abilitata | Sì | No | [Controllare Azure SQL database con l'Azure AD autenticazione](#list-azure-sql-databases-with-azure-ad-authentication) | Non è possibile trasferire un database Azure SQL con Azure AD'autenticazione abilitata in una directory diversa. Per altre informazioni, vedere [Usare l Azure Active Directory autenticazione .](../azure-sql/database/authentication-aad-overview.md) | 
| Archiviazione di Azure e Azure Data Lake Storage Gen2 | Sì | Sì |  | È necessario creare nuovamente eventuali elenchi di controllo di accesso. |
| Azure Data Lake Storage Gen1 | Sì | Sì |  | È necessario creare nuovamente eventuali elenchi di controllo di accesso. |
| File di Azure | Sì | Sì |  | È necessario creare nuovamente eventuali elenchi di controllo di accesso. |
| Sincronizzazione file di Azure | Sì | Sì |  | Il servizio di sincronizzazione archiviazione e/o l'account di archiviazione possono essere spostati in una directory diversa. Per altre informazioni, vedere [Domande frequenti sulle](../storage/files/storage-files-faq.md#azure-file-sync) File di Azure |
| Azure Managed Disks | Sì | Sì |  |  Se si usano set di crittografia del disco per crittografare i Managed Disks con chiavi gestite dal cliente, è necessario disabilitare e riattivare le identità assegnate dal sistema associate ai set di crittografia del disco. Ed è necessario creare nuovamente le assegnazioni di ruolo, ad esempio concedere nuovamente le autorizzazioni necessarie ai set di crittografia del disco negli insiemi di credenziali delle chiavi. |
| Servizio Azure Kubernetes | Sì | No |  | Non è possibile trasferire il cluster del servizio Web Diaks e le risorse associate in una directory diversa. Per altre informazioni, vedere [Domande frequenti su servizio Azure Kubernetes (AKS)](../aks/faq.md) |
| Criteri di Azure | Sì | No | Tutti Criteri di Azure, incluse definizioni personalizzate, assegnazioni, esenzioni e dati di conformità. | È necessario [esportare,](../governance/policy/how-to/export-resources.md)importare e rias assegnare le definizioni. Creare quindi nuove assegnazioni di criteri ed eventuali [esenzioni dei criteri necessarie.](../governance/policy/concepts/exemption-structure.md) |
| Azure Active Directory Domain Services | Sì | No |  | Non è possibile trasferire Azure AD Domain Services dominio gestito in una directory diversa. Per altre informazioni, vedere [Domande frequenti su Azure Active Directory (AD) Domain Services](../active-directory-domain-services/faqs.md) |
| Registrazioni per l'app | Sì | Sì |  |  |

> [!WARNING]
> Se si usa la crittografia dei dati in pausa per una risorsa, ad esempio un  account di archiviazione o un database SQL, che ha una dipendenza da un insieme di credenziali delle chiavi che non si trova nella stessa sottoscrizione che viene trasferita, può verificarsi uno scenario irreversibile. In questo caso, è consigliabile usare un insieme di credenziali delle chiavi diverso o disabilitare temporaneamente le chiavi gestite dal cliente per evitare questo scenario irreversibile.

## <a name="prerequisites"></a>Prerequisiti

Per completare questi passaggi, è necessario:

- [Bash in Azure Cloud Shell](../cloud-shell/overview.md) o nell'interfaccia della riga [di comando di Azure](/cli/azure)
- Amministratore account della sottoscrizione da trasferire nella directory di origine
- [Ruolo](built-in-roles.md#owner) proprietario nella directory di destinazione

## <a name="step-1-prepare-for-the-transfer"></a>Passaggio 1: Preparare il trasferimento

### <a name="sign-in-to-source-directory"></a>Accedere alla directory di origine

1. Accedere ad Azure come amministratore.

1. Ottenere un elenco delle sottoscrizioni con il [comando az account list.](/cli/azure/account#az_account_list)

    ```azurecli
    az account list --output table
    ```

1. Usare [az account set](/cli/azure/account#az_account_set) per impostare la sottoscrizione attiva da trasferire.

    ```azurecli
    az account set --subscription "Marketing"
    ```

### <a name="install-the-azure-resource-graph-extension"></a>Installare l'Azure Resource Graph predefinita

 L'estensione [dell'interfaccia](../governance/resource-graph/index.yml)della riga di comando di Azure Azure Resource Graph , *resource-graph*, consente di usare il [comando az graph](/cli/azure/graph) per eseguire query sulle risorse gestite da Azure Resource Manager. Questo comando verrà utilizzato nei passaggi successivi.

1. Usare [az extension list](/cli/azure/extension#az_extension_list) per verificare se è installata l'estensione *resource-graph.*

    ```azurecli
    az extension list
    ```

1. In caso contrario, installare *l'estensione resource-graph.*

    ```azurecli
    az extension add --name resource-graph
    ```

### <a name="save-all-role-assignments"></a>Salvare tutte le assegnazioni di ruolo

1. Usare [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list) per elencare tutte le assegnazioni di ruolo (incluse le assegnazioni di ruolo ereditate).

    Per semplificare la revisione dell'elenco, è possibile esportare l'output come JSON, TSV o tabella. Per altre informazioni, vedere Elencare le assegnazioni di ruolo usando il controllo degli accessi in base al ruolo [di Azure e l'interfaccia della riga di comando di Azure.](role-assignments-list-cli.md)

    ```azurecli
    az role assignment list --all --include-inherited --output json > roleassignments.json
    az role assignment list --all --include-inherited --output tsv > roleassignments.tsv
    az role assignment list --all --include-inherited --output table > roleassignments.txt
    ```

1. Salvare l'elenco di assegnazioni di ruolo.

    Quando si trasferisce una sottoscrizione, tutte  le assegnazioni di ruolo vengono eliminate definitivamente, quindi è importante salvare una copia.

1. Esaminare l'elenco delle assegnazioni di ruolo. Potrebbero non essere necessarie assegnazioni di ruolo nella directory di destinazione.

### <a name="save-custom-roles"></a>Salvare i ruoli personalizzati

1. Usare [l'elenco az role definition per](/cli/azure/role/definition#az_role_definition_list) elencare i ruoli personalizzati. Per altre informazioni, vedere Creare o aggiornare [ruoli personalizzati di Azure usando l'interfaccia](custom-roles-cli.md)della riga di comando di Azure.

    ```azurecli
    az role definition list --custom-role-only true --output json --query '[].{roleName:roleName, roleType:roleType}'
    ```

1. Salvare ogni ruolo personalizzato necessario nella directory di destinazione come file JSON separato.

    ```azurecli
    az role definition list --name <custom_role_name> > customrolename.json
    ```

1. Creare copie dei file dei ruoli personalizzati.

1. Modificare ogni copia in modo che usi il formato seguente.

    Questi file verranno utilizzati in un secondo momento per creare nuovamente i ruoli personalizzati nella directory di destinazione.

    ```json
    {
      "Name": "",
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": []
    }
    ```

### <a name="determine-user-group-and-service-principal-mappings"></a>Determinare i mapping di utenti, gruppi ed entità servizio

1. In base all'elenco di assegnazioni di ruolo, determinare gli utenti, i gruppi e le entità servizio a cui verrà mappato nella directory di destinazione.

    È possibile identificare il tipo di entità esaminando la `principalType` proprietà in ogni assegnazione di ruolo.

1. Se necessario, nella directory di destinazione creare tutti gli utenti, i gruppi o le entità servizio necessari.

### <a name="list-role-assignments-for-managed-identities"></a>Elencare le assegnazioni di ruolo per le identità gestite

Le identità gestite non vengono aggiornate quando una sottoscrizione viene trasferita in un'altra directory. Di conseguenza, tutte le identità gestite assegnate dal sistema o dall'utente esistenti verranno interrotte. Dopo il trasferimento, è possibile ri abilitare tutte le identità gestite assegnate dal sistema. Per le identità gestite assegnate dall'utente, sarà necessario crearle di nuovo e collegarle nella directory di destinazione.

1. Esaminare [l'elenco dei servizi di Azure che supportano le identità gestite](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) per notare dove si potrebbero usare le identità gestite.

1. Usare [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) per elencare le identità gestite assegnate dal sistema e assegnate dall'utente.

    ```azurecli
    az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
    ```

1. Nell'elenco delle identità gestite determinare quali sono assegnate dal sistema e quali sono assegnate dall'utente. Per determinare il tipo, è possibile usare i criteri seguenti.

    | Criteri | Tipo di identità gestita |
    | --- | --- |
    | `alternativeNames` la proprietà include `isExplicit=False` | Assegnato dal sistema |
    | `alternativeNames` la proprietà non include `isExplicit` | Assegnato dal sistema |
    | `alternativeNames` la proprietà include `isExplicit=True` | Assegnato dall'utente |

    È anche possibile usare [az identity list per](/cli/azure/identity#az_identity_list) elencare solo le identità gestite assegnate dall'utente. Per altre informazioni, vedere [Creare, elencare o eliminare un'identità gestita assegnata dall'utente usando l'interfaccia della](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)riga di comando di Azure.

    ```azurecli
    az identity list
    ```

1. Ottenere un elenco dei `objectId` valori per le identità gestite.

1. Cercare nell'elenco delle assegnazioni di ruolo per verificare se sono presenti assegnazioni di ruolo per le identità gestite.

### <a name="list-key-vaults"></a>List key vaults

Quando si crea un insieme di credenziali delle chiavi, questo viene associato automaticamente all'ID tenant Azure Active Directory predefinito per la sottoscrizione in cui viene creato. All'ID tenant vengono associate anche tutte le voci dei criteri di accesso. Per altre informazioni, vedere [Spostamento di un Azure Key Vault in un'altra sottoscrizione.](../key-vault/general/move-subscription.md)

> [!WARNING]
> Se si usa la crittografia in pausa per una risorsa, ad esempio un account di  archiviazione o un database SQL, che ha una dipendenza da un insieme di credenziali delle chiavi che non si trova nella stessa sottoscrizione che viene trasferita, può causare uno scenario irreversibile. In questo caso, è consigliabile usare un insieme di credenziali delle chiavi diverso o disabilitare temporaneamente le chiavi gestite dal cliente per evitare questo scenario irreversibile.

- Se si dispone di un insieme di credenziali delle chiavi, usare [az keyvault show](/cli/azure/keyvault#az_keyvault_show) per elencare i criteri di accesso. Per altre informazioni, vedere [Assegnare un criterio Key Vault di accesso.](../key-vault/general/assign-access-policy-cli.md)

    ```azurecli
    az keyvault show --name MyKeyVault
    ```

### <a name="list-azure-sql-databases-with-azure-ad-authentication"></a>Elencare Azure SQL database con Azure AD autenticazione

- Usare [az sql server ad-admin list](/cli/azure/sql/server/ad-admin#az_sql_server_ad_admin_list) e l'estensione [az graph](/cli/azure/graph) per verificare se si usano database Azure SQL con l'Azure AD di autenticazione abilitata. Per altre informazioni, vedere [Configurare e gestire l'Azure Active Directory con SQL.](../azure-sql/database/authentication-aad-configure.md)

    ```azurecli
    az sql server ad-admin list --ids $(az graph query -q 'resources | where type == "microsoft.sql/servers" | project id' -o tsv | cut -f1)
    ```

### <a name="list-acls"></a>Elencare gli elenchi di controllo di accesso

1. Se si usa Azure Data Lake Storage Gen1, elencare gli ACL applicati a qualsiasi file usando il portale di Azure o PowerShell.

1. Se si usa Azure Data Lake Storage Gen2, elencare gli elenchi di controllo di accesso applicati a qualsiasi file usando il portale di Azure o PowerShell.

1. Se si usa un File di Azure, elencare gli elenchi di controllo di accesso applicati a qualsiasi file.

### <a name="list-other-known-resources"></a>Elencare altre risorse note

1. Usare [az account show per](/cli/azure/account#az_account_show) ottenere l'ID sottoscrizione.

    ```azurecli
    subscriptionId=$(az account show --query id | sed -e 's/^"//' -e 's/"$//')
    ```

1. Usare [l'estensione az graph](/cli/azure/graph) per elencare altre risorse di Azure con dipendenze Azure AD directory note.

    ```azurecli
    az graph query -q \
    'resources | where type != "microsoft.azureactivedirectory/b2cdirectories" | where  identity <> "" or properties.tenantId <> "" or properties.encryptionSettingsCollection.enabled == true | project name, type, kind, identity, tenantId, properties.tenantId' \
    --subscriptions $subscriptionId --output table
    ```

## <a name="step-2-transfer-the-subscription"></a>Passaggio 2: Trasferire la sottoscrizione

In questo passaggio si trasferisce la sottoscrizione dalla directory di origine alla directory di destinazione. I passaggi saranno diversi a seconda che si voglia trasferire anche la proprietà della fatturazione.

> [!WARNING]
> Quando si trasferisce la sottoscrizione, tutte le  assegnazioni di ruolo nella directory di origine vengono eliminate definitivamente e non possono essere ripristinate. Non è possibile tornare indietro dopo aver trasferito la sottoscrizione. Assicurarsi di completare i passaggi precedenti prima di eseguire questo passaggio.

1. Determinare se si vuole trasferire anche la proprietà della fatturazione a un altro account.

1. Trasferire la sottoscrizione in una directory diversa.

    - Se si vuole mantenere la proprietà della fatturazione corrente, seguire la procedura descritta in Associare o aggiungere una sottoscrizione di [Azure al tenant Azure Active Directory.](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
    - Se si vuole trasferire anche la proprietà della fatturazione, seguire la procedura descritta in Trasferire la proprietà della fatturazione di una [sottoscrizione di Azure a un altro account](../cost-management-billing/manage/billing-subscription-transfer.md). Per trasferire la sottoscrizione in una directory diversa, è necessario selezionare la casella **di controllo Sottoscrizione Azure AD tenant.**

1. Al termine del trasferimento della sottoscrizione, tornare a questo articolo per creare nuovamente le risorse nella directory di destinazione.

## <a name="step-3-re-create-resources"></a>Passaggio 3: Creare di nuovo le risorse

### <a name="sign-in-to-target-directory"></a>Accedere alla directory di destinazione

1. Nella directory di destinazione accedere come utente che ha accettato la richiesta di trasferimento.

    Solo l'utente del nuovo account che ha accettato la richiesta di trasferimento avrà accesso per gestire le risorse.

1. Ottenere un elenco delle sottoscrizioni con il [comando az account list.](/cli/azure/account#az_account_list)

    ```azurecli
    az account list --output table
    ```

1. Usare [az account set](/cli/azure/account#az_account_set) per impostare la sottoscrizione attiva che si vuole usare.

    ```azurecli
    az account set --subscription "Contoso"
    ```

### <a name="create-custom-roles"></a>Creare ruoli personalizzati
        
- Usare [az role definition create](/cli/azure/role/definition#az_role_definition_create) per creare ogni ruolo personalizzato dai file creati in precedenza. Per altre informazioni, vedere Creare o aggiornare ruoli personalizzati di Azure tramite l'interfaccia della riga [di comando di Azure.](custom-roles-cli.md)

    ```azurecli
    az role definition create --role-definition <role_definition>
    ```

### <a name="assign-roles"></a>Assegnare ruoli

- Usare [az role assignment create per](/cli/azure/role/assignment#az_role_assignment_create) assegnare ruoli a utenti, gruppi ed entità servizio. Per altre informazioni, vedere Assegnare ruoli di [Azure tramite l'interfaccia della riga di comando di Azure.](role-assignments-cli.md)

    ```azurecli
    az role assignment create --role <role_name_or_id> --assignee <assignee> --resource-group <resource_group>
    ```

### <a name="update-system-assigned-managed-identities"></a>Aggiornare le identità gestite assegnate dal sistema

1. Disabilitare e riattivare le identità gestite assegnate dal sistema.

    | Servizio di Azure | Ulteriori informazioni | 
    | --- | --- |
    | Macchine virtuali | [Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#system-assigned-managed-identity) |
    | set di scalabilità di macchine virtuali | [Configurare identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali tramite l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#system-assigned-managed-identity) |
    | Altri servizi | [Servizi che supportano le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) |

1. Usare [az role assignment create per](/cli/azure/role/assignment#az_role_assignment_create) assegnare ruoli alle identità gestite assegnate dal sistema. Per altre informazioni, vedere Assegnare un accesso [a un'identità gestita a una risorsa usando](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)l'interfaccia della riga di comando di Azure.

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-user-assigned-managed-identities"></a>Aggiornare le identità gestite assegnate dall'utente

1. Eliminare, creare di nuovo e collegare identità gestite assegnate dall'utente.

    | Servizio di Azure | Ulteriori informazioni | 
    | --- | --- |
    | Macchine virtuali | [Configurare le identità gestite per le risorse di Azure in una macchina virtuale di Azure tramite l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) |
    | set di scalabilità di macchine virtuali | [Configurare identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali tramite l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vmss.md#user-assigned-managed-identity) |
    | Altri servizi | [Servizi che supportano le identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)<br/>[Creare, elencare o eliminare un'identità gestita assegnata dall'utente usando l'interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) |

1. Usare [az role assignment create per](/cli/azure/role/assignment#az_role_assignment_create) assegnare ruoli alle identità gestite assegnate dall'utente. Per altre informazioni, vedere Assegnare un accesso [a un'identità gestita a una risorsa usando](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)l'interfaccia della riga di comando di Azure.

    ```azurecli
    az role assignment create --assignee <objectid> --role '<role_name_or_id>' --scope <scope>
    ```

### <a name="update-key-vaults"></a>Aggiornare gli insiemi di credenziali delle chiavi

Questa sezione descrive i passaggi di base per aggiornare gli insiemi di credenziali delle chiavi. Per altre informazioni, vedere [Spostamento di un Azure Key Vault in un'altra sottoscrizione.](../key-vault/general/move-subscription.md)

1. Aggiornare l'ID tenant associato a tutti gli insiemi di credenziali delle chiavi esistenti nella sottoscrizione alla directory di destinazione.

1. Rimuovere tutte le voci dei criteri di accesso esistenti.

1. Aggiungere nuove voci di criteri di accesso associate alla directory di destinazione.

### <a name="update-acls"></a>Aggiornare gli ACL

1. Se si usa un Azure Data Lake Storage Gen1, assegnare gli ACL appropriati. Per altre informazioni, vedere [Protezione dei dati archiviati in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-secure-data.md).

1. Se si usa un Azure Data Lake Storage Gen2, assegnare gli ACL appropriati. Per altre informazioni, vedere [Access control in Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md) (Controllo di accesso in Azure Data Lake Storage Gen2).

1. Se si usa un File di Azure, assegnare gli ACL appropriati.

### <a name="review-other-security-methods"></a>Esaminare altri metodi di sicurezza

Anche se le assegnazioni di ruolo vengono rimosse durante il trasferimento, gli utenti nell'account proprietario originale potrebbero continuare ad avere accesso alla sottoscrizione tramite altri metodi di sicurezza, tra cui:

- Chiavi di accesso per servizi quali Archiviazione.
- [Certificati di gestione](../cloud-services/cloud-services-certs-create.md) che concedono all'amministratore utente l'accesso alle risorse della sottoscrizione.
- Credenziali di accesso remoto per servizi quali macchine virtuali di Azure.

Se si vuole rimuovere l'accesso dagli utenti nella directory di origine in modo che non abbia accesso alla directory di destinazione, è consigliabile ruotare le credenziali. Finché le credenziali non vengono aggiornate, gli utenti continueranno ad avere accesso dopo il trasferimento.

1. Ruotare le chiavi di accesso dell'account di archiviazione. Per altre informazioni, vedere [Gestire le chiavi di accesso dell'account di archiviazione](../storage/common/storage-account-keys-manage.md).

1. Se si usano chiavi di accesso per altri servizi, ad esempio database SQL di Azure o bus di servizio di Azure messaggistica, ruotare le chiavi di accesso.

1. Per le risorse che usano segreti, aprire le impostazioni per la risorsa e aggiornare il segreto.

1. Per le risorse che usano certificati, aggiornare il certificato.

## <a name="next-steps"></a>Passaggi successivi

- [Trasferire la proprietà della fatturazione di una sottoscrizione di Azure in un altro account](../cost-management-billing/manage/billing-subscription-transfer.md)
- [Trasferire le sottoscrizioni di Azure tra sottoscrittori e CSP](../cost-management-billing/manage/transfer-subscriptions-subscribers-csp.md)
- [Associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
- [Azure Lighthouse in scenari aziendali](../lighthouse/concepts/enterprise.md)
