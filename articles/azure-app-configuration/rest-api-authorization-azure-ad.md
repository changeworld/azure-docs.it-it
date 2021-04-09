---
title: API REST di configurazione app Azure-Azure Active Directory autorizzazione
description: Usare Azure Active Directory per l'autorizzazione per la configurazione app Azure tramite l'API REST
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: c028dcf6649da9abd196997d077b58386a5e6dd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092799"
---
# <a name="azure-active-directory-authorization---rest-api-reference"></a>Azure Active Directory autorizzazione-informazioni di riferimento sull'API REST

Quando si usa l'autenticazione Azure Active Directory (Azure AD), l'autorizzazione viene gestita dal controllo degli accessi in base al ruolo (RBAC). Il controllo degli accessi in base al ruolo richiede che gli utenti siano assegnati ai ruoli per concedere l'accesso alle risorse. Ogni ruolo contiene un set di azioni che gli utenti assegnati al ruolo sono in grado di eseguire.

## <a name="roles"></a>Ruoli

Per impostazione predefinita, i ruoli seguenti sono disponibili nelle sottoscrizioni di Azure:

- **Proprietario dei dati di configurazione app Azure**: questo ruolo fornisce l'accesso completo a tutte le operazioni.
- **Lettore dati di configurazione app Azure**: questo ruolo Abilita le operazioni di lettura.

## <a name="actions"></a>Azioni

I ruoli contengono un elenco di azioni che possono essere eseguite dagli utenti assegnati a tale ruolo. App Azure configurazione supporta le azioni seguenti:

- `Microsoft.AppConfiguration/configurationStores/keyValues/read`: Questa azione consente l'accesso in lettura alle risorse chiave-valore di configurazione dell'app, ad esempio/kV e/labels.
- `Microsoft.AppConfiguration/configurationStores/keyValues/write`: Questa azione consente l'accesso in scrittura alle risorse chiave-valore di configurazione dell'app.
- `Microsoft.AppConfiguration/configurationStores/keyValues/delete`: Questa azione consente di eliminare le risorse chiave-valore di configurazione dell'app. Si noti che l'eliminazione di una risorsa restituisce il valore chiave che è stato eliminato.

## <a name="error"></a>Errore

```http
HTTP/1.1 403 Forbidden
```

**Motivo:** L'entità che effettua la richiesta non ha le autorizzazioni necessarie per eseguire l'operazione richiesta.
**Soluzione:** Assegnare il ruolo necessario per eseguire l'operazione richiesta al principale che effettua la richiesta.

## <a name="managing-role-assignments"></a>Gestione delle assegnazioni di ruolo

È possibile gestire le assegnazioni di ruolo usando [le procedure RBAC di Azure](../role-based-access-control/overview.md) standard in tutti i servizi di Azure. È possibile eseguire questa operazione tramite l'interfaccia della riga di comando di Azure, PowerShell e il portale di Azure. Per altre informazioni, vedere [assegnare i ruoli di Azure usando il portale di Azure](../role-based-access-control/role-assignments-portal.md).