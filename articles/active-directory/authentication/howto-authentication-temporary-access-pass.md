---
title: Configurare un passaggio di accesso temporaneo in Azure AD per registrare metodi di autenticazione con password
description: Informazioni su come configurare e consentire agli utenti di registrare i metodi di autenticazione con password usando un passaggio di accesso temporaneo (TAP)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: justinha
author: inbarckms
manager: daveba
ms.reviewer: inbarckms
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56d45119fa86ab47e6a625c628d8cb9763db83bd
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520993"
---
# <a name="configure-temporary-access-pass-in-azure-ad-to-register-passwordless-authentication-methods-preview"></a>Configurare il passaggio di accesso temporaneo in Azure AD per registrare i metodi di autenticazione con password (anteprima)

I metodi di autenticazione senza password, ad esempio FIDO2 e l'accesso tramite telefono senza password tramite l'app Microsoft Authenticator, consentono agli utenti di accedere in modo sicuro senza una password. Gli utenti possono bootstrap i metodi con password in uno dei due modi seguenti:

- Uso di metodi di autenticazione a più fattori Azure AD esistenti 
- Uso di un passaggio di accesso temporaneo (TAP) 

TAP è un codice di accesso limitato al tempo emesso da un amministratore che soddisfa i requisiti di autenticazione avanzata e può essere usato per l'onboarding di altri metodi di autenticazione, inclusi quelli con password. TAP semplifica inoltre il ripristino quando un utente perde o dimentica il fattore di autenticazione forte, ad esempio una chiave di sicurezza FIDO2 o un'app Microsoft Authenticator, ma deve eseguire l'accesso per registrare nuovi metodi di autenticazione avanzata.


Questo articolo illustra come abilitare e usare un tocco in Azure AD usando il portale di Azure. È anche possibile eseguire queste azioni usando le API REST. 

>[!NOTE]
>Il passaggio di accesso temporaneo è attualmente disponibile in anteprima pubblica. Alcune funzionalità potrebbero non essere supportate o avere funzionalità limitate. 

## <a name="enable-the-tap-policy"></a>Abilitare il criterio TAP

Un criterio TAP definisce le impostazioni, ad esempio la durata dei passaggi creati nel tenant o gli utenti e i gruppi che possono usare un tocco per l'accesso. Prima che chiunque possa accedere con un tocco, è necessario abilitare i criteri del metodo di autenticazione e scegliere quali utenti e gruppi possono accedere usando un tocco.
Sebbene sia possibile creare un tocco per qualsiasi utente, solo quelli inclusi nei criteri possono accedervi.

L'amministratore globale e i titolari del ruolo amministratore dei criteri del metodo di autenticazione possono aggiornare i criteri del metodo TAP Authentication.
Per configurare i criteri del metodo TAP Authentication:

1. Accedere al portale di Azure come amministratore globale e fare clic su **Azure Active Directory**  >  metodi di autenticazione di **sicurezza**  >    >  **Temporary Access Pass**.
1. Fare clic su **Sì** per abilitare il criterio, selezionare gli utenti a cui sono applicati i criteri e tutte le impostazioni **generali** .

   ![Screenshot su come abilitare i criteri del metodo di autenticazione TAP](./media/how-to-authentication-temporary-access-pass/policy.png)

   Il valore predefinito e l'intervallo di valori consentiti sono descritti nella tabella seguente.


   | Impostazione          | Valori predefiniti | Valori consentiti               | Commenti                                                                                                                                                                                                                                                                 |   |
   |------------------|----------------|------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    Durata minima | 1 ora         | 10 – 43200 minuti (30 giorni) | Numero minimo di minuti per cui il tocco è valido.                                                                                                                                                                                                                         |   |
   | Durata massima | 24 ore       | 10 – 43200 minuti (30 giorni) | Numero massimo di minuti per cui il tocco è valido.                                                                                                                                                                                                                         |   |
   | Durata predefinita | 1 ora         | 10 – 43200 minuti (30 giorni) | I valori predefiniti possono essere sostituiti dai singoli passaggi, entro la durata minima e massima configurata dal criterio                                                                                                                                                |   |
   | Uso monouso     | Falso          | True/false                 | Quando il criterio è impostato su false, i passaggi nel tenant possono essere utilizzati una o più volte durante la relativa validità (durata massima). Applicando monouso nei criteri TAP, tutti i passaggi creati nel tenant verranno creati come monouso. |   |
   | Length           | 8              | 8-48 caratteri              | Definisce la lunghezza del codice di accesso.                                                                                                                                                                                                                                      |   |

