---
title: Controllo di accesso del modulo di protezione HSM gestito di Azure
description: Gestire le autorizzazioni di accesso per le chiavi e il modulo di protezione HSM gestito di Azure. Illustra il modello di autenticazione e autorizzazione per il servizio HSM gestito e come proteggere i moduli di protezione.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: ambapat
ms.openlocfilehash: bea1ccf0777c6325bc86c15e0f88304c465d89c9
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750285"
---
# <a name="managed-hsm-access-control"></a>Controllo di accesso per il modulo di protezione hardware gestito

> [!NOTE]
> Key Vault provider di risorse supporta due tipi di risorse: **insiemi di** credenziali **e HMS gestiti.** Il controllo di accesso descritto in questo articolo si applica solo **ai HMS gestiti.** Per altre informazioni sul controllo di accesso per il modulo di protezione HSM gestito, vedere Fornire l'accesso Key Vault chiavi, certificati e segreti con un controllo degli accessi in base [al ruolo di Azure.](../general/rbac-guide.md)

Il modulo di protezione hardware gestito di Azure Key Vault è un servizio cloud che consente di proteggere le chiavi di crittografia. Poiché questi dati sono riservati e importanti per l'azienda, è necessario proteggere i moduli di protezione hardware gestiti consentendo l'accesso solo ad applicazioni e utenti autorizzati. Questo articolo offre una panoramica del modello di controllo di accesso del modulo di protezione hardware gestito. Verranno illustrate l'autenticazione e l'autorizzazione e sarà descritto come proteggere l'accesso ai moduli di protezione hardware gestiti.

## <a name="access-control-model"></a>Modello di controllo di accesso

L'accesso a un HSM gestito è controllato tramite due interfacce: il **piano di gestione** e il piano **dati**. Il piano di gestione è il punto in cui si gestisce il HSM stesso. Le operazioni in questo piano includono la creazione e l'eliminazione di HSM gestiti e il recupero delle proprietà del servizio HSM gestito. Il piano dati è il punto in cui si lavora con i dati archiviati in un HSM gestito, cio' chiavi di crittografia supportate dal servizio HSM. È possibile aggiungere, eliminare, modificare e usare le chiavi per eseguire operazioni di crittografia, gestire le assegnazioni di ruolo per controllare l'accesso alle chiavi, creare un backup completo del modulo di protezione HSM, ripristinare il backup completo e gestire il dominio di sicurezza dall'interfaccia del piano dati.

Per accedere a un HSM gestito in entrambi i piani, tutti i chiamanti devono disporre dell'autenticazione e dell'autorizzazione appropriate. L'autenticazione stabilisce l'identità del chiamante. L'autorizzazione determina le operazioni che il chiamante può eseguire. Un chiamante può essere [](../../role-based-access-control/overview.md#security-principal) una qualsiasi delle entità di sicurezza definite in Azure Active Directory utente, gruppo, entità servizio o identità gestita.

Entrambi i piani usano Azure Active Directory per l'autenticazione. Per l'autorizzazione usano sistemi diversi, come indicato di seguito
- Il piano di gestione usa il controllo degli accessi in base al ruolo di Azure, il controllo degli accessi in base al ruolo di Azure, un sistema di autorizzazione basato su Azure Azure Resource Manager 
- Il piano dati usa un controllo degli accessi in base al ruolo gestito a livello di modulo di protezione dell'HSM , ovvero un sistema di autorizzazione implementato e applicato a livello di modulo di protezione HSM gestito.

