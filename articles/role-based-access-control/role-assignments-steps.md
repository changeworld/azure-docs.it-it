---
title: Passaggi per assegnare un ruolo di Azure - Controllo degli accessi in base al ruolo di Azure
description: Informazioni sui passaggi per assegnare ruoli di Azure a utenti, gruppi, entità servizio o identità gestite usando il controllo degli accessi in base al ruolo di Azure.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 04/14/2021
ms.author: rolyon
ms.openlocfilehash: 40a17da6383fb1f368c74a82fefa71991cdc1b19
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517675"
---
# <a name="steps-to-assign-an-azure-role"></a>Passaggi per assegnare un ruolo di Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] Questo articolo descrive i passaggi di alto livello per assegnare i ruoli di Azure [usando](role-assignments-portal.md)portale di Azure , [Azure PowerShell,](role-assignments-powershell.md)l'interfaccia della riga di comando di [Azure](role-assignments-cli.md)o l'API [REST](role-assignments-rest.md).

## <a name="step-1-determine-who-needs-access"></a>Passaggio 1: Determinare chi deve accedere

È prima di tutto necessario determinare chi deve accedere. È possibile assegnare un ruolo a un utente, un gruppo, un'entità servizio o un'identità gestita. Si tratta anche di *un'entità di sicurezza*.