## <a name="create-a-tap-in-the-azure-ad-portal"></a>Creare un tocco nel portale Azure AD

Dopo aver abilitato un criterio TAP, è possibile creare un tocco per un utente in Azure AD. Questi ruoli possono eseguire le azioni seguenti relative al tocco.

- L'amministratore globale può creare, eliminare e visualizzare il tocco su qualsiasi utente (eccetto se stesso)
- Gli amministratori di autenticazione con privilegi possono creare, eliminare, visualizzare il tocco su amministratori e membri (eccetto se stessi)
- Gli amministratori dell'autenticazione possono creare, eliminare e toccare i membri (eccetto se stessi)
- L'amministratore globale può visualizzare i dettagli del tocco sull'utente (senza leggere il codice).

Per creare un tocco:

1. Accedere al portale come amministratore globale, amministratore di autenticazione con privilegi o amministratore di autenticazione. 
1. Fare clic su **Azure Active Directory**, passare a utenti, selezionare un utente, ad esempio *Chris Green*, quindi scegliere **metodi di autenticazione**.
1. Se necessario, selezionare l'opzione per **provare la nuova esperienza dei metodi di autenticazione utente**.
1. Selezionare l'opzione per **aggiungere metodi di autenticazione**.
1. Sotto **Scegli metodo** fare clic su **pass di accesso temporaneo (anteprima)**.
1. Definire una durata o un'ora di attivazione personalizzata e fare clic su **Aggiungi**.

   ![Screenshot della creazione di un tocco](./media/how-to-authentication-temporary-access-pass/create.png)

1. Una volta aggiunti, vengono visualizzati i dettagli del tocco. Prendere nota del valore TAP effettivo. Questo valore viene fornito all'utente. Non è possibile visualizzare questo valore dopo aver fatto clic su **OK**.
   
   ![Schermata dei dettagli del tocco](./media/how-to-authentication-temporary-access-pass/details.png)

## <a name="use-a-tap"></a>Usa un tocco

L'uso più comune di un tocco è che un utente registri i dettagli di autenticazione durante il primo accesso, senza la necessità di completare richieste di sicurezza aggiuntive. I metodi di autenticazione sono registrati in [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . Gli utenti possono anche aggiornare i metodi di autenticazione esistenti qui.

1. Aprire un Web browser a [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) .
1. Immettere l'UPN dell'account per cui è stato creato il tocco, ad esempio *tapuser@contoso.com* .
1. Se l'utente è incluso nel criterio TAP, visualizzerà una schermata per immettere il tocco.
1. Immettere il tocco visualizzato nella portale di Azure.

   ![Screenshot della modalità di immissione di un tocco](./media/how-to-authentication-temporary-access-pass/enter.png)

>[!NOTE]
>Per i domini federati, è preferibile un tocco rispetto alla Federazione. Un utente con il tocco completa l'autenticazione in Azure AD e non viene reindirizzato al provider di identità federato (IdP).

L'utente è ora connesso ed è in grado di aggiornare o registrare un metodo come la chiave di sicurezza FIDO2. Gli utenti che aggiornano i metodi di autenticazione a causa della perdita delle credenziali o del dispositivo devono assicurarsi di rimuovere i vecchi metodi di autenticazione.

