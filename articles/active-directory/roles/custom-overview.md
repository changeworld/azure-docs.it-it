---
title: Panoramica del controllo degli accessi in base al ruolo di Azure Active Directory
description: Informazioni sull'assegnazione di ruoli e l'ambito con restrizioni in Azure Active Directory.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3fad2c683890776908afbfbf15ee91d46d564783
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/15/2021
ms.locfileid: "103466763"
---
# <a name="overview-of-role-based-access-control-in-azure-active-directory"></a>Panoramica del controllo degli accessi in base al ruolo di Azure Active Directory

Questo articolo descrive il controllo degli accessi in base al ruolo di Azure Active Directory (Azure AD). I ruoli di Azure AD consentono di concedere autorizzazioni granulari agli amministratori, in base al principio dei privilegi minimi. I ruoli predefiniti e personalizzati di Azure AD si basano su concetti simili a quelli del [sistema di controllo degli accessi in base per le risorse di Azure](../../role-based-access-control/overview.md) (ruoli di Azure). La [differenza tra questi due sistemi di controllo degli accessi in base al ruolo](../../role-based-access-control/rbac-and-directory-admin-roles.md) è la seguente:

- I ruoli di Azure AD controllano l'accesso a risorse di Azure AD come utenti, gruppi e applicazioni tramite l'API Graph
- I ruoli di Azure controllano l'accesso a risorse di Azure come macchine virtuali o archiviazione tramite Gestione risorse di Azure

Entrambi i sistemi contengono definizioni e assegnazioni di ruoli usate in modo simile. Tuttavia, le autorizzazioni dei ruoli di Azure AD non possono essere usate nei ruoli personalizzati di Azure e viceversa.

## <a name="understand-azure-ad-role-based-access-control"></a>Informazioni sul controllo degli accessi in base al ruolo di Azure AD
Azure AD supporta due tipi di definizioni di ruoli: 
* [Ruoli predefiniti](./permissions-reference.md)
* [Ruoli personalizzati](./custom-create.md)

I ruoli predefiniti sono ruoli pronti per l'uso con un set fisso di autorizzazioni. Queste definizioni dei ruoli non possono essere modificate. Azure AD supporta attualmente numerosi [ruoli predefiniti](./permissions-reference.md) e l'elenco è in continuo aumento. Per soddisfare requisiti più avanzati, Azure AD supporta anche [ruoli personalizzati](./custom-create.md). La concessione di autorizzazioni tramite ruoli di Azure AD personalizzati è un processo in due passaggi che prevede la creazione di una definizione del ruolo personalizzata e la successiva assegnazione tramite un'assegnazione di ruolo. Una definizione del ruolo personalizzata è una raccolta di autorizzazioni aggiunte da un elenco preimpostato. Queste autorizzazioni corrispondono a quelle usate nei ruoli predefiniti.  

Dopo aver creato la definizione del ruolo personalizzato (o usando un ruolo predefinito), è possibile assegnarla a un utente creando un'assegnazione di ruolo. Un'assegnazione di ruolo concede all'utente le autorizzazioni in una definizione del ruolo in un ambito specificato. Questo processo in due passaggi consente di creare una singola definizione del ruolo e di assegnarla più volte in ambiti diversi. Un ambito definisce il set di risorse di Azure AD a cui il membro del ruolo può accedere. L'ambito più comune è l'ambito a livello di organizzazione. Un ruolo personalizzato può essere assegnato a livello di organizzazione, per concedere così al membro del ruolo le autorizzazioni del ruolo per tutte le risorse nell'organizzazione. È anche possibile assegnare un ruolo personalizzato a un ambito degli oggetti. Un ambito degli oggetti è ad esempio una singola applicazione. Lo stesso ruolo può essere assegnato a un utente per tutte le applicazioni dell'organizzazione e quindi a un altro utente con un ambito limitato all'app Contoso Expense Reports.  

