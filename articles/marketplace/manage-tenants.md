---
title: Gestire i tenant nel Marketplace commerciale-Azure Marketplace
description: Informazioni su come gestire i tenant per il programma Commercial Marketplace nel centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 446792b26527126a4db99da14a2585c17cf8610c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108328"
---
# <a name="manage-tenants-in-the-commercial-marketplace"></a>Gestire i tenant nel Marketplace commerciale

**Ruoli appropriati**

- Manager

Un tenant di Azure Active Directory (AD), detto anche *account* aziendale in questa documentazione, è una rappresentazione dell'organizzazione configurata nell'portale di Azure e consente di gestire un'istanza specifica dei servizi cloud Microsoft per gli utenti interni ed esterni. Se l'organizzazione ha sottoscritto un servizio cloud Microsoft, ad esempio Azure, Microsoft Intune o Microsoft 365, è stato stabilito un tenant Azure AD.

È possibile configurare più tenant da usare con il Centro per i partner. Questa operazione può essere eseguita se la società ha più tenant (ad esempio, contoso.com, contoso.uk e così via). è possibile collegare qui i tenant aggiuntivi. Questa associazione consente di aggiungere e gestire gli utenti dai tenant aggiuntivi nell'account del Marketplace commerciale.

Tutti gli utenti con il ruolo Responsabile nell'account del Centro per i partner potranno aggiungere e rimuovere tenant di Azure AD dall'account.

## <a name="add-an-existing-tenant"></a>Aggiungere un tenant esistente

Per associare un altro tenant di Azure AD all'account del Centro per i partner:

1. Nella parte superiore destra del centro per i partner selezionare **Impostazioni**  >  **Impostazioni account**.
1. In **profilo organizzazione** selezionare **tenant**. Vengono visualizzate le associazioni tenant correnti.
1. Nella scheda **Developer** selezionare **associa**.
1. Immettere le credenziali di Azure AD per il tenant che si vuole associare.
1. Controllare l'organizzazione e il nome di dominio per il tenant di Azure AD. Per completare l'associazione, selezionare **Conferma**.

Se l'associazione ha esito positivo, sarà possibile aggiungere e gestire gli utenti degli account nella sezione Users del Centro per i partner. Per informazioni sull'aggiunta di utenti, vedere [gestire gli utenti](add-manage-users.md).

## <a name="create-a-new-tenant"></a>Creare un nuovo tenant

Per creare un altro tenant di Azure AD con l'account del Centro per i partner:

1. Nella parte superiore destra del centro per i partner selezionare **Impostazioni**  >  **Impostazioni account**.
1. In **profilo organizzazione** selezionare **tenant**. Vengono visualizzate le associazioni tenant correnti.
1. Nella scheda Developer (Sviluppatore) selezionare **Create (crea**).
1. Immettere le informazioni sulla directory per la nuova istanza di Azure AD:
    - **Nome di dominio**: nome univoco che verrà usato per il dominio di Azure AD, insieme a ".onmicrosoft.com". Se ad esempio è stato immesso "example", il dominio di Azure AD sarà "example.onmicrosoft.com".
    - **Indirizzo di posta elettronica del contatto**: indirizzo di posta elettronica a cui è possibile essere contattati in merito all'account, se necessario.
    - **Informazioni sull'account utente dell'amministratore globale**: nome, cognome, nome utente e password da usare per il nuovo account amministratore globale.
1. Selezionare Crea per confermare il nuovo dominio e le informazioni sull'account.
1. Accedere con il nuovo nome utente e la nuova password di amministratore globale di Azure AD per iniziare ad [aggiungere e gestire gli utenti](add-manage-users.md).

Per ulteriori informazioni sulla creazione di nuovi tenant all'interno del portale di Azure, anziché sul portale del centro per i partner, vedere l'articolo [creare un nuovo tenant in Azure Active Directory](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

## <a name="remove-a-tenant"></a>Rimuovere un tenant

Per rimuovere un tenant dall'account del Centro per i partner, trovarne il nome nella pagina **Tenant** (in **Impostazioni account**), quindi selezionare **Rimuovi**. Verrà richiesto di confermare che si vuole rimuovere il tenant. Al termine dell'operazione, nessun utente del tenant potrà accedere all'account del centro per i partner e tutte le autorizzazioni configurate per tali utenti verranno rimosse.

> [!TIP]
> Non è possibile rimuovere un tenant se si è attualmente connessi al Centro per i partner con un account nello stesso tenant. Per rimuovere un tenant, è necessario accedere al centro per i partner come **responsabile** di un altro tenant associato all'account. Se è presente un solo tenant associato all'account, il tenant può essere rimosso solo dopo aver eseguito l'accesso con l'account Microsoft che ha aperto l'account.
