---
title: Informazioni frequenti sulle identità gestite per le risorse di Azure-Azure AD "
description: Domande frequenti sulle identità gestite
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.openlocfilehash: 3d3ab9859eb9f85d5ca7d0573fa79443ae9fe964
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251008"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Identità gestite per le domande frequenti sulle risorse di Azure-Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.

## <a name="administration"></a>Amministrazione

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Come è possibile trovare le risorse con un'identità gestita?

È possibile trovare l'elenco delle risorse che hanno un'identità gestita assegnata dal sistema usando il comando dell'interfaccia della riga di comando di Azure seguente: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Quali autorizzazioni di controllo degli accessi in base al ruolo di Azure sono necessarie per un'identità gestita in una risorsa? 

- Identità gestita assegnata dal sistema: Sono necessarie autorizzazioni di scrittura per la risorsa. Per le macchine virtuali, ad esempio, è necessario Microsoft.Compute/virtualMachines/write. Questa azione è inclusa in ruoli predefiniti specifici della risorsa come [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Identità gestita assegnata dall'utente: Sono necessarie autorizzazioni di scrittura per la risorsa. Per le macchine virtuali, ad esempio, è necessario Microsoft.Compute/virtualMachines/write. Oltre all'assegnazione di ruolo [Operatore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-operator) per l'identità gestita.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Ricerca per categorie impedire la creazione di identità gestite assegnate dall'utente?

È possibile impedire agli utenti di creare identità gestite assegnate dall'utente usando [criteri di Azure](../../governance/policy/overview.md)

1. Passare alla [portale di Azure](https://portal.azure.com) e passare a **criterio**.
2. Scegli **definizioni**
3. Selezionare **+ definizione criteri** e immettere le informazioni necessarie.
4. Nella sezione della regola dei criteri incollare
    
    ```json
    {
      "mode": "All",
      "policyRule": {
        "if": {
          "field": "type",
          "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    
    ```

Dopo aver creato il criterio, assegnarlo al gruppo di risorse che si vuole usare.

1. Passare a gruppi di risorse.
2. Trovare il gruppo di risorse usato per il test.
3. Scegliere **criteri** dal menu a sinistra.
4. Selezionare **assegna criterio**
5. Nella sezione **nozioni di base** fornire:
    1. **Ambito** Il gruppo di risorse usato per il test
    1. **Definizione dei criteri**: il criterio creato in precedenza.
6. Lasciare le impostazioni predefinite per tutte le altre impostazioni e scegliere **Verifica + crea**

A questo punto, qualsiasi tentativo di creare un'identità gestita assegnata dall'utente nel gruppo di risorse avrà esito negativo.

  ![Violazione dei criteri](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Concetti

### <a name="do-managed-identities-have-a-backing-app-object"></a>Le identità gestite hanno un oggetto app di supporto?

No. Le identità gestite e le registrazioni App Azure AD non sono la stessa cosa nella directory.

Registrazioni app hanno due componenti: un oggetto applicazione e un oggetto entità servizio.
Le identità gestite per le risorse di Azure hanno solo uno di questi componenti: un oggetto entità servizio.

Le identità gestite non hanno un oggetto applicazione nella directory, ovvero ciò che viene comunemente usato per concedere le autorizzazioni dell'app per MS Graph. Al contrario, le autorizzazioni di Microsoft Graph per le identità gestite devono essere concesse direttamente all'entità servizio.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Quali sono le credenziali associate a un'identità gestita? Quanto tempo è valido e con quale frequenza viene ruotato?

> [!NOTE]
> Il modo in cui le identità gestite si autenticano è un dettaglio di implementazione interno soggetto a modifiche senza preavviso.

Le identità gestite usano l'autenticazione basata su certificati. Le credenziali di ogni identità gestita hanno una scadenza di 90 giorni ed è stato eseguito il rollback dopo 45 giorni.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Quale identità sarà predefinita da IMDS se non si specifica l'identità nella richiesta?

- Se l'identità gestita assegnata dal sistema è abilitata e non viene specificata alcuna identità nella richiesta, il valore predefinito di IMDS è l'identità gestita assegnata dal sistema.
- Se l'identità gestita assegnata dal sistema non è abilitata e esiste una sola identità gestita assegnata dall'utente, IMDS utilizza per impostazione predefinita l'identità gestita assegnata dall'utente singolo.
- Se l'identità gestita assegnata dal sistema non è abilitata e sono presenti più identità gestite assegnate dall'utente, è necessario specificare un'identità gestita nella richiesta.

## <a name="limitations"></a>Limitazioni

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>È possibile usare la stessa identità gestita in più aree?

In breve, sì, è possibile usare le identità gestite assegnate dall'utente in più di un'area di Azure. La risposta più lunga è che, mentre le identità gestite assegnate dall'utente vengono create come risorse internazionali, l' [entità servizio](../develop/app-objects-and-service-principals.md#service-principal-object) (SP) associata creata in Azure ad è disponibile a livello globale. L'entità servizio può essere usata da qualsiasi area di Azure e la relativa disponibilità dipende dalla disponibilità di Azure AD. Se, ad esempio, è stata creata un'identità gestita assegnata dall'utente nell'area South-Central e tale area non è più disponibile, questo problema influisca solo sulle attività del [piano di controllo](../../azure-resource-manager/management/control-plane-and-data-plane.md) sull'identità gestita stessa.  Le attività eseguite dalle risorse già configurate per l'utilizzo delle identità gestite non saranno interessate.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Le identità gestite per le risorse di Azure funzionano con i Servizi cloud di Azure?

No, non sono previste iniziative per supportare le identità gestite per le risorse di Azure nei Servizi cloud di Azure.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Cosa si intende per limite di sicurezza delle identità gestite per le risorse di Azure?

Il limite di sicurezza dell'identità è la risorsa a cui è collegata. Ad esempio, il limite di sicurezza per una macchina virtuale con le identità gestite per le risorse di Azure abilitate è la macchina virtuale. Qualsiasi codice in esecuzione su quella macchina virtuale può chiamare l'endpoint delle identità gestite per le risorse di Azure e richiedere i token. L'esperienza è simile a quella con altre risorse che supportano le identità gestite per le risorse di Azure.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Le identità gestite saranno ricreate automaticamente se si sposta una sottoscrizione in un'altra directory?

No. Se si sposta una sottoscrizione in un'altra directory, sarà necessario ricrearla manualmente e concedere nuovamente le assegnazioni di ruolo di Azure.
- Per le identità gestite assegnate dal sistema: disabilitare e abilitare di nuovo. 
- Per le identità gestite assegnate dall'utente: eliminare, ricreare e collegarle nuovamente alle risorse necessarie, ad esempio macchine virtuali.

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>È possibile usare un'identità gestita per accedere a risorse in tenant/directory diversi?

No. Le identità gestite attualmente non supportano gli scenari tra directory. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Sono previsti limiti di velocità che si applicano alle identità gestite?

I limiti delle identità gestite presentano dipendenze dai limiti dei servizi di Azure, i limiti del servizio metadati dell'istanza di Azure (IMDS) e i limiti del servizio Azure Active Directory.

- I **limiti dei servizi di Azure** definiscono il numero di operazioni di creazione che possono essere eseguite a livello di tenant e di sottoscrizione. Le identità gestite assegnate dall'utente presentano inoltre [limitazioni relative](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) alla modalità di denominazione.
- **IMDS** In generale, le richieste a IMDS sono limitate a cinque richieste al secondo. Le richieste che superano questa soglia verranno rifiutate con 429 risposte. Le richieste alla categoria identità gestita sono limitate a 20 richieste al secondo e 5 richieste simultanee. Altre informazioni sono disponibili nell'articolo relativo ai [metadati dell'istanza di Azure (Windows)](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity) .
- **Servizio Azure Active Directory** Ogni identità gestita viene conteggiata per il limite di quota dell'oggetto in un tenant di Azure AD come descritto in [limiti e restrizioni del servizio Azure ad](../enterprise-users/directory-service-limits-restrictions.md).


## <a name="is-it-ok-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>È possibile spostare un'identità gestita assegnata dall'utente in una sottoscrizione o un gruppo di risorse diverso?

Lo spostamento di un'identità gestita assegnata dall'utente in un gruppo di risorse diverso non è supportato.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sul [funzionamento delle identità gestite con le macchine virtuali](how-managed-identities-work-vm.md)
