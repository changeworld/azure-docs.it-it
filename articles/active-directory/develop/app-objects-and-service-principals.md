---
title: App & entità servizio in Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Informazioni sulla relazione tra oggetti applicazione e oggetti entità servizio in Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/16/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: fc1b5356ab607ecb60a457a7295831958e6815e1
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727061"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Oggetti applicazione e oggetti entità servizio in Azure Active Directory

Questo articolo descrive la registrazione dell'applicazione, gli oggetti applicazione e le entità servizio in Azure Active Directory: cosa sono, come vengono usate e come sono correlate tra loro. Viene presentato anche uno scenario di esempio multi-tenant per illustrare la relazione tra l'oggetto applicazione di un'applicazione e gli oggetti entità servizio corrispondenti.

## <a name="application-registration"></a>Registrazione dell'applicazione
Per delegare le funzioni di gestione delle identità e degli accessi Azure AD, è necessario registrare un'applicazione con un [tenant Azure AD.](developer-glossary.md#tenant) Quando si registra l'applicazione con Azure AD, si crea una configurazione di identità per l'applicazione che consente l'integrazione con Azure AD. Quando si registra un'app nel [portale di Azure][AZURE-Portal], si sceglie se si tratta di un tenant singolo (accessibile solo nel tenant) o multi-tenant (accessibile in altri tenant) e facoltativamente è possibile impostare un URI di reindirizzamento (a cui viene inviato il token di accesso).

