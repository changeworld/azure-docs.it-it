---
title: Creare & eliminare Azure AD B2C account utente consumer nel portale di Azure
description: Informazioni su come usare la portale di Azure per creare ed eliminare utenti consumer nella directory Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ace0ccb8372ff21a2d3e8721baf09bab539846c2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102033666"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>Usare il portale di Azure per creare ed eliminare utenti consumer in Azure AD B2C

Potrebbero essere presenti scenari in cui si desidera creare manualmente gli account utente nella directory Azure Active Directory B2C (Azure AD B2C). Anche se gli account del consumer in una directory Azure AD B2C vengono creati più di frequente quando gli utenti si iscrivono per usare una delle applicazioni, è possibile crearli a livello di codice e usando il portale di Azure. Questo articolo è incentrato sul metodo portale di Azure per la creazione e l'eliminazione di utenti.

Per aggiungere o eliminare utenti, all'account deve essere assegnato il ruolo di *amministratore utente* o *amministratore globale* .

## <a name="types-of-user-accounts"></a>Tipi di account utente

Come descritto in [Panoramica degli account utente in Azure ad B2C](user-overview.md), esistono tre tipi di account utente che è possibile creare in una directory Azure ad B2C:

* Lavoro
* Guest
* Consumer

Questo articolo è incentrato sull'uso degli **account utente** nel portale di Azure. Per informazioni sulla creazione e l'eliminazione di account di lavoro e Guest, vedere [aggiungere o eliminare utenti con Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Creazione di un utente consumer

1. Accedere al [portale di Azure](https://portal.azure.com).
1. Selezionare il filtro **Directory e sottoscrizione** nel menu in alto e quindi la directory contenente il tenant di Azure AD B2C.
1. Nel menu a sinistra selezionare **Azure AD B2C**. In alternativa, selezionare **Tutti i servizi** e quindi cercare e selezionare **Azure AD B2C**.
1. In **Gestisci** selezionare **Utenti**.
1. Selezionare **Nuovo utente**.
1. Selezionare **crea Azure ad B2C utente**.
1. Scegliere un **metodo di accesso** e immettere un indirizzo di **posta elettronica** o un **nome** utente per il nuovo utente. Il metodo di accesso selezionato qui deve corrispondere all'impostazione specificata per il provider di identità dell' *account locale* del tenant di Azure ad B2C (vedere **gestire** i  >  **provider di identità** nel tenant di Azure ad B2C).
1. Immettere un **nome** per l'utente. Si tratta in genere del nome completo (dato e cognome) dell'utente.
1. Opzionale È possibile **bloccare l'accesso** se si vuole ritardare la possibilità per l'utente di accedere. È possibile abilitare l'accesso in un secondo momento modificando il **profilo** dell'utente nel portale di Azure.
1. Scegliere **genera automaticamente password** o **Consenti la creazione della password**.
1. Specificare il **nome** e il **Cognome** dell'utente.
1. Selezionare **Crea**.

A meno che non sia stata selezionata l'opzione **Blocca accesso**, l'utente può ora accedere usando il metodo di accesso (posta elettronica o nome utente) specificato.

## <a name="reset-a-users-password"></a>Reimpostare la password di un utente

In qualità di amministratore, è possibile reimpostare la password di un utente, se l'utente dimentica la password. Quando si reimposta la password dell'utente, viene generata automaticamente una password temporanea per l'utente. La password temporanea non scade mai. All'accesso successivo dell'utente, la password funzionerà indipendentemente dal tempo trascorso da quando è stata generata. Quindi, l'utente deve reimpostare la password su uno permanente. 

> [!IMPORTANT]
> Prima di reimpostare la password di un utente, [configurare un flusso di reimpostazione della password forzata in Azure Active Directory B2C](force-password-reset.md). in caso contrario, l'utente non sarà in grado di eseguire l'accesso.

Per reimpostare la password di un utente:

1. Nella directory Azure AD B2C selezionare **utenti** e quindi selezionare l'utente per il quale si desidera reimpostare la password.
1. Cercare e selezionare l'utente che richiede la reimpostazione, quindi selezionare **Reimposta password**.

    Viene visualizzata la pagina **Alain Charon - Profilo** con l'opzione **Reimposta password**.

    ![Pagina del profilo dell'utente, con l'opzione Reimposta password evidenziata](media/manage-users-portal/user-profile-reset-password-link.png)

1. Nella pagina **Reimposta password** selezionare **Reimposta password**.
1. Copiare la password e assegnarla all'utente. L'utente dovrà modificare la password durante il processo di accesso successivo.


## <a name="delete-a-consumer-user"></a>Eliminare un utente consumer

1. Nella directory di Azure AD B2C selezionare **utenti** e quindi selezionare l'utente che si desidera eliminare.
1. Selezionare **Elimina** e quindi **Sì** per confermare l'eliminazione.

Per informazioni dettagliate sul ripristino di un utente entro i primi 30 giorni dopo l'eliminazione o per l'eliminazione definitiva di un utente, vedere [ripristinare o rimuovere un utente eliminato di recente con Azure Active Directory](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Passaggi successivi

Per gli scenari di gestione automatizzata degli utenti, ad esempio la migrazione degli utenti da un altro provider di identità alla directory Azure AD B2C, vedere [Azure ad B2C: migrazione degli](user-migration.md)utenti.
