---
title: Azure Key Vault lo stato di trasferimento di un insieme di credenziali a una sottoscrizione diversa | Microsoft Docs
description: Linee guida per lo trasferimento di un insieme di credenziali delle chiavi in una sottoscrizione diversa.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 05/05/2020
ms.author: mbaldwin
Customer intent: As a key vault administrator, I want to move my vault to another subscription.
ms.openlocfilehash: a84627b2b426385d21ad72b85780db86e7dd5ee3
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096069"
---
# <a name="moving-an-azure-key-vault-to-another-subscription"></a>Spostamento di Azure Key Vault in un'altra sottoscrizione

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Panoramica

> [!IMPORTANT]
> **Se si trasferisce un insieme di credenziali delle chiavi in un'altra sottoscrizione, si verificherà una modifica sostanziale nell'ambiente**
> Assicurarsi di comprendere l'effetto di questa modifica e seguire attentamente le istruzioni contenute in questo articolo prima di decidere di spostare l'insieme di credenziali delle chiavi in una nuova sottoscrizione.
> Se si usano identità del servizio gestito (MSI), leggere le istruzioni di post-spostamento alla fine del documento. 

[Azure Key Vault](overview.md) viene automaticamente associato all'ID tenant predefinito [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) per la sottoscrizione in cui viene creato. È possibile trovare l'ID tenant associato alla sottoscrizione seguendo le istruzioni riportate in questa [Guida](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md). Tutte le voci dei criteri di accesso e le assegnazioni di ruoli sono inoltre associate a questo ID tenant.  Se si sposta la sottoscrizione di Azure dal tenant A al tenant B, gli insiemi di credenziali delle chiavi esistenti saranno inaccessibili dalle entità servizio (utenti e applicazioni) nel tenant B. Per risolvere il problema, è necessario:

* Modificare l'ID tenant associato a tutti gli insiemi di credenziali delle chiavi esistenti nella sottoscrizione impostandolo sul tenant B.
* Rimuovere tutte le voci dei criteri di accesso esistenti.
* Aggiungere nuove voci dei criteri di accesso associate al tenant B.