![Entità di sicurezza per un'assegnazione di ruolo](./media/shared/rbac-security-principal.png)

- Utente: un soggetto che dispone di un profilo in Azure Active Directory. È anche possibile assegnare ruoli agli utenti in altri tenant. Per informazioni sugli utenti in altre organizzazioni, vedere [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md).
- Gruppo: un set di utenti creato in Azure Active Directory. Quando si assegna un ruolo a un gruppo, tutti gli utenti all'interno di tale gruppo dispongono del ruolo appropriato. 
- Entità servizio: un'identità di sicurezza utilizzata da applicazioni o servizi per accedere a specifiche risorse di Azure. È possibile pensare a questo elemento come a un'*identità utente* (nome utente e password o certificato) per un'applicazione.
- Identità gestita: un'identità in Azure Active Directory che viene gestita automaticamente da Azure. Le [identità gestite](../active-directory/managed-identities-azure-resources/overview.md) vengono in genere usate durante lo sviluppo di applicazioni cloud per gestire le credenziali per l'autenticazione ai servizi di Azure.

## <a name="step-2-select-the-appropriate-role"></a>Passaggio 2: Selezionare il ruolo appropriato

Le autorizzazioni vengono raggruppate in una definizione *di ruolo*. generalmente chiamata *ruolo*. È possibile selezionare da un elenco di diversi ruoli predefiniti. Se i ruoli predefiniti non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati.

![Definizione di ruolo per un'assegnazione di ruolo](./media/shared/rbac-role-definition.png)

Di seguito sono elencati quattro fondamentali ruoli predefiniti. I primi tre si applicano a tutti i tipi di risorse.

- [Proprietario](built-in-roles.md#owner): ha accesso completo a tutte le risorse, compreso il diritto di delegare l'accesso ad altri utenti.
- [Collaboratore](built-in-roles.md#contributor): può creare e gestire tutti i tipi di risorse di Azure, ma non può concedere l'accesso ad altri.
- [Lettore](built-in-roles.md#reader): può visualizzare le risorse di Azure esistenti.
- [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator): consente di gestire l'accesso degli utenti alle risorse di Azure.

Gli altri ruoli predefiniti consentono la gestione di risorse di Azure specifiche. Ad esempio, il ruolo [Collaboratore Macchina virtuale](built-in-roles.md#virtual-machine-contributor) consente a un utente di creare e gestire macchine virtuali.

1. Iniziare con l'articolo completo [Ruoli predefiniti di Azure](built-in-roles.md). La tabella all'inizio dell'articolo è un indice dei dettagli descritti più avanti nell'articolo.

1. In questo articolo passare alla categoria di servizi (ad esempio calcolo, archiviazione e database) per la risorsa a cui si vogliono concedere le autorizzazioni. Il modo più semplice per trovare ciò che si sta cercando è in genere cercare nella pagina una parola chiave pertinente, ad esempio "blob", "macchina virtuale" e così via.

1. Esaminare i ruoli elencati per la categoria di servizio e identificare le operazioni specifiche necessarie. Ancora una volta, iniziare sempre con il ruolo più restrittivo.

    Ad esempio, se un'entità di sicurezza deve leggere i BLOB in un account [](built-in-roles.md#storage-blob-data-reader) di archiviazione di Azure, ma non richiede l'accesso in scrittura, scegliere Lettore dati BLOB di archiviazione anziché Collaboratore dati BLOB di archiviazione [(e](built-in-roles.md#storage-blob-data-contributor) sicuramente non il ruolo proprietario dei dati del BLOB di archiviazione a livello di [amministratore).](built-in-roles.md#storage-blob-data-owner) È sempre possibile aggiornare le assegnazioni di ruolo in un secondo momento, se necessario.

1. Se non si trova un ruolo appropriato, è possibile creare un [ruolo personalizzato.](custom-roles.md)

## <a name="step-3-identify-the-needed-scope"></a>Passaggio 3: Identificare l'ambito necessario

*Ambito* è il set di risorse a cui si applica l'accesso. In Azure è possibile specificare un ambito a quattro [livelli:](../governance/management-groups/overview.md)gruppo di gestione, sottoscrizione, [gruppo di risorse](../azure-resource-manager/management/overview.md#resource-groups)e risorsa. Gli ambiti sono strutturati in una relazione padre-figlio. Ogni livello di gerarchia rende l'ambito più specifico. È possibile assegnare i ruoli a qualsiasi livello dell'ambito. Il livello selezionato determina l'estensione dell'applicazione del ruolo. I livelli inferiori ereditano le autorizzazioni del ruolo dai livelli superiori. 

![Ambito per un'assegnazione di ruolo](./media/shared/rbac-scope.png)

Quando si assegna un ruolo in un ambito padre, tali autorizzazioni vengono ereditate agli ambiti figlio. Ad esempio:

- Se si assegna il [ruolo Lettore](built-in-roles.md#reader) a un utente nell'ambito del gruppo di gestione, tale utente può leggere tutti gli elementi in tutte le sottoscrizioni del gruppo di gestione.
- Se si assegna il [ruolo](built-in-roles.md#billing-reader) Lettore fatturazione a un gruppo nell'ambito della sottoscrizione, i membri di tale gruppo possono leggere i dati di fatturazione per ogni gruppo di risorse e risorsa nella sottoscrizione.
- Se si assegna il ruolo [Collaboratore](built-in-roles.md#contributor) a un'applicazione nell'ambito del gruppo di risorse, è possibile gestire risorse di tutti i tipi in quel gruppo di risorse, ma non altri gruppi di risorse nella sottoscrizione.

 Per altre informazioni, vedere Informazioni [sull'ambito.](scope-overview.md)

## <a name="step-4-check-your-prerequisites"></a>Passaggio 4. Verificare i prerequisiti

Per assegnare i ruoli, è necessario essere connessi con un utente a cui [](built-in-roles.md#owner) è assegnato un ruolo con l'autorizzazione di scrittura per le assegnazioni di ruolo, ad esempio Proprietario o Amministratore [Accesso](built-in-roles.md#user-access-administrator) utenti nell'ambito a cui si sta tentando di assegnare il ruolo. Analogamente, per rimuovere un'assegnazione di ruolo, è necessario disporre dell'autorizzazione di eliminazione delle assegnazioni di ruolo.

- `Microsoft.Authorization/roleAssignments/write`
- `Microsoft.Authorization/roleAssignments/delete`

Se l'account utente non ha le autorizzazioni per assegnare un ruolo all'interno della sottoscrizione, verrà visualizzato un messaggio di errore che segnala che l'account non è autorizzato a eseguire l'azione 'Microsoft.Authorization/roleAssignments/write'. In questo caso, contattare gli amministratori della sottoscrizione in quanto possono assegnare le autorizzazioni per conto dell'utente.

Se si usa un'entità servizio per assegnare i ruoli, è possibile che venga visualizzato l'errore "Privilegi insufficienti per completare l'operazione". Questo errore è probabilmente dovuto al fatto che Azure sta tentando di cercare l'identità dell'assegnatare in Azure Active Directory (Azure AD) e l'entità servizio non può leggere Azure AD per impostazione predefinita. In questo caso, è necessario concedere all'entità servizio le autorizzazioni per leggere i dati nella directory. In alternativa, se si usa l'interfaccia della riga di comando di Azure, è possibile creare l'assegnazione di ruolo usando l'ID oggetto dell'assegnatare per ignorare la Azure AD ricerca. Per altre informazioni, vedere Risolvere i problemi relativi [al controllo degli accessi in base al ruolo di Azure.](troubleshooting.md)

## <a name="step-5-assign-role"></a>Passaggio 5. Assegnare il ruolo

Una volta che si conoscono l'entità di sicurezza, il ruolo e l'ambito, è possibile assegnare il ruolo. È possibile assegnare i ruoli usando portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure, Azure SDK o le API REST. È possibile avere fino a **2000** assegnazioni di ruolo in ogni sottoscrizione. Questo limite include le assegnazioni di ruolo negli ambiti di sottoscrizione, gruppo di risorse e risorsa. È possibile avere fino a **500** assegnazioni di ruolo in ogni sottoscrizione.

Per informazioni dettagliate su come assegnare i ruoli, vedere gli articoli seguenti.

- [Assegnare i ruoli di Azure usando il portale di Azure](role-assignments-portal.md)
- [Assegnare ruoli di Azure usando Azure PowerShell](role-assignments-powershell.md)
- [Assegnare ruoli di Azure tramite l'interfaccia della riga di comando di Azure](role-assignments-cli.md)
- [Assegnare ruoli di Azure usando l'API REST](role-assignments-rest.md)

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: Concedere l'accesso alle risorse di Azure a un utente usando il portale di Azure](quickstart-assign-role-user-portal.md)