I ruoli predefiniti e personalizzati di Azure AD operano in base a concetti simili al [Controllo degli accessi in base al ruolo di Azure](../develop/access-tokens.md#payload-claims). La [differenza tra questi due sistemi di controllo degli accessi in base al ruolo](../../role-based-access-control/rbac-and-directory-admin-roles.md) sta nel fatto che il controllo degli accessi in base al ruolo di Azure controlla l'accesso alle risorse di Azure, ad esempio le macchine virtuali o lo spazio di archiviazione, tramite Gestione delle risorse di Azure e i ruoli personalizzati di Azure AD controllano l'accesso alle risorse di Azure AD tramite l'API Graph. Entrambi i sistemi sfruttano il concetto di definizioni del ruolo e assegnazioni di ruolo. Le autorizzazioni del controllo degli accessi in base al ruolo di Azure AD non possono essere incluse nei ruoli di Azure e viceversa.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>In che modo Azure AD determina se un utente può accedere a una risorsa

Di seguito è indicata la procedura generale usata da Azure AD per determinare se l'utente è autorizzato ad accedere a una risorsa di gestione. Usare queste informazioni per risolvere i problemi di accesso.

1. Un utente (o un'entità servizio) acquisisce un token per l'endpoint Microsoft Graph o Azure AD Graph.
1. L'utente effettua una chiamata API ad Azure Active Directory (Azure AD) tramite Microsoft Graph o Azure AD Graph usando il token rilasciato.
1. A seconda delle circostanze, Azure AD esegue una delle operazioni seguenti:
   - Valuta le appartenenze ai ruoli dell'utente in base all'[attestazione wids](../../active-directory-b2c/access-tokens.md) nel token di accesso dell'utente.
   - Recupera tutte le assegnazioni di ruolo valide per l'utente, direttamente o tramite l'appartenenza a gruppi, per la risorsa in cui viene eseguita l'azione.
1. Azure AD determina se l'azione nella chiamata API è inclusa nei ruoli di cui l'utente dispone per questa risorsa.
1. Se l'utente non ha un ruolo con l'azione nell'ambito richiesto, l'accesso non è consentito. In caso contrario, l'accesso viene concesso.

## <a name="role-assignment"></a>Assegnazione di ruolo

Un'assegnazione di ruolo è una risorsa di Azure AD che collega una *definizione di ruolo* a un *utente* in un *ambito* specifico per concedere l'accesso alle risorse di Azure AD. L'accesso viene concesso mediante la creazione di un'assegnazione di ruolo e viene revocato rimuovendo un'assegnazione di ruolo. Un'assegnazione di ruolo è costituita da tre elementi principali:

- Utente di Azure AD
- Definizione di ruolo
- Ambito risorsa

È possibile [creare assegnazioni di ruolo](custom-create.md) usando il portale di Azure, l'interfaccia della riga di comando di Azure, Azure AD PowerShell o l'API Graph. È anche possibile [elencare le assegnazioni di ruolo](view-assignments.md).

Lo schema seguente mostra un esempio di assegnazione di ruolo. In questo esempio a Chris Green è stato assegnato il ruolo personalizzato di amministratore registrazioni app nell'ambito della registrazione app Contoso Widget Builder. L'assegnazione concede a Chris le autorizzazioni del ruolo di amministratore registrazioni app solo per la registrazione di questa app specifica.

![L'assegnazione di ruolo è il modo in cui vengono imposte le autorizzazioni e include tre parti](./media/custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Entità di sicurezza

Un'entità di sicurezza rappresenta l'utente a cui deve essere assegnato l'accesso alle risorse di Azure AD. Un utente è un soggetto che dispone di un profilo utente in Azure Active Directory.

### <a name="role"></a>Ruolo

Una definizione del ruolo o ruolo è una raccolta di autorizzazioni ed elenca le operazioni che è possibile eseguire sulle risorse di Azure AD, ad esempio creazione, lettura, aggiornamento ed eliminazione. Sono disponibili due tipi di ruoli in Azure AD:

- Ruoli predefiniti creati da Microsoft che non possono essere modificati.
- Ruoli personalizzati creati e gestiti dall'organizzazione.

### <a name="scope"></a>Scope

Un ambito è la restrizione delle azioni consentite a una risorsa di Azure AD specifica come parte di un'assegnazione di ruolo. Quando si assegna un ruolo, è possibile specificare un ambito che limita l'accesso dell'amministratore a una risorsa specifica. Se ad esempio si desidera concedere a uno sviluppatore un ruolo personalizzato, ma solo per gestire una registrazione dell'applicazione specifica, è possibile includere la registrazione dell'applicazione specifica come ambito nell'assegnazione di ruolo.

## <a name="required-license-plan"></a>Piano di licenza necessario

L'uso dei ruoli predefiniti in Azure AD è gratuito, mentre per usare i ruoli personalizzati è necessaria una licenza di Azure AD Premium P1. Per trovare la licenza corretta per le proprie esigenze, vedere [Caratteristiche e funzionalità di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui ruoli di Azure AD](concept-understand-roles.md)
- Creare assegnazioni di ruolo personalizzate usando il [portale di Azure, Azure AD PowerShell e l'API Graph](custom-create.md)
- [Elencare le assegnazioni di ruolo](view-assignments.md)