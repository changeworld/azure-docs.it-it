---
title: Autenticazione con passcode monouso per utenti guest B2B - Azure AD
description: Come usare il passcode monouso tramite indirizzo di posta elettronica per autenticare gli utenti guest B2B senza la necessità di un account Microsoft.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: CelesteDG
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b4089559b341dd268928b1f150b6fc173869ead
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529917"
---
# <a name="email-one-time-passcode-authentication"></a>Autenticazione con passcode una sola volta tramite posta elettronica

Questo articolo descrive come abilitare l'autenticazione con passcode una sola volta tramite posta elettronica per gli utenti guest B2B. La funzionalità passcode una sola volta tramite posta elettronica autentica gli utenti guest B2B quando non possono essere autenticati con altri mezzi, ad esempio Azure AD, un account Microsoft (MSA) o la federazione di Google. Grazie all'autenticazione con passcode monouso, non è necessario creare un account Microsoft. Quando l'utente guest riscatta un invito o accede a una risorsa condivisa, può richiedere un codice temporaneo, che viene inviato all'indirizzo di posta elettronica. Quindi immette tale codice per continuare ad accedere.

![Diagramma di panoramica del passcode una sola volta tramite posta elettronica](media/one-time-passcode/email-otp.png)

> [!IMPORTANT]
> - **A partire da ottobre 2021,** la funzionalità passcode una sola volta tramite posta elettronica verrà attivata per tutti i tenant esistenti e abilitata per impostazione predefinita per i nuovi tenant. Se non si vuole consentire l'attivazione automatica di questa funzionalità, è possibile disabilitarla. Vedere [Disabilitare il passcode una sola volta per la posta](#disable-email-one-time-passcode) elettronica di seguito.
> - Le impostazioni del passcode una sola volta tramite posta elettronica sono state spostate nel portale di Azure da Impostazioni di **collaborazione esterna** a Tutti i provider di **identità.**

> [!NOTE]
> Gli utenti con passcode monouso devono accedere tramite un collegamento che include il contesto tenant (ad esempio `https://myapps.microsoft.com/?tenantid=<tenant id>` o `https://portal.azure.com/<tenant id>` oppure, nel caso di un dominio verificato, `https://myapps.microsoft.com/<verified domain>.onmicrosoft.com`). È possibile usare anche collegamenti diretti alle applicazioni e alle risorse, purché includano tale contesto. Gli utenti guest attualmente non possono accedere tramite endpoint privi di un contesto tenant. Ad esempio, `https://myapps.microsoft.com` l'uso `https://portal.azure.com` di restituirà un errore.

## <a name="user-experience-for-one-time-passcode-guest-users"></a>Esperienza utente per gli utenti guest con passcode monouso

