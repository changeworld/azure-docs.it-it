---
title: Concedere l'autorizzazione alle applicazioni per accedere a un insieme di credenziali delle chiavi di Azure usando il controllo degli accessi in base al ruolo di Azure | Microsoft Docs
description: Informazioni su come fornire l'accesso a chiavi, segreti e certificati usando il controllo degli accessi in base al ruolo di Azure.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/15/2021
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 9806327c7393c3ba1fdab09acfb5545d6026e5cf
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818463"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Fornire l'accesso Key Vault chiavi, certificati e segreti con un controllo degli accessi in base al ruolo di Azure

> [!NOTE]
> Key Vault provider di risorse supporta due tipi di risorse: **insiemi di** credenziali e **HMS gestiti.** Il controllo di accesso descritto in questo articolo si applica solo **agli insiemi di credenziali**. Per altre informazioni sul controllo di accesso per il servizio HSM gestito, vedere [Controllo di accesso HSM gestito](../managed-hsm/access-control.md).

Il controllo degli accessi in base al ruolo [](../../azure-resource-manager/management/overview.md) di Azure è un sistema di autorizzazione basato su Azure Resource Manager che offre una gestione granulare degli accessi delle risorse di Azure.

Il controllo degli accessi in base al ruolo di Azure consente agli utenti di gestire le autorizzazioni chiave, segreti e certificati. Offre un'unica posizione per gestire tutte le autorizzazioni in tutti gli insiemi di credenziali delle chiavi. 

Il modello controllo degli accessi in base al ruolo di Azure consente di impostare le autorizzazioni per diversi livelli di ambito: gruppo di gestione, sottoscrizione, gruppo di risorse o singole risorse.  Il controllo degli accessi in base al ruolo di Azure per l'insieme di credenziali delle chiavi offre anche la possibilità di disporre di autorizzazioni separate per singole chiavi, segreti e certificati

Per altre informazioni, vedere Controllo [degli accessi in base al ruolo di Azure.](../../role-based-access-control/overview.md)

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Procedure consigliate per singole chiavi, segreti e certificati

È consigliabile usare un insieme di credenziali per ogni applicazione per ogni ambiente (sviluppo, pre-produzione e produzione).

Le singole chiavi, segreti e certificati devono essere usate solo per scenari specifici:

-   Applicazioni multistrato che devono separare il controllo di accesso tra i livelli

-   Condivisione di un singolo segreto tra più applicazioni

Per altre informazioni Azure Key Vault sulla gestione dei dati, vedere:

