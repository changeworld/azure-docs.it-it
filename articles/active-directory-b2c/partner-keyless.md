---
title: Esercitazione per la configurazione di senza chiave con Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Esercitazione per la configurazione di una chiave digitale con Azure Active Directory B2C per l'autenticazione senza password
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 1/17/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: c8a5666d373852da5ff79490f435b2d66d5cc6e0
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/30/2021
ms.locfileid: "99090367"
---
# <a name="tutorial-configure-keyless-with-azure-active-directory-b2c"></a>Esercitazione: configurare senza chiave con Azure Active Directory B2C

In questa esercitazione di esempio vengono fornite istruzioni su come configurare Azure Active Directory (AD) B2C con [autochiave](https://keyless.io/). Con Azure AD B2C come provider di identità, è possibile integrare la chiave digitale con le applicazioni dei clienti per offrire agli utenti un'autenticazione senza password vera e propria.

La soluzione senza chiave senza chiave **Zero-Knowledge biometrica (ZKB™)** fornisce l'autenticazione a più fattori senza password che elimina la frode, il phishing e il riutilizzo delle credenziali, ottimizzando al contempo l'esperienza dei clienti e proteggendo la privacy.

## <a name="pre-requisites"></a>Prerequisiti

Per iniziare, è necessario:

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile ottenere un [account gratuito](https://azure.microsoft.com/free/).

- [Tenant Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Il tenant deve essere collegato alla sottoscrizione di Azure.

- Un tenant cloud senza chiave, ottenere un [account di valutazione](https://keyless.io/go)gratuito.

- L'app di autenticazione con chiave digitale installata sul dispositivo dell'utente.

## <a name="scenario-description"></a>Descrizione dello scenario

L'integrazione con chiave digitale include i componenti seguenti:

- Azure AD B2C: il server di autorizzazione, responsabile della verifica delle credenziali dell'utente, noto anche come provider di identità.

- Applicazioni Web e per dispositivi mobili: applicazioni per dispositivi mobili o Web che si scelgono di proteggere con Azure AD B2C e senza chiave.

- L'app per dispositivi mobili con chiave digitale, che verrà usata per l'autenticazione per le applicazioni abilitate per Azure AD B2C.

Il diagramma dell'architettura seguente illustra l'implementazione di.

![Immagine che mostra il diagramma dell'architettura a chiave](./media/partner-keyless/keyless-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | L'utente arriva a una pagina di accesso. Gli utenti selezionano l'accesso/iscrizione e immette il nome utente
| 2. | L'applicazione invia gli attributi utente a Azure AD B2C per la verifica dell'identità.
| 3. | Azure AD B2C raccoglie gli attributi utente e invia gli attributi a autochiave per autenticare l'utente tramite l'app per dispositivi mobili con chiave.
| 4. | Il dispositivo senza chiave invia una notifica push al dispositivo mobile dell'utente registrato per l'autenticazione con mantenimento della privacy sotto forma di analisi biometrica facciale.
| 5. | Dopo che l'utente ha risposto alla notifica push, all'utente viene concesso o negato l'accesso all'applicazione del cliente in base ai risultati della verifica.

## <a name="integrate-with-azure-ad-b2c"></a>Integrazione con Azure AD B2C

### <a name="add-a-new-identity-provider"></a>Aggiungere un nuovo provider di identità

Per aggiungere un nuovo provider di identità, attenersi alla procedura seguente:

1. Accedere al **[portale di Azure](https://portal.azure.com/#home)** come amministratore globale del tenant di Azure ad B2C.

2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant.

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra del portale di Azure, cercare **Azure AD B2C** e selezionarlo.

4. Passa a **Dashboard**  >  **Azure Active Directory B2C**  >   **provider di identità**

5. Selezionare **provider di identità**.

6. Selezionare **Aggiungi**.

### <a name="configure-an-identity-provider"></a>Configurare un provider di identità

Per configurare un provider di identità, attenersi alla procedura seguente:

1. Selezionare il **tipo di provider di identità**  >  **OpenID Connect (anteprima)**
2. Compilare il modulo per configurare il provider di identità:

   |Proprietà | Valore |
   |:-----| :-----------|
   | Nome   | Keyless |
   | URL dei metadati | Inserire l'URI dell'app di autenticazione con chiave digitale ospitata, seguita dal percorso specifico, ad esempio ' https://keyless.auth/.well-known/openid-configuration ' |
   | Client Secret | Il segreto associato all'istanza di autenticazione senza chiave, non uguale a quello configurato in precedenza. Inserire una stringa complessa di propria scelta. Questo segreto verrà usato in un secondo momento nella configurazione del contenitore con chiave privata.|
   | ID client | ID del client. Questo ID verrà usato in un secondo momento nella configurazione del contenitore con chiave automatica.|
   | Ambito | openid |
   | Tipo di risposta | id_token |
   | Modalità di risposta | form_post|

3. Selezionare **OK**.

4. Selezionare Esegui **mapping delle attestazioni del provider di identità**.

5. Compilare il modulo per eseguire il mapping del provider di identità:

   |Proprietà | Valore |
   |:-----| :-----------|
   | UserID    | Dalla sottoscrizione |
   | Nome visualizzato | Dalla sottoscrizione |
   | Modalità di risposta | Dalla sottoscrizione |

6. Selezionare **Save (Salva** ) per completare la configurazione del nuovo provider di identità Open ID Connect (OIDC).

### <a name="create-a-user-flow-policy"></a>Creare un criterio di flusso utente

A questo punto dovrebbe essere visualizzato un nuovo provider di identità OIDC elencato nei provider di identità B2C.

1. Nel tenant di Azure AD B2C, in **criteri**, selezionare **flussi utente**.

2. Selezionare **nuovo** flusso utente.

3. Selezionare **iscrizione e accesso**, selezionare una **versione** e quindi fare clic su **Crea**.

4. Immettere un **nome** per il criterio.

5. Nella sezione Identity Providers (provider di identità) selezionare il provider di identità con chiave digitale appena creato.

6. Configurare i parametri del flusso utente. Inserire un nome e selezionare il provider di identità creato. È anche possibile aggiungere un indirizzo di posta elettronica. In questo caso, Azure non reindirizza la procedura di accesso direttamente a senza chiave, bensì visualizzerà una schermata in cui l'utente può scegliere l'opzione che si vuole usare.

7. Lasciare invariato il campo di autenticazione a più **fattori** .

8. Selezionare **Applica criteri di accesso condizionale**

9. In **attributi utente e attestazioni token** selezionare **indirizzo di posta elettronica** nell'opzione Raccogli attributo. È possibile aggiungere tutti gli attributi che Azure Active Directory possibile raccogliere sull'utente insieme alle attestazioni che Azure AD B2C possibile restituire all'applicazione client.

10. Selezionare **Crea**.

11. Al termine della creazione, selezionare il nuovo **flusso utente**.

12. Nel pannello sinistro selezionare **attestazioni applicazione**. In opzioni, selezionare la casella di controllo **posta elettronica** e selezionare **Salva**.

## <a name="test-the-user-flow"></a>Testare il flusso utente

1. Aprire il tenant di Azure AD B2C e in criteri selezionare Framework esperienza di identità.

2. Selezionare il SignUpSignIn creato in precedenza.

3. Selezionare Esegui flusso utente e selezionare le impostazioni:

   a. Applicazione: selezionare l'app registrata (l'esempio è JWT)

   b. URL di risposta: selezionare l'URL di Reindirizzamento

   c. Selezionare Esegui il flusso utente.

4. Esaminare il flusso di iscrizione e creare un account

5. Quando si crea l'attributo User, viene chiamato il metodo senza chiave digitale durante il flusso. Se il flusso è incompleto, controllare che l'utente non sia salvato nella directory.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni, vedere gli articoli seguenti:

- [Criteri personalizzati in AAD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Introduzione ai criteri personalizzati in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
