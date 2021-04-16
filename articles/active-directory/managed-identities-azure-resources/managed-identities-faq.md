---
title: Domande frequenti sulle identità gestite per le risorse di Azure - Azure AD"
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
ms.openlocfilehash: 07b106630cffae75c5e4588d14de7ae938945614
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534128"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Domande frequenti sulle identità gestite per le risorse di Azure - Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Identità gestite per le risorse di Azure è il nuovo nome per il servizio precedentemente noto come identità del servizio gestita.

## <a name="administration"></a>Amministrazione

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Come è possibile trovare risorse con un'identità gestita?

È possibile trovare l'elenco delle risorse con un'identità gestita assegnata dal sistema usando il comando dell'interfaccia della riga di comando di Azure seguente: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Quali autorizzazioni di controllo degli accessi in base al ruolo di Azure sono necessarie per un'identità gestita in una risorsa? 

- Identità gestita assegnata dal sistema: Sono necessarie autorizzazioni di scrittura per la risorsa. Per le macchine virtuali, ad esempio, è necessario Microsoft.Compute/virtualMachines/write. Questa azione è inclusa in ruoli predefiniti specifici della risorsa come [Collaboratore Macchina virtuale](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).
- Identità gestita assegnata dall'utente: Sono necessarie autorizzazioni di scrittura per la risorsa. Per le macchine virtuali, ad esempio, è necessario Microsoft.Compute/virtualMachines/write. Oltre all'assegnazione di ruolo [Operatore di identità gestite](../../role-based-access-control/built-in-roles.md#managed-identity-operator) per l'identità gestita.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Ricerca per categorie la creazione di identità gestite assegnate dall'utente?

È possibile evitare che gli utenti creino identità gestite assegnate dall'utente [usando](../../governance/policy/overview.md) Criteri di Azure

1. Passare al [portale di Azure](https://portal.azure.com) e passare a **Criteri**.
2. Scegliere **le definizioni**
3. Selezionare **+ Definizione criteri** e immettere le informazioni necessarie.
4. Nella sezione delle regole dei criteri incollare
    
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

1. Passare ai gruppi di risorse.
2. Trovare il gruppo di risorse in uso per i test.
3. Scegliere **Criteri** dal menu a sinistra.
4. Selezionare **Assegna criterio**
5. Nella sezione **Informazioni di** base specificare:
    1. **Ambito** Gruppo di risorse in uso per i test
    1. **Definizione dei criteri:** criteri creati in precedenza.
6. Lasciare le impostazioni predefinite per tutte le altre impostazioni e scegliere **Rivedi e crea**

A questo punto, qualsiasi tentativo di creare un'identità gestita assegnata dall'utente nel gruppo di risorse avrà esito negativo.

  ![Violazione dei criteri](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Concetti

### <a name="do-managed-identities-have-a-backing-app-object"></a>Le identità gestite hanno un oggetto app di backup?

No. Le identità gestite App Azure AD le registrazioni non sono la stessa cosa nella directory.

Registrazioni app due componenti: un oggetto applicazione + un oggetto entità servizio.
Le identità gestite per le risorse di Azure hanno solo uno di questi componenti: un oggetto entità servizio.

Le identità gestite non hanno un oggetto applicazione nella directory, ovvero ciò che viene comunemente usato per concedere le autorizzazioni dell'app per MS Graph. Le autorizzazioni ms graph per le identità gestite devono invece essere concesse direttamente all'entità servizio.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Qual è la credenziale associata a un'identità gestita? Per quanto tempo è valida e con quale frequenza viene ruotata?

> [!NOTE]
> La modalità di autenticazione delle identità gestite è un dettaglio di implementazione interno soggetto a modifiche senza preavviso.

Le identità gestite usano l'autenticazione basata su certificati. Ogni credenziale di ogni identità gestita ha una scadenza di 90 giorni e ne viene eseguito il roll roll dopo 45 giorni.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Quale identità sarà predefinita da IMDS se non si specifica l'identità nella richiesta?

- Se l'identità gestita assegnata dal sistema è abilitata e non viene specificata alcuna identità nella richiesta, IMDS imposta per impostazione predefinita l'identità gestita assegnata dal sistema.
- Se l'identità gestita assegnata dal sistema non è abilitata ed esiste una sola identità gestita assegnata dall'utente, per impostazione predefinita IMDS viene assegnata a tale identità gestita assegnata dall'utente singolo.
- Se l'identità gestita assegnata dal sistema non è abilitata ed esistono più identità gestite assegnate dall'utente, è necessario specificare un'identità gestita nella richiesta.

## <a name="limitations"></a>Limitazioni

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>È possibile usare la stessa identità gestita in più aree?

In breve, sì, è possibile usare le identità gestite assegnate dall'utente in più aree di Azure. La risposta più lunga è che, mentre le identità gestite assegnate dall'utente vengono create come risorse regionali, l'entità servizio [associata](../develop/app-objects-and-service-principals.md#service-principal-object) creata in Azure AD è disponibile a livello globale. L'entità servizio può essere usata da qualsiasi area di Azure e la relativa disponibilità dipende dalla disponibilità Azure AD. Ad esempio, se è stata creata un'identità gestita assegnata dall'utente nell'area South-Central e tale area diventa non disponibile, questo problema influisce solo sulle attività del piano di controllo sull'identità gestita stessa. [](../../azure-resource-manager/management/control-plane-and-data-plane.md)  Le attività eseguite da tutte le risorse già configurate per l'uso delle identità gestite non verrebbero influenzate.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Le identità gestite per le risorse di Azure funzionano con i Servizi cloud di Azure?

No, non sono previste iniziative per supportare le identità gestite per le risorse di Azure nei Servizi cloud di Azure.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Cosa si intende per limite di sicurezza delle identità gestite per le risorse di Azure?

Il limite di sicurezza dell'identità è la risorsa a cui è collegata. Ad esempio, il limite di sicurezza per una macchina virtuale con le identità gestite per le risorse di Azure abilitate è la macchina virtuale. Qualsiasi codice in esecuzione su quella macchina virtuale può chiamare l'endpoint delle identità gestite per le risorse di Azure e richiedere i token. L'esperienza è simile a quella con altre risorse che supportano le identità gestite per le risorse di Azure.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Le identità gestite saranno ricreate automaticamente se si sposta una sottoscrizione in un'altra directory?

No. Se si sposta una sottoscrizione in un'altra directory, sarà necessario crearla manualmente e concedere nuovamente le assegnazioni di ruolo di Azure.
- Per le identità gestite assegnate dal sistema: disabilitare e abilitare di nuovo. 
- Per le identità gestite assegnate dall'utente: eliminare, creare di nuovo e collegarle di nuovo alle risorse necessarie (ad esempio, macchine virtuali)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>È possibile usare un'identità gestita per accedere a risorse in tenant/directory diversi?

No. Le identità gestite attualmente non supportano gli scenari tra directory. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Sono previsti limiti di frequenza che si applicano alle identità gestite?

I limiti delle identità gestite hanno dipendenze dai limiti del servizio di Azure, dai limiti del servizio metadati dell'istanza di Azure e Azure Active Directory dei servizi.

- **I limiti del servizio di Azure** definiscono il numero di operazioni di creazione che possono essere eseguite a livello di tenant e sottoscrizione. Anche le identità gestite assegnate dall'utente [presentano limitazioni](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) relative al modo in cui possono essere denominate.
- **IMDS** In generale, le richieste a IMDS sono limitate a cinque richieste al secondo. Le richieste che superano questa soglia verranno rifiutate con 429 risposte. Le richieste alla categoria identità gestita sono limitate a 20 richieste al secondo e 5 richieste simultanee. Per altre informazioni, vedere [l'articolo Servizio metadati dell'istanza di Azure (Windows).](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity)
- **Azure Active Directory servizio** Ogni identità gestita viene conteggiato per il limite di quota degli oggetti in un tenant Azure AD come descritto in Limiti e restrizioni del servizio Azure [AD.](../enterprise-users/directory-service-limits-restrictions.md)


### <a name="is-it-possible-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>È possibile spostare un'identità gestita assegnata dall'utente in un gruppo di risorse/sottoscrizione diverso?

Lo spostamento di un'identità gestita assegnata dall'utente in un gruppo di risorse diverso non è supportato.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni [sul funzionamento delle identità gestite con le macchine virtuali](how-managed-identities-work-vm.md)
