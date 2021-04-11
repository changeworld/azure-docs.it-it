---
title: Come creare un tenant di Azure Active Directory Developer gratuito
description: Questo articolo illustra come creare un account sviluppatore
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 1e8bb59c09622a39dad680940ff34e643ee0cc3b
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222742"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Come creare un tenant di Azure Active Directory Developer gratuito

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Durante l'anteprima è necessaria una licenza P2. 

Ci sono due semplici modi per creare un Azure Active Directory gratuito con una licenza di valutazione P2, in modo da poter installare il servizio emittente di credenziali verificabile ed è possibile testare la creazione e la convalida delle credenziali verificabili:

- [Partecipa](https://aka.ms/o365devprogram) al programma gratuito di Microsoft 365 Developer per ottenere una sandbox, strumenti e altre risorse gratuite come una Azure Active Directory con licenze P2. Utenti, gruppi, cassette postali e così via configurati.
- Creare un nuovo [tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) e attivare una [versione di valutazione gratuita](https://azure.microsoft.com/trial/get-started-active-directory/) di Azure ad Premium P1 o P2 nel nuovo tenant.

Se si decide di iscriversi al programma gratuito Microsoft 365 Developer, è necessario seguire alcuni semplici passaggi:

1. Fai clic sul pulsante **partecipa ora** sullo schermo.

2. Accedere con un nuovo account Microsoft o usare un account esistente (lavoro) già esistente.

3. Nella pagina di iscrizione selezionare l'area geografica, immettere il nome della società e accettare i termini e le condizioni del programma prima di fare clic su **Avanti**.

4. Fare clic su **Configura sottoscrizione**. Specificare l'area in cui si vuole creare il nuovo tenant, creare un nome utente, un dominio e immettere una password. Verrà creato un nuovo tenant e il primo amministratore del tenant.

5. Immettere le informazioni di sicurezza necessarie per proteggere l'account amministratore del nuovo tenant. Verrà impostata l'autenticazione a più fattori per l'account.


A questo punto, è stato creato un tenant con 25 licenze utente E5. Le licenze E5 includono Azure AD licenze P2. Facoltativamente, è possibile aggiungere pacchetti di dati di esempio a utenti, gruppi, posta e SharePoint per eseguire il test nell'ambiente di sviluppo. Per il servizio di emissione delle credenziali verificabili, non sono necessarie.

Per praticità, è possibile aggiungere il proprio account di lavoro come [Guest](https://docs.microsoft.com/azure/active-directory/b2b/b2b-quickstart-add-guest-users-portal.md) nel tenant appena creato e usare tale account per amministrare il tenant. Se si desidera che l'account Guest sia in grado di gestire il servizio credenziali verificabile, è necessario assegnare il ruolo "amministratore globale" a tale utente.

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di un account sviluppatore, è possibile provare la [prima esercitazione](get-started-verifiable-credentials.md) per altre informazioni sulle credenziali verificabili.