Quando è abilitata la funzionalità passcode una [](#when-does-a-guest-user-get-a-one-time-passcode) sola volta per i messaggi di posta elettronica, gli utenti appena invitati che soddisfano determinate condizioni useranno l'autenticazione con passcode una sola volta. Gli utenti guest che hanno riscattato un invito prima che sia stato abilitato il passcode una sola volta tramite posta elettronica continueranno a usare lo stesso metodo di autenticazione.

Grazie all'autenticazione con passcode monouso, l'utente guest può riscattare l'invito facendo clic su un collegamento diretto o tramite l'indirizzo di posta elettronica di invito. In entrambi i casi, un messaggio nel browser indica che verrà inviato un codice all'indirizzo di posta elettronica dell'utente guest. L'utente guest seleziona **Send code** (Invia codice):

   ![Screenshot che mostra il pulsante Invia codice](media/one-time-passcode/otp-send-code.png)

Un passcode viene inviato all'indirizzo di posta elettronica dell'utente. L'utente recupera il passcode dal messaggio di posta elettronica e lo immette nella finestra del browser:

   ![Screenshot che mostra la pagina Immetti il codice](media/one-time-passcode/otp-enter-code.png)

L'utente guest viene autenticato e può quindi visualizzare la risorsa condivisa o continuare ad accedere.

> [!NOTE]
> I passcode monouso sono validi per 30 minuti. Dopo 30 minuti, tale passcode monouso specifico non è più valido e l'utente deve richiederne uno nuovo. Le sessioni utente scadono dopo 24 ore. Dopo tale periodo, l'utente guest riceve un nuovo passcode quando accede alla risorsa. La scadenza della sessione offre una sicurezza maggiore, in particolare quando un utente guest lascia la società o non ha più bisogno dell'accesso.

## <a name="when-does-a-guest-user-get-a-one-time-passcode"></a>In quali casi un utente guest ottiene un passcode monouso?

Quando un utente guest riscatta un invito o usa un collegamento a una risorsa che è stato condivisa con tale utente, questo riceve un passcode monouso se:

- Non ha un account Azure AD
- Non ha un account Microsoft
- Il tenant che emette l'invito non ha impostato federazione Google per gli utenti @gmail.com e @googlemail.com

Al momento dell'invito non è presente alcuna indicazione del fatto che l'utente che si sta invitando userà l'autenticazione con passcode monouso. Tuttavia quando l'utente guest accede, l'autenticazione con passcode monouso sarà il metodo di fallback se non è possibile utilizzare altri metodi di autenticazione.

È possibile verificare se un utente guest esegue l'autenticazione usando passcode una sola volta visualizzando la **proprietà Source** nei dettagli dell'utente. Nel riquadro portale di Azure passare **a** Azure Active Directory Utenti e quindi selezionare l'utente per aprire la pagina dei  >  dettagli.

![Screenshot che mostra un utente con passcode monouso e il valore di origine Passcode monouso](media/one-time-passcode/guest-user-properties.png)

> [!NOTE]
> Quando un utente riscatta un passcode monouso e in un secondo momento ottiene un account Microsoft, un account di Azure AD o un altro account federato, continuerà a essere autenticato con un passcode monouso. Se si vuole aggiornare il metodo di autenticazione dell'utente, è possibile reimpostarne [lo stato di riscatto](reset-redemption-status.md).

### <a name="example&quot;></a>Esempio

Utente guest teri@gmail.com viene invitato a Fabrikam, che non dispone di federazione Google configurata. Teri non ha un account Microsoft. Riceverà un passcode monouso per l'autenticazione.

## <a name=&quot;disable-email-one-time-passcode&quot;></a>Disabilitare il passcode di posta elettronica una sola volta

A partire da ottobre 2021, la funzionalità passcode una sola volta tramite posta elettronica verrà attivata per tutti i tenant esistenti e abilitata per impostazione predefinita per i nuovi tenant. A quel punto, Microsoft non supporterà più il riscatto degli inviti creando account e tenant non gestiti (&quot;virali&quot; o &quot;just-in-time") Azure AD per gli scenari di collaborazione B2B. È in fase di abilitazione la funzionalità passcode una sola volta per la posta elettronica perché offre un metodo di autenticazione di fallback trasparente per gli utenti guest. È tuttavia possibile disabilitare questa funzionalità se si sceglie di non usarla.

> [!NOTE]
>
> Se nel tenant è stata abilitata la funzionalità passcode una sola volta del messaggio di posta elettronica e la si disattiva, gli utenti guest che hanno riscattato un passcode una sola volta non saranno in grado di accedere. È possibile reimpostare [lo stato di riscatto](reset-redemption-status.md) in modo che possano accedere di nuovo usando un altro metodo di autenticazione.

### <a name="to-disable-the-email-one-time-passcode-feature"></a>Per disabilitare la funzionalità passcode una sola volta tramite posta elettronica

1. Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale di Azure AD.

2. Nel riquadro di spostamento selezionare **Azure Active Directory**.

3. Selezionare **Identità esterne** > **Tutti i provider di identità**.

4. Selezionare **Email one-time passcode (Invia un unico passcode** tramite posta elettronica) e quindi **selezionare Disable email one-time passcode for guests**(Disabilita passcode unico di posta elettronica per guest).

   > [!NOTE]
   > Le impostazioni del passcode una sola volta tramite posta elettronica sono state spostate nel portale di Azure da Impostazioni di **collaborazione esterna** a Tutti i provider di **identità.**
   > Se viene visualizzato un interruttore anziché le opzioni del passcode una sola volta tramite posta elettronica, significa che in precedenza è stata abilitata, disabilitata o acconsentito esplicitamente all'anteprima della funzionalità. Selezionare **No** per disabilitare la funzionalità.
   >
   >![Disattivazione del passcode una sola volta tramite posta elettronica disabilitata](media/one-time-passcode/enable-email-otp-disabled.png)

5. Selezionare **Salva**.

## <a name="note-for-public-preview-customers"></a>Nota per i clienti dell'anteprima pubblica

Se in precedenza si è acconsentito esplicitamente all'anteprima pubblica con passcode unico di posta elettronica, la data di ottobre 2021 per l'abilitazione automatica delle funzionalità non è applicabile, quindi i processi aziendali correlati non saranno interessati. Inoltre, nel portale di Azure, nelle proprietà Invia un **passcode** una sola volta all'indirizzo di posta elettronica per gli utenti guest, non verrà visualizzata l'opzione Abilita automaticamente il passcode una sola volta per i guest a partire da ottobre **2021.** Verrà invece visualizzato l'interruttore **Sì** **o No** seguente:

![Passcode una sola volta di posta elettronica: consenso esplicito](media/one-time-passcode/enable-email-otp-opted-in.png)

Tuttavia, se si preferisce rifiutare esplicitamente la funzionalità e consentirla automaticamente di attivarla a ottobre 2021, [](/graph/api/resources/emailauthenticationmethodconfiguration)è possibile ripristinare le impostazioni predefinite usando il tipo di risorsa di configurazione del metodo di autenticazione tramite posta elettronica dell'API Microsoft Graph . Dopo aver ripristinato le impostazioni predefinite, le opzioni seguenti saranno disponibili in **Email one-time passcode for guests**(Invia un passcode unico tramite posta elettronica per i guest):

![Abilitare il passcode una sola volta per la posta elettronica](media/one-time-passcode/email-otp-options.png)

- **Abilitare automaticamente il passcode di posta elettronica una sola volta per i guest a partire da ottobre 2021.** (Impostazione predefinita) Se la funzionalità passcode una sola volta del messaggio di posta elettronica non è già abilitata per il tenant, verrà attivata automaticamente a partire da ottobre 2021. Non sono necessarie altre azioni se si vuole che la funzionalità sia abilitata in quel momento. Se la funzionalità è già stata abilitata o disabilitata, questa opzione non sarà disponibile.

- **Abilitare il passcode di posta elettronica una sola volta per i guest con validità .** Attiva la funzionalità passcode di posta elettronica una sola volta per il tenant.

- **Disabilitare il passcode di posta elettronica una sola volta per i guest**. Disattiva la funzionalità passcode una sola volta per il tenant e impedisce l'attivazione della funzionalità nel mese di ottobre 2021.

## <a name="note-for-azure-us-government-customers"></a>Nota per i clienti di Azure US Government

La funzionalità passcode una sola volta per la posta elettronica è disabilitata per impostazione predefinita nel cloud di Azure us government. I partner non potranno accedere a meno che questa funzionalità non sia abilitata. A differenza del cloud pubblico di Azure, il cloud azure us government non supporta il riscatto degli inviti con account Azure Active Directory self-service.

 ![Passcode una sola volta disabilitato per la posta elettronica](media/one-time-passcode/enable-email-otp-disabled.png)

Per abilitare la funzionalità passcode una sola volta per la posta elettronica nel cloud azure us government:

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore globale di Azure AD.
2. Nel riquadro di spostamento selezionare **Azure Active Directory**.
3. Selezionare **Relazioni organizzative Tutti** i provider di   >  **identità**.

   > [!NOTE]
   > - Se le relazioni **organizzative** non sono presenti, cercare "Identità esterne" nella barra di ricerca nella parte superiore.

4. Selezionare **Email one-time passcode** e quindi **Selezionare Sì.**
5. Selezionare **Salva**.

Per altre informazioni sulle limitazioni correnti, vedere [Cloud di Azure us government.](current-limitations.md#azure-us-government-clouds)
