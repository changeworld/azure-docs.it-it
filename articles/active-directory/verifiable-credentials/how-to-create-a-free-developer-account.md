---
title: Come creare un tenant per sviluppatori Azure Active Directory gratuito
description: Questo articolo illustra come creare un account sviluppatore
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a50c8b083c1cd453dbe3c51c63ec9cf53859c3bd
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587266"
---
# <a name="how-to-create-a-free-azure-active-directory-developer-tenant"></a>Come creare un tenant per sviluppatori Azure Active Directory gratuito

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> In anteprima è necessaria una licenza P2. 

Esistono due semplici modi per creare un Azure Active Directory gratuito con una licenza di valutazione P2 in modo da poter installare il servizio Autorità di emittente delle credenziali verificabili ed è possibile testare la creazione e la convalida di credenziali verificabili:

- [Partecipa](https://aka.ms/o365devprogram) al programma Microsoft 365 Developer Program gratuito e ottieni una sandbox gratuita, strumenti e altre risorse come un Azure Active Directory con licenze P2. Utenti, gruppi, cassette postali e così via configurati.
- Creare un nuovo [tenant](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) e attivare una [versione di](https://azure.microsoft.com/trial/get-started-active-directory/) valutazione gratuita Azure AD Premium P1 o P2 nel nuovo tenant.

Se si decide di iscriversi al programma di sviluppo Microsoft 365 gratuito, è necessario seguire alcuni semplici passaggi:

1. Fare clic sul **pulsante Partecipa** ora nella schermata.

2. Accedere con un nuovo account Microsoft o usare un account esistente (aziendale) già esistente.

3. Nella pagina di iscrizione selezionare l'area, immettere il nome della società e accettare i termini e le condizioni del programma prima di fare clic su **Avanti.**

4. Fare clic **su Configura sottoscrizione**. Specificare l'area in cui creare il nuovo tenant, creare un nome utente, un dominio e immettere una password. Verrà creato un nuovo tenant e il primo amministratore del tenant.

5. Immettere le informazioni di sicurezza necessarie per proteggere l'account amministratore del nuovo tenant. Verrà impostata l'autenticazione MFA per l'account.


A questo punto è stato creato un tenant con 25 licenze utente E5. Le licenze E5 includono Azure AD P2. Facoltativamente, è possibile aggiungere pacchetti di dati di esempio con utenti, gruppi, posta elettronica e SharePoint per eseguire test nell'ambiente di sviluppo. Per il servizio di emissione delle credenziali verificabili, non sono necessarie.

Per praticità, è possibile aggiungere il proprio account aziendale come [guest](/azure/active-directory/external-identities/b2b-quickstart-add-guest-users-portal) nel tenant appena creato e usarlo per amministrare il tenant. Se si vuole che l'account guest sia in grado di gestire il servizio credenziali verificabili, è necessario assegnare il ruolo "Amministratore globale" a tale utente.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un account sviluppatore, è possibile provare la [prima esercitazione](get-started-verifiable-credentials.md) per ottenere altre informazioni sulle credenziali verificabili.