- [Azure Key Vault funzionalità di sicurezza](security-features.md)
- [Azure Key Vault dei servizi](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Ruoli predefiniti di Azure per le Key Vault del piano dati
> [!NOTE]
> `Key Vault Contributor` il ruolo è per le operazioni del piano di gestione per gestire gli insiemi di credenziali delle chiavi. Non consente l'accesso a chiavi, segreti e certificati.

| Ruolo predefinito | Descrizione | ID |
| --- | --- | --- |
| Key Vault amministratore| Eseguire tutte le operazioni del piano dati in un insieme di credenziali delle chiavi e in tutti gli oggetti in esso contenuti, inclusi certificati, chiavi e segreti. Non è possibile gestire le risorse dell'insieme di credenziali delle chiavi o le assegnazioni di ruolo. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault Certificates Officer | Eseguire qualsiasi azione sui certificati di un insieme di credenziali delle chiavi, ad eccezione delle autorizzazioni di gestione. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault Crypto Officer | Eseguire qualsiasi azione sulle chiavi di un insieme di credenziali delle chiavi, ad eccezione delle autorizzazioni di gestione. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Key Vault crittografia del servizio di crittografia | Legge i metadati delle chiavi ed esegue operazioni di wrapping/annullamento del wrapping. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault utente crypto  | Eseguire operazioni di crittografia usando le chiavi. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Key Vault lettore | Leggere i metadati degli insiemi di credenziali delle chiavi e i relativi certificati, chiavi e segreti. Impossibile leggere valori sensibili, ad esempio il contenuto del segreto o il materiale della chiave. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Key Vault Secrets Officer| Eseguire qualsiasi azione sui segreti di un insieme di credenziali delle chiavi, ad eccezione delle autorizzazioni di gestione. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Key Vault'utente dei segreti | Leggere il contenuto del segreto. Funziona solo per gli insiemi di credenziali delle chiavi che usano il modello di autorizzazione "Controllo degli accessi in base al ruolo di Azure". | 4633458b-17de-408a-b874-0445c86b69e6 |

Per altre informazioni sulle definizioni dei ruoli predefiniti di Azure, vedere [Ruoli predefiniti di Azure.](../../role-based-access-control/built-in-roles.md)

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Uso delle autorizzazioni per segreto, chiave e certificato del controllo degli accessi in base al ruolo di Azure con Key Vault

Il nuovo modello di autorizzazione controllo degli accessi in base al ruolo di Azure per l'insieme di credenziali delle chiavi offre un'alternativa al modello di autorizzazioni dei criteri di accesso all'insieme di credenziali. 

### <a name="prerequisites"></a>Prerequisiti

Per aggiungere assegnazioni di ruolo, è necessario disporre di

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
- autorizzazioni `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, ad esempio [Amministratore accesso utenti](../../role-based-access-control/built-in-roles.md#user-access-administrator) o [Proprietario](../../role-based-access-control/built-in-roles.md#owner)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Abilitare le autorizzazioni di Controllo degli accessi in base al ruolo di Azure Key Vault

> [!NOTE]
> La modifica del modello di autorizzazione richiede l'autorizzazione 'Microsoft.Authorization/roleAssignments/write', che fa parte dei ruoli [Proprietario](../../role-based-access-control/built-in-roles.md#owner) e [Amministratore Accesso](../../role-based-access-control/built-in-roles.md#user-access-administrator) utenti. I ruoli di amministratore della sottoscrizione classica, ad esempio "Amministratore del servizio" e "Co-amministratore", non sono supportati.

1.  Abilitare le autorizzazioni di Controllo degli accessi in base al ruolo di Azure nel nuovo insieme di credenziali delle chiavi:

    ![Abilitare le autorizzazioni di Controllo degli accessi in base al ruolo di Azure - Nuovo insieme di credenziali](../media/rbac/image-1.png)

2.  Abilitare le autorizzazioni di Controllo degli accessi in base al ruolo di Azure nell'insieme di credenziali delle chiavi esistente:

    ![Abilitare le autorizzazioni di Controllo degli accessi in base al ruolo di Azure - Insieme di credenziali esistente](../media/rbac/image-2.png)

> [!IMPORTANT]
> L'impostazione del modello di autorizzazione controllo degli accessi in base al ruolo di Azure invalida tutte le autorizzazioni dei criteri di accesso. Può causare interruzioni quando non vengono assegnati ruoli di Azure equivalenti.

### <a name="assign-role"></a>Assegnare il ruolo

> [!Note]
> È consigliabile usare l'ID ruolo univoco anziché il nome del ruolo negli script. Pertanto, se un ruolo viene rinominato, gli script continueranno a funzionare. In questo documento il nome del ruolo viene usato solo per una leggibilità.

Eseguire il comando seguente per creare un'assegnazione di ruolo:

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName <role_name> -SignInName <assignee_upn> -Scope <scope>

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName Reader -ApplicationId <applicationId> -Scope <scope>
```
---

Nell'portale di Azure, la schermata Assegnazioni di ruolo di Azure è disponibile per tutte le risorse nella scheda Controllo di accesso (IAM).

![Assegnazione di ruolo - scheda (IAM)](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Assegnazione di ruolo dell'ambito del gruppo di risorse

1.  Passare al gruppo di risorse dell'insieme di credenziali delle chiavi.
    ![Assegnazione di ruolo - Gruppo di risorse](../media/rbac/image-4.png)

2.  Fare clic su Controllo di accesso (IAM) \> Aggiungi assegnazione di ruolo \> Aggiungi

3.  Creare Key Vault ruolo lettore "Key Vault lettore" per l'utente corrente

    ![Aggiungere un ruolo - Gruppo di risorse](../media/rbac/image-5.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Reader' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```
---

L'assegnazione di ruolo precedente consente di elencare gli oggetti dell'insieme di credenziali delle chiavi nell'insieme di credenziali delle chiavi.

### <a name="key-vault-scope-role-assignment"></a>Key Vault di ruolo dell'ambito

1. Passare alla Key Vault \> Controllo di accesso (IAM)

2. Fare clic su Aggiungi assegnazione ruolo \> Aggiungi

3. Creare il ruolo di responsabile dei segreti Key Vault "responsabile dei segreti" per l'utente corrente.

    ![Assegnazione di ruolo - Insieme di credenziali delle chiavi](../media/rbac/image-6.png)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```
---

Dopo aver creato l'assegnazione di ruolo precedente, è possibile creare/aggiornare/eliminare segreti.

4. Creare un nuovo segreto ( Segreti \> +Genera/Importa) per testare l'assegnazione di ruolo a livello di segreto.

    ![Aggiungere un ruolo - Insieme di credenziali delle chiavi](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Assegnazione di ruolo ambito segreto

1. Aprire uno dei segreti creati in precedenza, osservare Panoramica e controllo di accesso (IAM) 

2. Fare clic sulla scheda Controllo di accesso (IAM)

    ![Assegnazione di ruolo - segreto](../media/rbac/image-8.png)

3. Creare il ruolo di responsabile dei segreti Key Vault "responsabile dei segreti" per l'utente corrente, come è stato fatto in precedenza per il Key Vault.

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
#Assign by User Principal Name
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -SignInName {i.e user@microsoft.com} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret

#Assign by Service Principal ApplicationId
New-AzRoleAssignment -RoleDefinitionName 'Key Vault Secrets Officer' -ApplicationId {i.e 8ee5237a-816b-4a72-b605-446970e5f156} -Scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```
---

### <a name="test-and-verify"></a>Test e verifica

> [!NOTE]
> I browser usano la memorizzazione nella cache e l'aggiornamento della pagina è necessario dopo la rimozione delle assegnazioni di ruolo.<br>
> Consentire l'aggiornamento delle assegnazioni di ruolo per alcuni minuti

1. Convalidare l'aggiunta di un nuovo segreto senza il Key Vault "Responsabile dei segreti" a livello di insieme di credenziali delle chiavi.

Passare alla scheda Controllo di accesso (IAM) dell'insieme di credenziali delle chiavi e rimuovere l'assegnazione di ruolo "Key Vault Secrets Officer" per questa risorsa.

![Rimuovere l'assegnazione - Insieme di credenziali delle chiavi](../media/rbac/image-9.png)

Passare al segreto creato in precedenza. È possibile visualizzare tutte le proprietà dei segreti.

![Visualizzazione segreta con accesso](../media/rbac/image-10.png)

Creare un nuovo segreto ( Segreti \> +Genera/Importa) dovrebbe essere visualizzato l'errore seguente:

   ![Creare un nuovo segreto](../media/rbac/image-11.png)

2.  Convalidare la modifica dei segreti senza Key Vault secret officer a livello di segreto.

-   Passare alla scheda Controllo di accesso segreto (IAM) creata in precedenza e rimuovere l'assegnazione di ruolo "Key Vault Secrets Officer" per questa risorsa.

-   Passare al segreto creato in precedenza. È possibile visualizzare le proprietà dei segreti.

   ![Visualizzazione segreta senza accesso](../media/rbac/image-12.png)

3. Convalidare i segreti letti senza ruolo lettore a livello di insieme di credenziali delle chiavi.

-   Passare alla scheda Controllo di accesso (IAM) del gruppo di risorse dell'insieme di credenziali delle chiavi e rimuovere l'assegnazione di ruolo "Key Vault lettore".

-   Passando alla scheda Segreti dell'insieme di credenziali delle chiavi dovrebbe essere visualizzato l'errore seguente:

   ![Scheda Segreto - Errore](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Creazione di ruoli personalizzati 

[Comando az role definition create](/cli/azure/role/definition#az_role_definition_create)

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$roleDefinition = @"
{ 
   "Name": "Backup Keys Operator", 
   "Description": "Perform key backup/restore operations", 
    "Actions": [ 
    ], 
    "DataActions": [ 
        "Microsoft.KeyVault/vaults/keys/read ", 
        "Microsoft.KeyVault/vaults/keys/backup/action", 
         "Microsoft.KeyVault/vaults/keys/restore/action" 
    ], 
    "NotDataActions": [ 
   ], 
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] 
}
"@

$roleDefinition | Out-File role.json

New-AzRoleDefinition -InputFile role.json
```
---

Per altre informazioni su come creare ruoli personalizzati, vedere:

[Ruoli personalizzati di Azure](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Limiti noti e prestazioni

-   2000 Assegnazioni di ruolo di Azure per sottoscrizione

-   Latenza delle assegnazioni di ruolo: con le prestazioni previste correnti, saranno previsti fino a 10 minuti (600 secondi) dopo la modifica delle assegnazioni di ruolo per l'applicazione del ruolo

## <a name="learn-more"></a>Altre informazioni

- [Panoramica del controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md)
- [Esercitazione sui ruoli personalizzati](../../role-based-access-control/tutorial-custom-role-cli.md)