Quando viene creato un modulo di protezione hsm gestito, il richiedente fornisce anche un elenco di amministratori del piano dati (sono supportate [tutte le entità](../../role-based-access-control/overview.md#security-principal) di sicurezza). Solo questi amministratori possono accedere al piano dati HSM gestito per eseguire operazioni chiave e gestire le assegnazioni di ruolo del piano dati (controllo degli accessi in base al ruolo locale del modulo di protezione dell'HSM gestito).

Il modello di autorizzazione per entrambi i piani usa la stessa sintassi, ma vengono applicati a livelli diversi e le assegnazioni di ruolo usano ambiti diversi. Il controllo degli accessi in base al ruolo di Azure del piano di gestione viene applicato dall'Azure Resource Manager mentre il controllo degli accessi in base al ruolo locale del modulo di protezione dell'HSM gestito del piano dati viene applicato dal modulo di protezione HSM gestito stesso.

> [!IMPORTANT]
> La concessione a un piano di gestione dell'entità di sicurezza dell'accesso a un modulo di protezione HSM gestito non concede loro alcun accesso al piano dati per accedere alle chiavi o alle assegnazioni di ruolo del piano dati Controllo degli accessi in base al ruolo locale del modulo di protezione delle chiavi gestito. Questo isolamento è progettato per impedire l'espansione accidentale dei privilegi che influiscono sull'accesso alle chiavi archiviate in HSM gestito.

Ad esempio, un amministratore della sottoscrizione (poiché dispone dell'autorizzazione "Collaboratore" per tutte le risorse nella sottoscrizione) può eliminare un modulo di protezione dell'ambiente di protezione gestito nella sottoscrizione, ma se non dispone dell'accesso al piano dati concesso in modo specifico tramite il controllo degli accessi in base al ruolo locale del modulo di protezione dell'account gestito, non può accedere alle chiavi o gestire l'assegnazione di ruolo nel modulo di protezione hsm gestito per concedere a se stessi o ad altri utenti l'accesso al piano dati.

## <a name="azure-active-directory-authentication"></a>Autenticazione di Azure Active Directory

Quando si crea un modulo di protezione hsm gestito in una sottoscrizione di Azure, questo viene associato automaticamente al tenant Azure Active Directory della sottoscrizione. Tutti i chiamanti in entrambi i piani devono essere registrati in questo tenant ed eseguire l'autenticazione per accedere al servizio HSM gestito.

L'applicazione esegue l'autenticazione con Azure Active Directory prima di chiamare uno dei due piani. L'applicazione può usare qualsiasi [metodo di autenticazione supportato](../../active-directory/develop/authentication-vs-authorization.md) in base al tipo di applicazione. L'applicazione acquisisce un token per una risorsa nel piano per ottenere l'accesso. La risorsa è un endpoint nel piano dati o di gestione, in base all'ambiente di Azure. L'applicazione usa il token e invia una richiesta API REST all'endpoint HSM gestito. Per altre informazioni, vedere l'[intero flusso di autenticazione](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

L'uso di un singolo meccanismo di autenticazione per entrambi i piani offre diversi vantaggi:

- Le organizzazioni possono controllare l'accesso in modo centralizzato a tutti gli HMS gestiti nell'organizzazione.
- Se un utente lascia l'organizzazione, perde immediatamente l'accesso a tutti gli HMS gestiti nell'organizzazione.
- Le organizzazioni possono personalizzare l'autenticazione usando le opzioni Azure Active Directory, ad esempio per abilitare l'autenticazione a più fattori per una sicurezza aggiunta.

## <a name="resource-endpoints"></a>Endpoint delle risorse

Le entità di sicurezza accedono ai piani tramite endpoint. I controlli di accesso per i due piani funzionano in maniera indipendente. Per concedere a un'applicazione l'accesso per l'uso delle chiavi in un HSM gestito, si concede l'accesso al piano dati usando il controllo degli accessi in base al ruolo locale del servizio HSM gestito. Per concedere a un utente l'accesso alla risorsa HSM gestita per creare, leggere, eliminare, spostare i HSM gestiti e modificare altre proprietà e tag che si usano nel controllo degli accessi in base al ruolo di Azure.

La tabella seguente illustra gli endpoint per il piano dati e di gestione.

| Piano di&nbsp;accesso | Endpoint di accesso | Operazioni | Meccanismo di controllo di accesso |
| --- | --- | --- | --- |
| Piano di gestione | **Globale:**<br> management.azure.com:443<br> | Creare, leggere, aggiornare, eliminare e spostare i HMS gestiti<br>Impostare i tag del servizio HSM gestito | Controllo degli accessi in base al ruolo di Azure |
| Piano dati | **Globale:**<br> &lt;hsm-name &gt; .managedhsm.azure.net:443<br> | **Chiavi:** decrittografare, crittografare,<br> unwrap, wrap, verify, sign, get, list, update, create, import, delete, backup, restore, purge<br/><br/> Gestione dei ruoli del piano dati (controllo degli accessi in base al ruolo locale del modulo di protezione dell'HSM **gestito):** elencare le definizioni dei ruoli, assegnare ruoli, eliminare assegnazioni di _ruolo, <br/> <br/>_ definire ruoli personalizzati *Backup/ripristino: backup, ripristino, controllo dello stato delle operazioni di **backup/ripristino <br/> <br/>** Dominio di sicurezza**: scaricare e caricare il dominio di sicurezza | Controllo degli accessi in base al ruolo locale del servizio HSM gestito |
|||||

## <a name="management-plane-and-azure-rbac"></a>Piano di gestione e controllo degli accessi in base al ruolo di Azure

Nel piano di gestione si usa il controllo degli accessi in base al ruolo di Azure per autorizzare le operazioni che un chiamante può eseguire. Nel modello di Controllo degli accessi in base al ruolo di Azure ogni sottoscrizione di Azure ha un'istanza di Azure Active Directory. È possibile concedere l'accesso a utenti, gruppi e applicazioni da questa directory. Viene concesso l'accesso per gestire le risorse della sottoscrizione di Azure che usano il modello di distribuzione Azure Resource Manager. Per concedere l'accesso, usare il [portale di Azure](https://portal.azure.com/), l'[interfaccia della riga di comando di Azure](/cli/azure/install-classic-cli), [Azure PowerShell](/powershell/azureps-cmdlets-docs) o le [API REST di Azure Resource Manager](/rest/api/authorization/roleassignments).

È possibile creare un insieme di credenziali delle chiavi in un gruppo di risorse e gestire l'accesso usando Azure Active Directory. È possibile consentire a utenti o gruppi di gestire gli insiemi di credenziali delle chiavi in un gruppo di risorse. Per concedere l'accesso a un livello di ambito specifico, assegnare i ruoli di Azure appropriati. Per concedere l'accesso a un utente in modo che possa gestire insiemi di credenziali delle chiavi, assegnare all'utente un ruolo `key vault Contributor` predefinito in un ambito specifico. I livelli di ambito seguenti possono essere assegnati a un ruolo di Azure:

- **Gruppo di gestione:** un ruolo di Azure assegnato a livello di sottoscrizione si applica a tutte le sottoscrizioni in tale gruppo di gestione.
- **Sottoscrizione:** un ruolo di Azure assegnato a livello di sottoscrizione si applica a tutti i gruppi di risorse e le risorse all'interno di tale sottoscrizione.
- **Gruppo di risorse:** un ruolo di Azure assegnato a livello di gruppo di risorse si applica a tutte le risorse in tale gruppo di risorse.
- **Risorsa specifica:** un ruolo di Azure assegnato per una risorsa specifica si applica a tale risorsa. In questo caso, la risorsa è un insieme di credenziali delle chiavi specifico.

Ci sono diversi ruoli predefiniti. Se un ruolo predefinito non soddisfa le specifiche esigenze, è possibile definire un ruolo personalizzato. Per altre informazioni, vedere [Controllo degli accessi in base al ruolo di Azure: ruoli predefiniti.](../../role-based-access-control/built-in-roles.md)

## <a name="data-plane-and-managed-hsm-local-rbac"></a>Controllo degli accessi in base al ruolo locale del piano dati e del servizio HSM gestito

Si concede a un'entità di sicurezza l'accesso per eseguire operazioni chiave specifiche assegnando un ruolo. Per ogni assegnazione di ruolo è necessario specificare un ruolo e un ambito a cui si applica tale assegnazione. Per il controllo degli accessi in base al ruolo locale del servizio HSM gestito sono disponibili due ambiti.

- **"/" o "/keys":** ambito a livello di HSM. Le entità di sicurezza assegnate a un ruolo in questo ambito possono eseguire le operazioni definite nel ruolo per tutti gli oggetti (chiavi) nel modulo di protezione HSM gestito.
- **"/keys/ &lt; key-name &gt; "**: ambito a livello di chiave. Le entità di sicurezza assegnate a un ruolo in questo ambito possono eseguire le operazioni definite in questo ruolo solo per tutte le versioni della chiave specificata.

## <a name="next-steps"></a>Passaggi successivi

- Per un'esercitazione introduttiva per un amministratore, vedere [Che cos'è managed HSM?](overview.md)
- Per un'esercitazione sulla gestione dei ruoli, vedere Controllo degli accessi in base al [ruolo locale del servizio HSM gestito](role-management.md)
- Per altre informazioni sulla registrazione dell'utilizzo per la registrazione HSM gestita, vedere [Registrazione HSM gestita](logging.md)