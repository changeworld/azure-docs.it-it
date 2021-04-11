---
title: Aggiungere e gestire gli utenti per il Marketplace commerciale-Azure Marketplace
description: Informazioni su come gestire gli utenti nel programma Commercial Marketplace per un account Microsoft Commercial Marketplace nel centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: d5b9197bfd2526dd414406ebf1aca509d3b3fa91
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108283"
---
# <a name="add-and-manage-users-for-the-commercial-marketplace"></a>Aggiungere e gestire gli utenti per il Marketplace commerciale

**Ruoli appropriati**

- Proprietario
- Manager

La sezione **Utenti** del Centro per i partner (in **Impostazioni account**) consente di usare Azure AD per gestire gli utenti, i gruppi e le applicazioni di Azure AD che hanno accesso all'account del Centro per i partner. L'account deve avere autorizzazioni a livello di responsabile per l'[account aziendale (tenant di Azure AD)](company-work-accounts.md) in cui si vogliono aggiungere o modificare gli utenti. Per gestire gli utenti all'interno di un account aziendale/tenant diverso, è necessario disconnettersi e quindi accedere nuovamente come utente con autorizzazioni di **responsabile** per tale account aziendale/tenant.

Dopo aver eseguito l'accesso con l'account aziendale (Azure AD tenant), è possibile aggiungere e gestire gli utenti.

## <a name="add-existing-users"></a>Aggiungere utenti esistenti

Per aggiungere utenti all'account del Centro per i partner già esistenti nell'[account aziendale (tenant di Azure AD)](company-work-accounts.md) della società:

1. Passare a **Utenti** (in **Impostazioni account**) e selezionare **Aggiungi utenti**.
1. Selezionare uno o più utenti nell'elenco visualizzato. È possibile usare la casella di ricerca per cercare utenti specifici. *Se si seleziona più di un utente da aggiungere all'account del Centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più utenti con ruoli o autorizzazioni diverse, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
1. Al termine della scelta degli utenti, selezionare **Aggiungi selezionato**.
1. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per gli utenti selezionati.
1. Selezionare **Salva**.

## <a name="create-new-users"></a>Creare nuovi utenti

Per creare nuovi account utente, è necessario avere un account con autorizzazioni di [amministratore globale](/azure/active-directory/roles/permissions-reference).

1. Passare a **Utenti** (in **Impostazioni account**), selezionare **Aggiungi utenti**, quindi scegliere **Crea nuovi utenti**.
1. Immettere un nome, un cognome e un nome utente per ogni nuovo utente.
1. Per assegnare al nuovo utente un account amministratore globale nella directory dell'organizzazione, selezionare la casella di controllo **Impostare questo utente come amministratore globale nel tenant di Azure AD, con il controllo completo su tutte le risorse della directory**. In questo modo l'utente avrà accesso completo a tutte le funzionalità amministrative nell'istanza di Azure AD dell'azienda e potrà aggiungere e gestire gli utenti nell'account aziendale (tenant di Azure AD) dell'organizzazione, ma non nel Centro per i partner, a meno che non si conceda all'account il ruolo o le autorizzazioni appropriate.
1. Se si è selezionata la casella **Impostare questo utente come amministratore globale**, si dovrà specificare un *indirizzo e-mail per il ripristino della password* per consentire all'utente di recuperare la password, se necessario.
1. Nella sezione **Appartenenza a gruppo** selezionare i gruppi a cui dovrà appartenere il nuovo utente.
1. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'utente.
1. Selezionare **Salva**.

La creazione di un nuovo utente nel Centro per i partner creerà anche un account per tale utente nell'account aziendale (tenant di Azure AD) a cui è stato effettuato l'accesso. Se si apportano modifiche al nome di un utente nel Centro per i partner, le stesse modifiche vengono apportate all'account aziendale dell'organizzazione (tenant di Azure AD).

## <a name="invite-new-users-by-email"></a>Invitare nuovi utenti tramite posta elettronica

Per invitare tramite posta elettronica utenti che attualmente non fanno parte dell'account aziendale (tenant di Azure AD) della società, è necessario un account con autorizzazioni di [amministratore globale](/azure/active-directory/roles/permissions-reference).

1. Passare a **Utenti** (in **Impostazioni account**), selezionare **Aggiungi utenti**, quindi scegliere **Invitare gli utenti tramite posta elettronica**.
1. Immettere uno o più indirizzi di posta elettronica (fino a 10), separati da virgole o punti e virgola.
1. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'utente.
1. Selezionare **Salva**.

Gli utenti invitati riceveranno un invito tramite posta elettronica per aggiungere l'account del Centro per i partner. Verrà creato un nuovo account utente guest nell'account aziendale (tenant di Azure AD). Ogni utente dovrà accettare l'invito per poter accedere all'account.

Se è necessario inviare nuovamente un invito, visitare la pagina *Utenti*, trovare l'invito nell'elenco di utenti, selezionare l'indirizzo di posta elettronica (o il testo *Invito in sospeso*), quindi nella parte inferiore della pagina selezionare **Invia di nuovo l'invito**.

Se l'organizzazione usa l'[integrazione di directory](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573653(v=azure.100)) per sincronizzare il servizio directory locale con l'istanza di Azure AD, non sarà possibile creare nuovi utenti, gruppi o applicazioni Azure AD nel Centro per i partner. È necessario che l'utente (o un altro amministratore nella directory locale) li crei direttamente nella directory locale prima di poterli visualizzare e aggiungere nel Centro per i partner.

## <a name="remove-a-user"></a>Rimuovere un utente

Per rimuovere un utente dall'account aziendale (tenant di Azure AD), passare a **Utenti** (in **Impostazioni account**), selezionare l'utente che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** tra le azioni disponibili. Verrà visualizzata una finestra popup in cui confermare che si vogliono rimuovere gli utenti selezionati.

## <a name="change-a-user-password"></a>Modificare una password utente

Se uno degli utenti deve modificare la password, può farlo autonomamente se al momento della creazione dell'account si è specificato un *indirizzo e-mail per il ripristino della password*. È anche possibile aggiornare la password di un utente seguendo questa procedura. Per modificare la password di un utente nell'account aziendale della società (tenant di Azure AD), è necessario aver effettuato l'accesso a un account con autorizzazioni di [amministratore globale](/azure/active-directory/roles/permissions-reference). In questo modo verrà modificata la password dell'utente nel tenant di Azure AD, insieme alla password usata per accedere al centro per i partner.

1. Nella pagina **Utenti** (in **Impostazioni account**) selezionare il nome dell'account utente che si vuole modificare.
1. Selezionare il pulsante **Reimposta password** nella parte inferiore della pagina.
1. Verrà visualizzata una pagina di conferma per visualizzare le informazioni di accesso per l'utente, inclusa una password temporanea. Assicurarsi di stampare o copiare queste informazioni e di farle avere all'utente, perché non sarà possibile accedere alla password temporanea dopo aver lasciato questa pagina.
