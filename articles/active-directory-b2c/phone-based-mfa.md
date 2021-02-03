---
title: Protezione dell'autenticazione a più fattori basata sul telefono in Azure AD B2C
titleSuffix: Azure AD B2C
description: Informazioni sui suggerimenti per la protezione dell'autenticazione a più fattori basata su telefono nel tenant di Azure AD B2C usando monitoraggio di Azure Log Analytics report e avvisi. Usare la cartella di lavoro per identificare le autenticazioni telefoniche fraudolente e attenuare le iscrizioni fraudolente. =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 3ca73e020009817001f309ddf29c2984a8541026
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527480"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>Protezione dell'autenticazione a più fattori basata su telefono

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Con Azure Active Directory (Azure AD) Multi-Factor Authentication (multi-factor authentication), gli utenti possono scegliere di ricevere una chiamata vocale automatizzata al numero di telefono che registrano per la verifica. Gli utenti malintenzionati possono trarre vantaggio da questo metodo creando più account e inserendo telefonate senza completare il processo di registrazione dell'autenticazione a più fattori. Queste numerose iscrizioni non riuscite potrebbero esaurire i tentativi di iscrizione consentiti, impedendo ad altri utenti di iscriversi a nuovi account nel tenant Azure AD B2C. Per garantire la protezione da questi attacchi, è possibile usare monitoraggio di Azure per monitorare gli errori di autenticazione del telefono e attenuare le iscrizioni fraudolente.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, creare un' [area di lavoro log Analytics](azure-monitor.md).

## <a name="create-a-phone-based-mfa-events-workbook"></a>Creazione di una cartella di lavoro di eventi con autenticazione a più fattori

Il repository [Azure ad B2C report & Alerts](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) in github contiene elementi che è possibile usare per creare e pubblicare report, avvisi e dashboard in base ai log di Azure ad B2C. La cartella di lavoro bozza illustrata di seguito evidenzia gli errori correlati al telefono.

### <a name="overview-tab"></a>Scheda Panoramica

Le informazioni seguenti sono visualizzate nella scheda **Panoramica** :

- Motivi dell'errore (numero totale di autenticazioni del telefono non riuscite per ogni motivo)
- Bloccato a causa di una reputazione non valida
- Indirizzo IP con autenticazione telefono non riuscita (numero totale di autenticazioni telefoniche non riuscite per ogni indirizzo IP specificato)
- Numeri di telefono con indirizzo IP-autenticazioni del telefono non riuscite
- Browser (errori di autenticazione telefono per browser client)
- Sistema operativo (errori di autenticazione telefono per sistema operativo client)

