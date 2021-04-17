---
title: Esercitazione per configurare Azure Active Directory B2C con Nevis
titleSuffix: Azure AD B2C
description: Informazioni su come integrare l'Azure AD B2C con Nevis per l'autenticazione senza password
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/23/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 42e244249ecb0539637918ae2439bdb4f5da4b38
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588480"
---
# <a name="tutorial-to-configure-nevis-with-azure-active-directory-b2c-for-passwordless-authentication"></a>Esercitazione per configurare Nevis con l'Azure Active Directory B2C per l'autenticazione senza password

In questa esercitazione di esempio si apprenderà come estendere Azure AD B2C  [con Nevis](https://www.nevis.net/en/solution/authentication-cloud) per abilitare l'autenticazione senza password. Nevis offre un'esperienza utente finale completamente personalizzata per dispositivi mobili con l'app Nevis Access per fornire un'autenticazione avanzata ai clienti e rispettare i requisiti di transazione di Payment Services Directive 2 (PSD2).

## <a name="prerequisites"></a>Prerequisiti

Per iniziare, sono necessari:

- Un [account](https://www.nevis-security.com/aadb2c/) di valutazione di Nevis

- Una sottoscrizione di Azure AD. Se non si ha un account, ottenere un [account gratuito.](https://azure.microsoft.com/free/)

- Un [Azure AD B2C tenant](./tutorial-create-tenant.md) collegato alla sottoscrizione di Azure.

- Configurato Azure AD B2C'ambiente per [l'uso](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)di criteri personalizzati, se si vuole integrare Nevis nel flusso dei criteri di iscrizione.

## <a name="scenario-description"></a>Descrizione dello scenario

In questo scenario, aggiungere un'app di accesso completamente personalizzazione all'applicazione back-end per l'autenticazione senza password. I componenti seguenti costituiscono la soluzione:

- Un tenant Azure AD B2C, con criteri di accesso e iscrizione combinati al back-end
- L'istanza di Nevis e la relativa API REST per migliorare Azure AD B2C
- App di accesso personalizzato

Il diagramma mostra l'implementazione.

![Flusso di accesso con password di alto livello con Azure AD B2C e Nevis](./media/partner-nevis/nevis-architecture-diagram.png)

|Passaggio | Descrizione |
|:-----| :-----------|
| 1. | Un utente tenta di accedere a un'applicazione o di effettuare l'iscrizione Azure AD B2C criteri di accesso e iscrizione.
| 2. | Durante l'iscrizione, l'app Nevis Access viene registrata nel dispositivo utente usando un codice a codici a capo. Una chiave privata viene generata nel dispositivo utente e viene usata per firmare le richieste dell'utente.
| 3. |  Azure AD B2C un profilo tecnico RESTful per avviare l'accesso con la soluzione Nevis.
| 4. | La richiesta di accesso viene inviata all'app di accesso, come messaggio push, codice a coda o come collegamento diretto.
| 5. | L'utente approva il tentativo di accesso con la biometria. Viene quindi restituito un messaggio a Nevis, che verifica l'account di accesso con la chiave pubblica archiviata.
| 6. | Azure AD B2C invia un'ultima richiesta a Nevis per verificare che l'accesso sia stato completato correttamente.
| 7. |In base al messaggio di esito positivo/negativo Azure AD B2C utente viene concesso/negato l'accesso all'applicazione.

## <a name="integrate-your-azure-ad-b2c-tenant"></a>Integrare il tenant Azure AD B2C

### <a name="onboard-to-nevis"></a>Eseguire l'onboard in Nevis 

[Iscriversi per un account Nevis.](https://www.nevis-security.com/aadb2c/)
Si riceveranno due messaggi di posta elettronica:

1. Notifica dell'account di gestione

2. Invito di un'app per dispositivi mobili.

### <a name="add-your-azure-ad-b2c-tenant-to-your-nevis-account"></a>Aggiungere il Azure AD B2C tenant all'account Nevis

1. Dal messaggio di posta elettronica di valutazione dell'account di gestione Nevis copiare la chiave di gestione negli Appunti.

2. Aprire https://console.nevis.cloud/ in un browser.

3. Accedere alla console di gestione con la chiave.

4. Selezionare **Aggiungi istanza**

5. Selezionare l'istanza appena creata per aprirla.

6. Nella barra di spostamento laterale selezionare **Integrazioni personalizzate**

7. Selezionare **Aggiungi integrazione personalizzata**.

8. In Integration Name (Nome integrazione) immettere il nome Azure AD B2C tenant.

9. Per URL/Dominio immettere `https://yourtenant.onmicrosoft.com`

10. Selezionare **Avanti**

>[!NOTE]
>Il token di accesso nevis sarà necessario in un secondo momento.

11. Selezionare **Fine**.

### <a name="install-the-nevis-access-app-on-your-phone"></a>Installare l'app Nevis Access nel telefono

1. Dal messaggio di posta elettronica di valutazione dell'app nevis per dispositivi mobili aprire **l'invito all'app Test Flight.**

2. Installare l'app.

3. Seguire le istruzioni fornite per installare l'app Nevis Access.

### <a name="integrate-azure-ad-b2c-with-nevis"></a>Integrare Azure AD B2C con Nevis

1. Aprire il [portale di Azure](https://portal.azure.com/).

2. Passare al tenant Azure AD B2C. Assicurarsi di aver selezionato il tenant giusto, perché il tenant Azure AD B2C in genere si trova in un tenant separato.

3. Nel menu selezionare Identity Experience Framework **(IEF)**

4. Selezionare **Chiavi dei criteri**

5. Selezionare **Aggiungi** e creare una nuova chiave con le impostazioni seguenti:

      a. Selezionare **Manuale** in Opzioni

      b. Impostare Nome su **AuthCloudAccessToken**

      c. Incollare il token **di accesso Nevis archiviato** in precedenza nel campo Segreto

      d. Per Utilizzo chiavi selezionare **Crittografia**

      e. Selezionare **Crea**

### <a name="configure-and-upload-the-nevishtml-to-azure-blob-storage"></a>Configurare e caricare l'nevis.html nell'archivio BLOB di Azure

1. Nell'ambiente di gestione delle identità (IDE) passare alla [**cartella dei**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) criteri.

2. Aprire il file [**nevis.html.**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/nevis.html)

3. Sostituire il  **authentication_cloud_url** con l'URL della console di amministrazione di Nevis - `https://<instance_id>.mauth.nevis.cloud` .

4. **Salvare** le modifiche apportate al file.

5. Seguire le [istruzioni e](./customize-ui-with-html.md#2-create-an-azure-blob-storage-account) caricare il file **nevis.html** nell'archivio BLOB di Azure.

6. Seguire le [istruzioni](./customize-ui-with-html.md#3-configure-cors) e abilitare Condivisione risorse tra le origini (CORS) per questo file.

7. Dopo aver completato il caricamento e abilitato CORS, selezionare il file **nevis.html** nell'elenco.

8. Nella scheda **Panoramica,** accanto **all'URL,** selezionare **l'icona di collegamento di** copia.

9. Aprire il collegamento in una nuova scheda del browser per assicurarsi che visualizzi una casella grigia.

>[!NOTE]
>Il collegamento BLOB sarà necessario in un secondo momento.

### <a name="customize-your-trustframeworkbasexml"></a>Personalizzare la TrustFrameworkBase.xml

1. Nell'IDE passare alla cartella [**dei**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) criteri.

2. Aprire il file [**TrustFrameworkBase.xml.**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkBase.xml)

3. Sostituire **yourtenant** con il nome dell'account tenant di Azure in **TenantId**.

4. Sostituire **yourtenant** con il nome dell'account tenant di Azure in **PublicPolicyURI**.

5. Sostituire tutte **authentication_cloud_url** con l'URL della console di amministrazione di Nevis

6. **Salvare** le modifiche apportate al file.

### <a name="customize-your-trustframeworkextensionsxml"></a>Personalizzare la TrustFrameworkExtensions.xml

1. Nell'IDE passare alla cartella [**dei**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) criteri.

2. Aprire il [**fileTrustFrameworkExtensions.xml.**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/TrustFrameworkExtensions.xml)

3. Sostituire **yourtenant** con il nome dell'account tenant di Azure in **TenantId**.

4. Sostituire **yourtenant** con il nome dell'account tenant di Azure in **PublicPolicyURI**.

5. In **BasePolicy** in **TenantId** sostituire anche _il_ tenant con il nome dell'account tenant di Azure.

6. In **BuildingBlocks** sostituire **LoadUri** con l'URL del collegamento BLOBnevis.htm _l nell'account_ di archiviazione BLOB.

7. **Salvare** il file.

### <a name="customize-your-signuporsigninxml"></a>Personalizzare la SignUpOrSignin.xml

1. Nell'IDE passare alla cartella [**dei**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Nevis/policy) criteri.

2. Aprire il [**fileSignUpOrSignin.xml.**](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Nevis/policy/SignUpOrSignin.xml)

3. Sostituire **yourtenant** con il nome dell'account tenant di Azure in **TenantId**.

4. Sostituire **yourtenant** con il nome dell'account tenant di Azure in **PublicPolicyUri**.

5. In **BasePolicy** in **TenantId** sostituire **anche il** tenant con il nome dell'account tenant di Azure.

6. **Salvare** il file.

### <a name="upload-your-custom-policies-to-azure-ad-b2c"></a>Caricare i criteri personalizzati in Azure AD B2C

1. Aprire la home [Azure AD B2C tenant.](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)

2. Fare clic su **Framework dell'esperienza di gestione delle identità**.

3. Selezionare **Carica criteri personalizzati**.

4. Selezionare il **TrustFrameworkBase.xml** file modificato.

5. Selezionare la **casella di controllo Sovrascrivi i criteri** personalizzati se esiste già.
6. Selezionare **Carica**.

7. Ripetere i passaggi 5 e 6 **perTrustFrameworkExtensions.xml**.

8. Ripetere i passaggi 5 e 6 **perSignUpOrSignin.xml**.

## <a name="test-the-user-flow"></a>Testare il flusso utente

### <a name="test-account-creation-and-nevis-access-app-setup"></a>Testare la creazione dell'account e la configurazione dell'app Nevis Access

1. Aprire la home [Azure AD B2C tenant.](https://portal.azure.com/#blade/Microsoft_AAD_B2CAdmin/TenantManagementMenuBlade/overview)

2. Fare clic su **Framework dell'esperienza di gestione delle identità**.

3. Scorrere verso il basso fino a Criteri personalizzati e **selezionare B2C_1A_signup_signin**.

4. Selezionare **Esegui adesso**.

5. Nella finestra popup selezionare **Iscriviti ora.**

6. Aggiungere l'indirizzo di posta elettronica.

7. Selezionare **Invia codice di verifica**.

8. Copiare il codice di verifica dal messaggio di posta elettronica.

9. Selezionare **Verifica**.

10. Compilare il modulo con la nuova password e il nome visualizzato.

11. Selezionare **Crea**.

12. Verrà visualizzata la pagina di analisi del codice a codici a codici.

13. Nel telefono aprire **l'app Nevis Access**.

14. Selezionare **Face ID**.

15. Quando la schermata indica che **la registrazione di Authenticator è riuscita,** selezionare **Continua.**

16. Sul telefono eseguire nuovamente l'autenticazione con il viso.

17. Verrà visualizzata la pagina di destinazione [jwt.ms](https://jwt.ms) i dettagli del token decodificato.

### <a name="test-the-pure-passwordless-sign-in"></a>Testare l'accesso senza password puro

1. In **Identity Experience Framework** selezionare il **B2C_1A_signup_signin**.

2. Selezionare **Esegui adesso**.

3. Nella finestra popup selezionare **Autenticazione senza password.**

4. Immettere l'indirizzo di posta elettronica.

5. Selezionare **Continua**.

6. Nel telefono, in Notifiche, selezionare Notifica **dell'app Nevis Access.**

7. Eseguire l'autenticazione con il viso.

8. Verrà visualizzata automaticamente la pagina [jwt.ms](https://jwt.ms) di destinazione in cui vengono visualizzati i token.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti

- [Criteri personalizzati in AAD B2C](./custom-policy-overview.md)

- [Introduzione ai criteri personalizzati in Azure AD B2C](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