Per ulteriori informazioni su Azure Key Vault e Azure Active Directory, vedere.
- [Informazioni su Azure Key Vault](overview.md)
- [Informazioni Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)
- [Come trovare l'ID tenant](../../active-directory/fundamentals/active-directory-how-to-find-tenant.md)

## <a name="limitations"></a>Limitazioni

> [!IMPORTANT]
> **Non è possibile spostare gli insiemi di credenziali delle chiavi usati per la crittografia del disco** Se si usa Key Vault con la crittografia del disco per una macchina virtuale, l'insieme di credenziali delle chiavi non può essere spostato in un gruppo di risorse diverso o in una sottoscrizione mentre è abilitata la crittografia del disco. Prima di trasferire l'insieme di credenziali delle chiavi in un nuovo gruppo di risorse o una nuova sottoscrizione, è necessario disabilitare la crittografia del disco. 

Alcune entità servizio (utenti e applicazioni) sono associate a un tenant specifico. Se si sposta l'insieme di credenziali delle chiavi in una sottoscrizione in un altro tenant, è probabile che non sia possibile ripristinare l'accesso a un'entità servizio specifica. Verificare che tutte le entità servizio essenziali esistano nel tenant in cui si sta migrando l'insieme di credenziali delle chiavi.

## <a name="prerequisites"></a>Prerequisiti

* Accesso a livello di [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) o superiore alla sottoscrizione corrente in cui è presente l'insieme di credenziali delle chiavi. È possibile assegnare un ruolo usando [portale di Azure](../../role-based-access-control/role-assignments-portal.md), l'interfaccia della riga di comando di [Azure](../../role-based-access-control/role-assignments-cli.md)o [PowerShell](../../role-based-access-control/role-assignments-powershell.md).
* Accesso a livello di [collaboratore](../../role-based-access-control/built-in-roles.md#contributor) o superiore alla sottoscrizione in cui si vuole spostare l'insieme di credenziali delle chiavi. È possibile assegnare un ruolo usando [portale di Azure](../../role-based-access-control/role-assignments-portal.md), l'interfaccia della riga di comando di [Azure](../../role-based-access-control/role-assignments-cli.md)o [PowerShell](../../role-based-access-control/role-assignments-powershell.md).
* Un gruppo di risorse nella nuova sottoscrizione. È possibile crearne uno usando [portale di Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md), [PowerShell](../../azure-resource-manager/management/manage-resource-groups-powershell.md)o l'interfaccia della riga di comando di [Azure](../../azure-resource-manager/management/manage-resource-groups-cli.md).

È possibile controllare i ruoli esistenti usando [portale di Azure](../../role-based-access-control/role-assignments-list-portal.md), [PowerShell](../../role-based-access-control/role-assignments-list-powershell.md), l'interfaccia della riga di comando di [Azure](../../role-based-access-control/role-assignments-list-cli.md)o l' [API REST](../../role-based-access-control/role-assignments-list-rest.md).


## <a name="moving-a-key-vault-to-a-new-subscription"></a>Trasferimento di un insieme di credenziali delle chiavi in una nuova sottoscrizione

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
2. Passare all'insieme di credenziali delle [chiavi](overview.md)
3. Fare clic sulla scheda "panoramica"
4. Selezionare il pulsante "Sposta"
5. Selezionare "sposta in un'altra sottoscrizione" dalle opzioni a discesa
6. Selezionare il gruppo di risorse in cui si vuole spostare l'insieme di credenziali delle chiavi
7. Confermare l'avviso relativo allo scambio di risorse
8. Scegliere "OK"

## <a name="additional-steps-when-subscription-is-in-a-new-tenant"></a>Passaggi aggiuntivi quando la sottoscrizione si trova in un nuovo tenant

Se il Key Vault è stato spostato in una sottoscrizione di un nuovo tenant, è necessario aggiornare manualmente l'ID tenant e rimuovere i criteri di accesso e le assegnazioni di ruolo obsoleti. Ecco le esercitazioni per questi passaggi in PowerShell e nell'interfaccia della riga di comando di Azure. Se si usa PowerShell, potrebbe essere necessario eseguire il comando Clear-AzContext riportato di seguito per consentire all'utente di visualizzare le risorse al di fuori dell'ambito selezionato corrente. 

### <a name="update-tenant-id-in-a-key-vault"></a>Aggiornare l'ID tenant in un insieme di credenziali delle chiavi

```azurepowershell
Select-AzSubscription -SubscriptionId <your-subscriptionId>                # Select your Azure Subscription
$vaultResourceId = (Get-AzKeyVault -VaultName myvault).ResourceId          # Get your key vault's Resource ID 
$vault = Get-AzResource -ResourceId $vaultResourceId -ExpandProperties     # Get the properties for your key vault
$vault.Properties.TenantId = (Get-AzContext).Tenant.TenantId               # Change the Tenant that your key vault resides in
$vault.Properties.AccessPolicies = @()                                     # Access policies can be updated with real
                                                                           # applications/users/rights so that it does not need to be                             # done after this whole activity. Here we are not setting 
                                                                           # any access policies. 
Set-AzResource -ResourceId $vaultResourceId -Properties $vault.Properties  # Modifies the key vault's properties.

Clear-AzContext                                                            #Clear the context from PowerShell
Connect-AzAccount                                                          #Log in again to confirm you have the correct tenant id
````

```azurecli
az account set -s <your-subscriptionId>                                    # Select your Azure Subscription
tenantId=$(az account show --query tenantId)                               # Get your tenantId
az keyvault update -n myvault --remove Properties.accessPolicies           # Remove the access policies
az keyvault update -n myvault --set Properties.tenantId=$tenantId          # Update the key vault tenantId
```
### <a name="update-access-policies-and-role-assignments"></a>Aggiornare i criteri di accesso e le assegnazioni di ruolo

> [!NOTE]
> Se Key Vault usa il modello di autorizzazione [RBAC di Azure](../../role-based-access-control/overview.md) . È necessario rimuovere anche le assegnazioni di ruolo di Key Vault. È possibile rimuovere assegnazioni di ruolo usando il [portale di Azure](../../role-based-access-control/role-assignments-portal.md), l'interfaccia della riga di comando di [Azure](../../role-based-access-control/role-assignments-cli.md)o [PowerShell](../../role-based-access-control/role-assignments-powershell.md). 

Ora che l'insieme di credenziali è associato all'ID tenant corretto e le voci dei criteri di accesso o le assegnazioni di ruolo precedenti sono state rimosse, impostare le nuove voci dei criteri di accesso o le assegnazioni di ruolo.

Per l'assegnazione dei criteri, vedere:
- [Assegnare un criterio di accesso tramite il portale](assign-access-policy-portal.md)
- [Assegnare un criterio di accesso usando l'interfaccia della riga di comando](assign-access-policy-cli.md)
- [Assegnare un criterio di accesso usando PowerShell](assign-access-policy-powershell.md)

Per aggiungere assegnazioni di ruolo, vedere:
- [Assegnare i ruoli di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md)
- [Assegnare i ruoli di Azure usando l'interfaccia della riga di comando](../../role-based-access-control/role-assignments-cli.md)
- [Assegnare i ruoli di Azure tramite PowerShell](../../role-based-access-control/role-assignments-powershell.md)


### <a name="update-managed-identities"></a>Aggiorna identità gestite

Se si trasferisce l'intera sottoscrizione e si usa un'identità gestita per le risorse di Azure, sarà necessario aggiornarla anche alla nuova Azure Active Directory tenant. Per altre informazioni sulle identità gestite, [Panoramica dell'identità gestita](../../active-directory/managed-identities-azure-resources/overview.md).

Se si usa l'identità gestita, sarà necessario aggiornare anche l'identità perché l'identità precedente non sarà più nel tenant Azure Active Directory corretto. Per risolvere il problema, vedere i documenti seguenti. 

* [Aggiornamento di MSI](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)
* [Trasferisce la sottoscrizione alla nuova directory](../../role-based-access-control/transfer-subscription.md)

## <a name="next-steps"></a>Passaggi successivi

- Vedere altre informazioni su [chiavi, segreti e certificati](about-keys-secrets-certificates.md)
- Per informazioni concettuali, incluse le modalità di interpretazione dei log Key Vault, vedere [Key Vault registrazione](logging.md)
- [Guida per gli sviluppatori all'insieme di credenziali delle chiavi](../general/developers-guide.md)
- [Proteggere l'insieme di credenziali delle chiavi](secure-your-key-vault.md)
- [Configurare reti virtuali e firewall di Azure Key Vault](network-security.md)