![Scheda Panoramica](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>Scheda Dettagli

Le informazioni seguenti sono segnalate nella scheda **Dettagli** :

- Criteri di Azure AD B2C-autenticazioni del telefono non riuscite
- Errori di autenticazione telefono per numero di telefono-grafico temporale (sequenza temporale regolabile)
- Errori di autenticazione tramite telefono per criterio di Azure AD B2C-grafico temporale (sequenza temporale regolabile)
- Errori di autenticazione telefono per indirizzo IP-grafico temporale (sequenza temporale regolabile)
- Selezionare il numero di telefono per visualizzare i dettagli dell'errore (selezionare un numero di telefono per un elenco dettagliato degli errori)

![Scheda Dettagli 1 di 3](media/phone-based-mfa/details-tab-1.png)

![Scheda Dettagli 2 di 3](media/phone-based-mfa/details-tab-2.png)

![Scheda dettagli 3 di 3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>Usare la cartella di lavoro per identificare le iscrizioni fraudolente

È possibile usare la cartella di lavoro per comprendere gli eventi di autenticazione a più fattori basati sul telefono e identificare l'uso potenzialmente dannoso del servizio di telefonia.

1. Informazioni sulle normali esigenze del tenant rispondendo a queste domande:

   - Dove sono le aree da cui si prevede l'autenticazione a più fattori basata su telefono?
   - Esaminare i motivi illustrati per i tentativi di autenticazione a più fattori basati su telefono non riusciti; sono considerati normali o previsti?

2. Riconoscere le caratteristiche dell'iscrizione fraudolenta:

   - **Basato sulla posizione**: esaminare gli **errori di autenticazione telefono per indirizzo IP** per qualsiasi account associato a percorsi da cui non si prevede che gli utenti si iscrivono.

   > [!NOTE]
   > L'indirizzo IP specificato è un'area approssimativa.

   - **Basato sulla velocità**: esaminare le **autenticazioni del telefono non riuscite (al giorno)**, che indicano i numeri di telefono che stanno effettuando un numero anomalo di tentativi di autenticazione telefono non riusciti al giorno, ordinati dal più alto (a sinistra) al più basso (destro).

3. Attenuare le iscrizioni fraudolente attenendosi alla procedura descritta nella sezione successiva.
 

## <a name="mitigate-fraudulent-sign-ups"></a>Attenuazione delle iscrizioni fraudolente

Eseguire le azioni seguenti per attenuare le iscrizioni fraudolente.

- Usare le versioni **consigliate** dei flussi utente per eseguire le operazioni seguenti:
     
   - Abilitare la funzionalità di accesso monouso per [la posta elettronica (OTP) per l'autenticazione a più fattori](phone-authentication-user-flows.md) (si applica ai flussi di iscrizione e accesso).
   - [Configurare un criterio di accesso condizionale](conditional-access-identity-protection-setup.md) per bloccare gli accessi in base alla posizione (si applica solo ai flussi di accesso e non ai flussi di iscrizione).
   - Usare i connettori API per [l'integrazione con una soluzione anti-bot come reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (si applica ai flussi di iscrizione).

- Rimuovere i codici paese che non sono rilevanti per l'organizzazione dal menu a discesa in cui l'utente verifica il numero di telefono. questa modifica verrà applicata alle iscrizioni future:
    
   1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale del tenant di Azure AD B2C.

   2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C. A tale scopo, fare clic sul filtro **Directory e sottoscrizione** nel menu in alto e scegliere la directory che contiene il tenant.

   3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.

   4. Selezionare il flusso utente e quindi selezionare **lingue**. Selezionare la lingua per la posizione geografica dell'organizzazione per aprire il pannello dei dettagli della lingua. (Per questo esempio, si selezionerà la **lingua inglese** per il Stati Uniti). Selezionare la **pagina autenticazione** a più fattori e quindi selezionare **Scarica impostazioni predefinite (en)**.
 
      ![Carica le nuove sostituzioni per scaricare le impostazioni predefinite](media/phone-based-mfa/download-defaults.png)

   5. Aprire il file JSON che è stato scaricato nel passaggio precedente. Nel file cercare `DEFAULT` e sostituire la riga con `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"` . Assicurarsi di impostare `Overrides` su `true` .

   > [!NOTE]
   > È possibile personalizzare l'elenco di codici paese consentiti nell' `countryList` elemento (vedere l' [esempio della pagina di autenticazione del fattore telefonico](localization-string-ids.md#phone-factor-authentication-page-example)).

   7. Salvare il file JSON. Nel pannello dei dettagli della lingua, in **carica nuove sostituzioni**, selezionare il file JSON modificato per caricarlo.

   8. Chiudere il pannello e selezionare **Esegui flusso utente**. Per questo esempio, verificare che **Stati Uniti** sia l'unico codice paese disponibile nell'elenco a discesa:
 
      ![Elenco a discesa del codice paese](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su [Identity Protection e accesso condizionale per Azure ad B2C](conditional-access-identity-protection-overview.md) 

- Applicare [l'accesso condizionale ai flussi utente in Azure Active Directory B2C](conditional-access-user-flow.md)