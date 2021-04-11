---
title: Aggiungere e gestire applicazioni Azure AD-Azure Marketplace
description: Informazioni su come aggiungere e gestire Azure AD applicazioni per il programma Commercial Marketplace nel centro per i partner.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.openlocfilehash: be527647466ad76455585e16baabb26e39e42193
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108349"
---
# <a name="add-and-manage-azure-ad-applications"></a>Aggiungere e gestire applicazioni Azure AD

**Ruoli appropriati**

- Proprietario
- Manager

È possibile consentire alle applicazioni o ai servizi che fanno parte del Azure Active Directory dell'azienda (Azure AD) di accedere all'account del centro per i partner.

## <a name="add-existing-azure-ad-applications"></a>Aggiungere applicazioni Azure AD esistenti

Per aggiungere applicazioni già esistenti nell'istanza di Azure Active Directory dell'azienda:

1. Nella pagina **Utenti** (in **Impostazioni account**) selezionare **Aggiungi applicazioni Azure AD**.
1. Selezionare una o più applicazioni Azure AD dall'elenco visualizzato. È possibile usare la casella di ricerca per cercare applicazioni Azure AD specifiche. Se si seleziona più di un'applicazione Azure AD da aggiungere all'account del Centro per i partner, è necessario assegnare loro lo stesso ruolo o set di autorizzazioni personalizzate. Per aggiungere più applicazioni Azure AD con ruoli o autorizzazioni diversi, ripetere questi passaggi per ogni ruolo o set di autorizzazioni personalizzate.
1. Al termine della selezione Azure AD applicazioni, selezionare **Aggiungi selezionato**.
1. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per le applicazioni Azure AD selezionate.
1. Selezionare **Salva**.

## <a name="add-new-azure-ad-applications"></a>Aggiungere nuove applicazioni Azure AD

Per concedere all'account di una nuova applicazione Azure AD l'accesso al Centro per i partner, è possibile crearne uno nella sezione **Utenti**. Verrà creato un nuovo account nell'account aziendale dell'azienda (tenant Azure AD), non solo nell'account del centro per i partner. Se si usa questa applicazione Azure AD soprattutto per l'autenticazione del Centro per i partner e non è necessario che gli utenti vi accedano direttamente, è possibile immettere un indirizzo valido per **URL di risposta** e **URI ID app**, purché tali valori non vengano usati da altre applicazioni Azure AD nella directory.

1. Nella pagina **Utenti** (in **Impostazioni account**) selezionare **Aggiungi applicazioni Azure AD**.
1. Nella pagina successiva selezionare **Nuova applicazione Azure AD**.
1. Immettere l'**URL di risposta** per la nuova applicazione Azure AD. Si tratta dell'URL dove gli utenti possono accedere e usare l'applicazione Azure AD (noto anche come URL dell'app o URL di accesso). L'*URL di risposta* non può contenere più di 256 caratteri e deve essere univoco all'interno della directory.
1. Immettere l'**URI ID app** per la nuova applicazione Azure AD. Si tratta di un identificatore logico per l'applicazione Azure AD, presentato quando viene inviata ad Azure AD una richiesta di accesso Single Sign-On. L' *URI ID app* deve essere univoco per ogni Azure ad applicazione nella directory. Questo ID può contenere al massimo 256 caratteri. Per altre informazioni sull'URI ID app, vedere [Integrazione di applicazioni con Azure Active Directory](/azure/active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts).
1. Nella sezione **Ruoli** specificare i ruoli o le autorizzazioni personalizzate per l'applicazione Azure AD.
1. Selezionare **Salva**.

Dopo aver aggiunto o creato un'applicazione Azure AD, è possibile tornare alla sezione **Utenti** e selezionare il nome dell'applicazione per esaminare le impostazioni per l'applicazione, incluso l'ID tenant, l'ID client, l'URL di risposta e l'URI ID app.

## <a name="remove-an-azure-ad-application"></a>Rimuovere un'applicazione Azure AD

Per rimuovere un'applicazione dall'account aziendale (tenant di Azure AD), passare a **Utenti** (in **Impostazioni account**), selezionare l'applicazione che si vuole rimuovere usando la casella di controllo nella colonna all'estrema destra, quindi scegliere **Rimuovi** tra le azioni disponibili. Verrà visualizzata una finestra popup in cui confermare che si vogliono rimuovere le applicazioni selezionate.

## <a name="manage-keys-for-an-azure-ad-application"></a>Gestire le chiavi per un'applicazione Azure AD

Se l'applicazione Azure AD legge e scrive i dati in Microsoft Azure AD, sarà necessaria una chiave. È possibile creare chiavi per un'applicazione Azure AD modificandone le informazioni nel Centro per i partner. È anche possibile rimuovere le chiavi che non sono più necessarie.

1. Nella pagina **Utenti** (in **Impostazioni account**) selezionare il nome dell'applicazione Azure AD. Verranno visualizzate tutte le chiavi attive per l'applicazione Azure AD, inclusa la data in cui è stata creata la chiave e il momento in cui scadrà 50.
1. Per rimuovere una chiave non più necessaria, selezionare **Rimuovi**.
1. Per aggiungere una nuova chiave, selezionare **Aggiungi nuova chiave**.
1. Verrà visualizzata una schermata con i valori **ID client** e **Chiave**. Assicurarsi di stampare o copiare queste informazioni perché non sarà possibile accedervi di nuovo dopo aver lasciato questa pagina.
1. Per creare altre chiavi, selezionare **Aggiungi un'altra chiave**.