Per istruzioni dettagliate sulla registrazione di un'app, vedere l'avvio rapido [per la registrazione dell'app.](quickstart-register-app.md)

Dopo aver completato la registrazione dell'app, si ha un'istanza univoca globale dell'app (l'oggetto [applicazione](#application-object)) che si trova all'interno del tenant o della directory principale.  Si ha anche un ID univoco globale per l'app (l'ID app o client).  Nel portale è quindi possibile aggiungere segreti o certificati e ambiti per far funzionare l'app, personalizzare la personalizzazione dell'app nella finestra di dialogo di accesso e altro ancora.

Se si registra un'applicazione nel portale, un oggetto applicazione e un oggetto entità servizio vengono creati automaticamente nel tenant principale.  Se si registra o si crea un'applicazione usando le API Microsoft Graph, la creazione dell'oggetto entità servizio è un passaggio separato.

## <a name="application-object"></a>Oggetto applicazione
Un'applicazione Azure AD è definita dal relativo unico oggetto applicazione, che risiede nel tenant Azure AD in cui è stata registrata l'applicazione (noto come tenant "home" dell'applicazione).  Un oggetto applicazione viene usato come modello o progetto per creare uno o più oggetti entità servizio.  Viene creata un'entità servizio in ogni tenant in cui viene usata l'applicazione. Analogamente a una classe nella programmazione orientata a oggetti, l'oggetto applicazione ha alcune proprietà statiche che vengono applicate a tutte le entità servizio (o istanze dell'applicazione) create.

L'oggetto applicazione descrive tre aspetti di un'applicazione: il modo in cui il servizio può rilasciare token per accedere all'applicazione, le risorse a cui l'applicazione potrebbe dover accedere e le azioni che l'applicazione può eseguire.

Il **pannello Registrazioni app** nel [portale di Azure][AZURE-Portal] viene usato per elencare e gestire gli oggetti applicazione nel tenant principale.

![Registrazioni app pannello](./media/app-objects-and-service-principals/app-registrations-blade.png)

[L Microsoft Graph'entità Application][MS-Graph-App-Entity] definisce lo schema per le proprietà di un oggetto applicazione.

## <a name="service-principal-object"></a>Oggetto entità servizio
Per accedere alle risorse protette da un tenant di Azure AD, l'entità che richiede l'accesso deve essere rappresentata da un'entità di sicurezza. Questo requisito è vero sia per gli utenti (entità utente) che per le applicazioni (entità servizio). L'entità di sicurezza definisce i criteri di accesso e le autorizzazioni per l'utente/applicazione nel tenant di Azure AD. Ciò abilita le funzionalità di base, ad esempio l'autenticazione dell'utente/applicazione durante l'accesso e l'autorizzazione durante l'accesso alle risorse.

Esistono tre tipi di entità servizio: applicazione, identità gestita e legacy.

Il primo tipo di entità servizio è la rappresentazione locale, o istanza dell'applicazione, di un oggetto applicazione globale in un singolo tenant o directory. In questo caso, un'entità servizio è un'istanza concreta creata dall'oggetto applicazione ed eredita determinate proprietà da tale oggetto applicazione. Un'entità servizio viene creata in ogni tenant in cui viene usata l'applicazione e fa riferimento all'oggetto app univoco a livello globale.  L'oggetto entità servizio definisce ciò che l'app può effettivamente eseguire nel tenant specifico, chi può accedere all'app e a quali risorse può accedere l'app.

Quando a un'applicazione viene concesso di accedere alle risorse in un tenant (al momento della registrazione o del [consenso](developer-glossary.md#consent)), viene creato un oggetto entità servizio. È anche possibile creare oggetti entità servizio in un tenant usando Azure PowerShell [,](howto-authenticate-service-principal-powershell.md)l'interfaccia della riga di comando di [Azure,](/cli/azure/create-an-azure-service-principal-azure-cli) [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http), portale di Azure [e][AZURE-Portal]altri strumenti. Quando si usa il portale, un'entità servizio viene creata automaticamente quando si registra un'applicazione.

Il secondo tipo di entità servizio viene usato per rappresentare [un'identità gestita.](/azure/active-directory/managed-identities-azure-resources/overview) Le identità gestite eliminano la necessità per gli sviluppatori di gestire le credenziali. Le identità gestite forniscono un'identità che le applicazioni possono usare per la connessione alle risorse che supportano Azure AD autenticazione. Quando un'identità gestita è abilitata, nel tenant viene creata un'entità servizio che rappresenta tale identità gestita. Alle entità servizio che rappresentano le identità gestite possono essere concesse l'accesso e le autorizzazioni, ma non possono essere aggiornate o modificate direttamente.

Il terzo tipo di entità servizio rappresenta un'app legacy (un'app creata prima dell'introduzione o della creazione delle registrazioni dell'app tramite esperienze legacy). Un'entità servizio legacy può avere credenziali, nomi di entità servizio, URL di risposta e altre proprietà modificabili da un utente autorizzato, ma senza una registrazione dell'app associata. L'entità servizio può essere usata solo nel tenant in cui è stata creata.

[L Microsoft Graph'entità ServicePrincipal][MS-Graph-Sp-Entity] definisce lo schema per le proprietà di un oggetto entità servizio.

Il **pannello Applicazioni aziendali** nel portale viene usato per elencare e gestire le entità servizio in un tenant. È possibile visualizzare le autorizzazioni dell'entità servizio, le autorizzazioni concesse dall'utente, gli utenti che hanno eseguito il consenso, le informazioni di accesso e altro ancora.

![Pannello App aziendali](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

## <a name="relationship-between-application-objects-and-service-principals"></a>Relazione tra oggetti applicazione ed entità servizio

L'oggetto applicazione è la *rappresentazione* globale dell'applicazione da usare  in tutti i tenant e l'entità servizio è la rappresentazione locale da usare in un tenant specifico.

L'oggetto applicazione funge da modello da cui *derivano* le proprietà comuni e predefinite per l'uso nella creazione di oggetti entità servizio corrispondenti. Un oggetto applicazione ha quindi una relazione 1:1 con l'applicazione software e una relazione 1:molti con gli oggetti entità servizio corrispondenti.

In ogni tenant in cui viene usata l'applicazione è necessario creare un'entità servizio per poter stabilire un'identità per l'iscrizione e/o l'accesso alle risorse che venga protetta da un tenant. Un'applicazione single-tenant ha una sola entità servizio (nel relativo tenant principale), creata e autorizzata per essere usata durante la registrazione dell'applicazione. Un'applicazione multi-tenant ha anche un'entità servizio creata in ogni tenant in cui un utente di tale tenant ha acconsentito all'uso.

### <a name="consequences-of-modifying-and-deleting-applications"></a>Conseguenze della modifica e dell'eliminazione di applicazioni
Tutte le modifiche apportate all'oggetto applicazione vengono riflesse anche nel relativo oggetto entità servizio solo nel tenant principale dell'applicazione (tenant in cui è stato registrato). Ciò significa che l'eliminazione di un oggetto applicazione eliminerà anche l'oggetto entità servizio del tenant principale.  Tuttavia, il ripristino dell'oggetto applicazione non ripristini l'entità servizio corrispondente. Per le applicazioni multi-tenant, le modifiche apportate all'oggetto applicazione non vengono riflesse negli oggetti entità servizio dei tenant consumer fino a quando non viene rimosso l'accesso tramite il [Pannello di accesso all'applicazione](https://myapps.microsoft.com) e poi concesso nuovamente.

## <a name="example"></a>Esempio

Il diagramma seguente illustra la relazione tra l'oggetto applicazione e i corrispondenti oggetti entità servizio di un'applicazione, nel contesto di un'applicazione multi-tenant di esempio denominata **app HR**. In questo scenario di esempio sono presenti tre tenant di Azure AD:

- **Adatum**, il tenant usato dalla società che ha sviluppato l'**app HR**
- **Contoso**, il tenant usato dall'organizzazione Contoso, che utilizza l'**app HR**
- **Fabrikam**, il tenant usato dall'organizzazione Fabrikam, che utilizza anche in questo caso l'**app HR**

![Relazione tra l'oggetto app e l'oggetto entità servizio](./media/app-objects-and-service-principals/application-objects-relationship.svg)

In questo scenario di esempio:

| Passaggio | Descrizione |
|------|-------------|
| 1    | È il processo di creazione degli oggetti applicazione ed entità servizio nel tenant principale dell'applicazione. |
| 2    | Quando gli amministratori di Contoso e Fabrikam completano il consenso, nel tenant di Azure AD della rispettiva società viene creato un oggetto entità servizio a cui vengono assegnate le autorizzazioni concesse dall'amministratore. Si noti anche che l'app HR potrebbe essere configurata/progettata per permettere il consenso da parte di utenti per l'uso individuale. |
| 3    | I tenant consumer dell'applicazione HR (Contoso e Fabrikam) dispongono ognuno del proprio oggetto entità servizio. Ognuno rappresenta l'uso di un'istanza dell'applicazione in fase di runtime, gestito tramite le autorizzazioni concesse dall'amministratore. |

## <a name="next-steps"></a>Passaggi successivi

- È possibile usare esplora Microsoft Graph [per eseguire](https://developer.microsoft.com/graph/graph-explorer) query sia sull'applicazione che sugli oggetti entità servizio.
- È possibile accedere all'oggetto applicazione di [un'applicazione usando l'API Microsoft Graph, l'editor][AZURE-Portal] del manifesto dell'applicazione del portale di Azure o i cmdlet di [PowerShell Azure AD](/powershell/azure/), come rappresentato dalla relativa entità applicazione [OData][MS-Graph-App-Entity].
- È possibile accedere all'oggetto entità servizio di un'applicazione tramite l'API Microsoft Graph o i [cmdlet di PowerShell Azure AD](/powershell/azure/), come rappresentato dalla relativa entità [ServicePrincipal][MS-Graph-Sp-Entity]OData.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