Gli utenti possono anche usare il tocco per registrarsi per l'accesso tramite telefono senza password direttamente dall'app Authenticator. Per altre informazioni, vedere [aggiungere l'account aziendale o dell'Istituto di istruzione all'app Microsoft Authenticator](../user-help/user-help-auth-app-add-work-school-account.md).

![Screenshot di come immettere un tocco usando un account aziendale o dell'Istituto di istruzione](./media/how-to-authentication-temporary-access-pass/enter-work-school.png)

## <a name="delete-a-tap"></a>Elimina un tocco

Non è possibile usare un tocco scaduto. Con i **metodi di autenticazione** per un utente, la colonna **detail** indica quando il tocco è scaduto. È possibile eliminare questi rubinetti scaduti attenendosi alla procedura seguente:

1. Nel portale di Azure AD passare a **utenti**, selezionare un utente, ad esempio *toccare utente*, quindi scegliere **metodi di autenticazione**.
1. Sul lato destro del metodo di autenticazione **pass di accesso temporaneo (anteprima)** visualizzato nell'elenco, selezionare **Elimina**.

## <a name="replace-a-tap"></a>Sostituisci un tocco 

- Un utente può avere un solo tocco. Il codice di accesso può essere usato durante l'ora di inizio e di fine del tocco.
- Se l'utente richiede un nuovo tocco:
  - Se il tocco esistente è valido, l'amministratore deve eliminare il tocco esistente e creare un nuovo passaggio per l'utente. Se si elimina un TAP valido, le sessioni dell'utente vengono revocate. 
  - Se il TAP esistente è scaduto, un nuovo tocco sostituirà il tocco esistente e non revocherà le sessioni dell'utente.

Per ulteriori informazioni sugli standard NIST per il caricamento e il ripristino, vedere la [pubblicazione speciale NIST 800-63A](https://pages.nist.gov/800-63-3/sp800-63a.html#sec4).

## <a name="limitations"></a>Limitazioni

Tenere presenti queste limitazioni:

- Quando si usa un TAP monouso per registrare un metodo senza password, ad esempio FIDO2 o l'accesso tramite telefono, l'utente deve completare la registrazione entro 10 minuti dall'accesso con il tocco monouso. Questa limitazione non si applica a un TAP che può essere utilizzato più di una volta.
- Gli utenti guest non possono accedere con un tocco.
- Gli utenti nell'ambito dei criteri di registrazione per la reimpostazione della password self-service (SSPR) saranno necessari per registrare uno dei metodi SSPR dopo aver eseguito l'accesso con TAP. Se l'utente userà solo la chiave FIDO2, escluderli dal criterio SSPR o disabilitare i criteri di registrazione di SSPR. 
- Non è possibile utilizzare TAP con l'estensione server dei criteri di rete e la scheda Active Directory Federation Services (AD FS).
- Quando l'accesso SSO facile è abilitato nel tenant, agli utenti viene richiesto di immettere una password. Il collegamento **Usa il pass di accesso temporaneo, invece** , sarà disponibile per l'accesso dell'utente con Tap.

![Screenshot di usare invece un tocco](./media/how-to-authentication-temporary-access-pass/alternative.png)

## <a name="troubleshooting"></a>Risoluzione dei problemi    

- Se il tocco non viene offerto a un utente durante l'accesso, verificare quanto segue:
  - L'utente rientra nell'ambito del criterio TAP Authentication method.
  - L'utente ha una scelta valida e, se si usa una sola volta, non è stata ancora usata.
- Se il **pass di accesso temporaneo è stato bloccato a causa del criterio delle credenziali utente** viene visualizzato durante l'accesso con Tap, verificare quanto segue:
  - L'utente dispone di un tocco di uso più lungo mentre i criteri del metodo di autenticazione richiedono un tocco monouso.
  - È già stata usata una sola volta il tocco.

## <a name="next-steps"></a>Passaggi successivi

- [Pianificare una distribuzione di autenticazione con password in Azure Active Directory](howto-authentication-passwordless-deployment.md)

