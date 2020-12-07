---
title: Configurare il provider di identità dell'account locale
titleSuffix: Azure AD B2C
description: Definire i tipi di identità che è possibile usare (posta elettronica, nome utente, numero di telefono) per l'autenticazione dell'account locale quando si configurano i flussi utente nel tenant di Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/29/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0ce9b60ead9aff792bf2e4e98841469d58620ccd
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754619"
---
# <a name="set-up-phone-sign-up-and-sign-in-for-user-flows-preview"></a>Configurare l'iscrizione e l'accesso tramite telefono per i flussi utente (anteprima)

> [!NOTE]
> Le funzionalità per l'iscrizione e l'accesso tramite telefono e la posta elettronica di ripristino per i flussi utente sono disponibili in anteprima pubblica.

Oltre alla posta elettronica e al nome utente, è possibile abilitare il numero di telefono come opzione di iscrizione a livello di tenant aggiungendo l'iscrizione e l'accesso tramite telefono al provider di identità dell'account locale. Dopo aver abilitato l'iscrizione e l'accesso tramite telefono per gli account locali, è possibile aggiungere l'iscrizione tramite telefono ai flussi utente.

Per configurare l'iscrizione e l'accesso tramite telefono in un flusso utente sono necessari i passaggi seguenti:

- [Configurare l'iscrizione e l'accesso a livello di tenant del telefono](#configure-phone-sign-up-and-sign-in-tenant-wide) nel provider di identità dell'account locale per accettare un numero di telefono come identità dell'utente. 

- [Aggiungere la registrazione del telefono al flusso utente](#add-phone-sign-up-to-a-user-flow) per consentire agli utenti di iscriversi all'applicazione usando il numero di telefono.

- [Abilitare la richiesta di posta elettronica di ripristino (anteprima)](#enable-the-recovery-email-prompt-preview) per consentire agli utenti di specificare un messaggio di posta elettronica che può essere usato per recuperare il proprio account quando non hanno il telefono.

Multi-factor authentication è disabilitato per impostazione predefinita quando si configura un flusso utente con l'iscrizione tramite telefono. È possibile abilitare l'autenticazione a più fattori nei flussi utente con l'iscrizione tramite telefono, ma poiché un numero di telefono viene usato come identificatore primario, il codice di posta elettronica monouso è l'unica opzione disponibile per il secondo fattore di autenticazione.

## <a name="configure-phone-sign-up-and-sign-in-tenant-wide"></a>Configurare l'iscrizione e l'accesso tramite telefono a livello di tenant

L'iscrizione tramite posta elettronica è abilitata per impostazione predefinita nelle impostazioni del provider di identità dell'account locale. È possibile modificare i tipi di identità supportati nel tenant selezionando o deselezionando l'iscrizione tramite posta elettronica, il nome utente o il numero di telefono.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Assicurarsi di usare la directory che contiene il tenant di Azure AD B2C selezionando il filtro **directory + sottoscrizione** nel menu in alto e scegliendo la directory che contiene il tenant del Azure ad.

3. Scegliere **Tutti i servizi** nell'angolo in alto a sinistra nel portale di Azure e quindi cercare e selezionare **Azure AD B2C**.

4. In **Gestisci** selezionare **provider di identità**.

5. Nell'elenco provider di identità selezionare **account locale**.

   ![Provider di identità Selezione account locale](media/phone-authentication-user-flows/identity-provider-local-account.png)

1. Nella pagina **Configura IDP locale** assicurarsi che sia selezionato **telefono** come uno dei tipi di identità consentiti che i consumer possono usare per creare gli account locali nel tenant Azure ad B2C.

   ![Selezionare i tipi di identità consentiti](media/phone-authentication-user-flows/configure-local-idp.png)

1. Selezionare **Salva**.

## <a name="add-phone-sign-up-to-a-user-flow"></a>Aggiungere la registrazione tramite telefono a un flusso utente

Dopo aver aggiunto l'iscrizione tramite telefono come opzione di identità per gli account locali, è possibile aggiungerla ai flussi utente, purché siano le versioni più recenti del flusso utente **consigliate** . Di seguito è riportato un esempio che illustra come aggiungere l'iscrizione tramite telefono ai nuovi flussi utente. Tuttavia, è anche possibile aggiungere l'iscrizione tramite telefono a flussi utente consigliati per la versione esistente (selezionare **flussi utente**  >  *Nome flusso utente*  >  **identità provider**  >  **account locale iscrizione telefono**). 

Di seguito è riportato un esempio che illustra come aggiungere l'iscrizione tramite telefono a un nuovo flusso utente.

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.

    ![Tenant B2C, riquadro Directory e sottoscrizione, portale di Azure](./media/phone-authentication-user-flows/directory-subscription-pane.png)

3. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
4. In **Criteri** selezionare **Flussi utente** e quindi **Nuovo flusso utente**.

    ![Pagina Flussi utente nel portale con il pulsante Nuovo flusso utente evidenziato](./media/phone-authentication-user-flows/signup-signin-user-flow.png)

5. Nella pagina **Crea un flusso utente** selezionare il flusso utente **Iscrizione e accesso**.

    ![Selezionare una pagina flusso utente con il flusso di iscrizione e accesso evidenziato](./media/phone-authentication-user-flows/select-user-flow-type.png)

6. In **Selezionare una versione** selezionare **Consigliata**, quindi selezionare **Crea**. Per altre informazioni sulle versioni dei flussi utente, vedere [qui](user-flow-versions.md).

    ![Pulsante Crea flusso utente](./media/phone-authentication-user-flows/select-version.png)

7. Immettere un **nome** per il flusso utente. Ad esempio, *signupsignin1*.
8. Nella sezione **Identity Providers** in **local accounts** Selezionare **Phone iscrizione**.

   ![Opzione di iscrizione telefono flusso utente selezionata](media/phone-authentication-user-flows/user-flow-phone-signup.png)

9. In **provider di identità di social** networking selezionare tutti gli altri provider di identità che si desidera consentire per questo flusso utente.

   > [!NOTE]
   > Multi-factor authentication è disabilitato per impostazione predefinita. È possibile abilitare l'autenticazione a più fattori per un flusso utente di iscrizione tramite telefono, ma poiché viene usato un numero di telefono come identificatore primario, il codice di posta elettronica monouso è l'unica opzione disponibile per il secondo fattore di autenticazione.

1. Nella sezione **attributi utente e attestazioni token** scegliere le attestazioni e gli attributi che si desidera raccogliere e inviare dall'utente durante l'iscrizione. Ad esempio, selezionare **Mostra più** e quindi scegliere gli attributi e le attestazioni per **Paese/Area geografica**, **Nome visualizzato** e **Codice postale**. Selezionare **OK**.

1. Selezionare **Crea** per aggiungere il flusso utente. Viene automaticamente aggiunto al nome il prefisso *B2C_1*.

## <a name="enable-the-recovery-email-prompt-preview"></a>Abilitare la richiesta di posta elettronica di ripristino (anteprima)

Quando si Abilita l'iscrizione e l'accesso tramite telefono per i flussi utente, è anche consigliabile abilitare la funzionalità di posta elettronica di ripristino. Con questa funzionalità, un utente può specificare un indirizzo di posta elettronica che può essere usato per recuperare il proprio account quando non dispone del telefono. Questo indirizzo di posta elettronica viene usato solo per il recupero dell'account. Non può essere usato per l'accesso.

- Quando la richiesta di posta elettronica di ripristino è **attiva**, viene richiesto un utente che esegue la prima volta per verificare un messaggio di posta elettronica di backup. Un utente che non ha fornito un messaggio di posta elettronica di ripristino prima di viene richiesto di verificare un messaggio di posta elettronica di backup durante l'accesso successivo.

- Quando il messaggio di posta elettronica di ripristino è **disattivato**, un utente che effettua l'iscrizione o l'accesso non Visualizza il messaggio di posta elettronica di ripristino.
 
È possibile abilitare la richiesta di posta elettronica di ripristino nelle proprietà del flusso utente.

> [!NOTE]
> Prima di iniziare, verificare [di avere aggiunto l'iscrizione tramite telefono al flusso utente](#add-phone-sign-up-to-a-user-flow) come descritto in precedenza.

### <a name="to-enable-the-recovery-email-prompt"></a>Per abilitare la richiesta di posta elettronica di ripristino

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Selezionare l'icona **Directory e sottoscrizione** nella barra degli strumenti del portale e quindi la directory contenente il tenant di Azure AD B2C.
3. Nel portale di Azure cercare e selezionare **Azure AD B2C**.
4. In Azure AD B2C, in **criteri**, selezionare **flussi utente**.
5. Selezionare il flusso utente nell'elenco.
6. In **Impostazioni** selezionare **Proprietà**.
7. Accanto a **abilitare la richiesta di posta elettronica di ripristino per l'iscrizione al numero di telefono e l'accesso (anteprima)**, selezionare:

   - **Su** per visualizzare la richiesta di posta elettronica di ripristino durante l'iscrizione e l'accesso.
   - **Disattivato** per nascondere la richiesta di posta elettronica di ripristino.

    ![Proprietà dei flussi utente con abilitazione della posta elettronica di ripristino abilitata](./media/phone-authentication-user-flows/recovery-email-settings.png)

8. Selezionare **Salva**.

### <a name="to-test-the-recovery-email-prompt"></a>Per testare la richiesta di posta elettronica di ripristino

Dopo aver abilitato l'iscrizione e l'accesso tramite telefono e il messaggio di posta elettronica di ripristino nel flusso utente, è possibile usare **Esegui flusso utente** per testare l'esperienza utente.

1. Selezionare **criteri**  >  **flussi utente** e quindi selezionare il flusso utente creato. Nella pagina Panoramica flusso utente selezionare **Esegui flusso utente**.

2. Per **applicazione**, selezionare l'applicazione Web registrata nel passaggio 1. L'**URL di risposta** dovrebbe mostrare `https://jwt.ms`.

3. Selezionare **Esegui flusso utente** e verificare il comportamento seguente:

   - Un utente che si iscrive per la prima volta viene chiesto di fornire un messaggio di posta elettronica di ripristino. 
   - Un utente che ha già effettuato l'iscrizione ma non ha fornito un messaggio di posta elettronica di ripristino viene chiesto di specificarne uno al momento dell'accesso.

4. Immettere un indirizzo di posta elettronica e quindi selezionare **Invia codice di verifica**. Verificare che venga inviato un codice alla cartella posta in arrivo specificata. Recuperare il codice e immetterlo nella casella **codice di verifica** . Quindi selezionare **Verifica codice**.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere provider di identità esterni](tutorial-add-identity-providers.md)
- [Creare un flusso utente](tutorial-create-user-flows.md)