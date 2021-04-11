---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/05/2021
ms.author: mimart
ms.openlocfilehash: 5a2382146cd8b85b8eef54b924a206dda7107b0f
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106382462"
---
#### <a name="app-registrations"></a>[Registrazioni per l'app](#tab/app-reg-ga/) 

1. In **Gestisci** selezionare **Autorizzazioni API**.
1. In **Autorizzazioni configurate** selezionare **Aggiungi un'autorizzazione**.
1. Selezionare la scheda **API Microsoft** , quindi selezionare **Microsoft Graph**.
1. Selezionare **Autorizzazioni applicazione**.
1. Espandere il gruppo di autorizzazioni appropriato e selezionare la casella di controllo relativa all'autorizzazione da concedere all'applicazione di gestione. Ad esempio:
    * **Utente**  >  di **User. ReadWrite. All**: per gli scenari di migrazione degli utenti o di gestione degli utenti.
    * **Gruppo**  >  di **Group. ReadWrite. All**: per la creazione di gruppi, la lettura e l'aggiornamento delle appartenenze ai gruppi e l'eliminazione di gruppi.
    * **Auditlog**  >  **Auditlog. Read. All**: per leggere i log di controllo della directory.
    * **Criteri**  >  di **Policy. ReadWrite. TrustFramework**: per gli scenari di integrazione continua/recapito continuo (ci/CD). Ad esempio, la distribuzione di criteri personalizzati con Azure Pipelines.
1. Selezionare **Aggiungi autorizzazioni**. Come indicato, attendere alcuni minuti prima di procedere con il passaggio successivo.
1. Selezionare **Concedi consenso amministratore per (nome del tenant)** .
1. Se non si è attualmente connessi con l'account amministratore globale, accedere con un account nel tenant di Azure AD B2C a cui è stato assegnato almeno il ruolo di *amministratore dell'applicazione cloud* , quindi selezionare **concedi il consenso dell'amministratore per (il nome del tenant)**.
1. Selezionare **Aggiorna**, quindi verificare che "concesso per..." viene visualizzato in **stato**. La propagazione delle autorizzazioni potrebbe richiedere alcuni minuti.

#### <a name="applications-legacy"></a>[Applicazioni (legacy)](#tab/applications-legacy/)

1. Nella pagina Panoramica dell' **app registrata** selezionare **Impostazioni**.
1. In **accesso all'API** selezionare **autorizzazioni necessarie**.
1. Selezionare **Microsoft Graph**.
1. In **Autorizzazioni applicazione** selezionare la casella di controllo relativa all'autorizzazione da concedere all'applicazione di gestione. Ad esempio:
    * **Leggi tutti i dati del registro di controllo**: selezionare questa autorizzazione per la lettura dei log di controllo della directory.
    * **Lettura e scrittura dei dati della directory**: selezionare questa autorizzazione per la migrazione degli utenti o gli scenari di gestione degli utenti.
    * **Leggere e scrivere i criteri del Framework di attendibilità dell'organizzazione**: selezionare questa autorizzazione per gli scenari di integrazione continua/recapito continuo (ci/CD). Ad esempio, la distribuzione di criteri personalizzati con Azure Pipelines.
1. Selezionare **Salva**.
1. Selezionare **Concedi autorizzazioni** e quindi selezionare **Sì**. Potrebbero essere necessari alcuni minuti per la propagazione completa delle autorizzazioni.
