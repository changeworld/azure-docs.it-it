---
title: Aggiungere un flusso utente di iscrizione self-service - Azure AD
description: Creare flussi utente per le app compilate dall'organizzazione. Quindi, gli utenti che visitano l'app possono ottenere un account Guest usando le opzioni configurate nel flusso utente.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a59e573c9b24d9a8b5577b55d143fcaca67952f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102120760"
---
# <a name="add-a-self-service-sign-up-user-flow-to-an-app"></a>Aggiungere un flusso utente di iscrizione self-service a un'app

> [!NOTE]
> Alcune delle funzionalità descritte in questo articolo sono le funzionalità di anteprima pubblica di Azure Active Directory. Per altre informazioni sulle anteprime, vedere [Condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Per le applicazioni compilate, è possibile creare flussi utente che consentono a un utente di iscriversi a un'app e creare un nuovo account Guest. Un flusso utente di iscrizione Self-Service definisce la serie di passaggi che l'utente seguirà durante l'iscrizione, i provider di identità consentiti per l'uso e gli attributi dell'utente che si desidera raccogliere. È possibile associare una o più applicazioni a un singolo flusso utente.

> [!NOTE]
> È possibile associare i flussi utente alle app create dall'organizzazione. Non è possibile usare i flussi utente per le app Microsoft, ad esempio SharePoint o Teams.

## <a name="before-you-begin"></a>Prima di iniziare

### <a name="add-identity-providers-optional"></a>Aggiungi provider di identità (facoltativo)

Azure AD è il provider di identità predefinito per l'iscrizione self-service. Questo significa che per impostazione predefinita gli utenti possono effettuare l'iscrizione con un account Azure AD. Nei flussi utente di iscrizione self-service, è anche possibile includere provider di identità basati su social network come Google e Facebook, account Microsoft (anteprima) e codice di posta elettronica monouso (anteprima).

- [Provider di identità dell'account Microsoft (anteprima)](microsoft-account.md)
- [Autenticazione del codice di accesso monouso tramite posta elettronica](one-time-passcode.md)
- [Aggiungere Facebook all'elenco di provider di identità basati su social network](facebook-federation.md)
- [Aggiungere Google all'elenco di provider di identità basati su social network](google-federation.md)

### <a name="define-custom-attributes-optional"></a>Definire attributi personalizzati (facoltativo)

Gli attributi utente sono valori raccolti dall'utente durante l'iscrizione self-service. Azure AD include un set predefinito di attributi, ma è possibile creare attributi personalizzati da usare nel flusso utente. Questi attributi supportano anche le operazioni di lettura e scrittura usando l'API Microsoft Graph. Vedere [Definire attributi personalizzati per i flussi utente](user-flow-add-custom-attributes.md).

## <a name="enable-self-service-sign-up-for-your-tenant"></a>Abilitare l'iscrizione self-service per il tenant

Prima di poter aggiungere un flusso utente di iscrizione self-service alle applicazioni, è necessario abilitare la funzionalità per il tenant. Dopo l'abilitazione, nel flusso utente diventano disponibili i controlli che consentono di associare il flusso utente a un'applicazione.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. In **Servizi di Azure** selezionare **Azure Active Directory**.
3. Selezionare **Impostazioni utente**, quindi in **Utenti esterni** selezionare **Gestisci le impostazioni di collaborazione esterna**.
4. Impostare l'opzione **Abilita i flussi di accesso self-service con i flussi utente** su **Sì**.

   ![Abilitare l'iscrizione self-service per utenti guest](media/self-service-sign-up-user-flow/enable-self-service-sign-up.png)
5. Selezionare **Salva**.
## <a name="create-the-user-flow-for-self-service-sign-up"></a>Creare il flusso utente per l'iscrizione self-service

Verrà creato il flusso utente per l'iscrizione self-service e lo si aggiungerà a un'applicazione.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. In **Servizi di Azure** selezionare **Azure Active Directory**.
3. Nel menu a sinistra selezionare **Identità esterne**.
4. Selezionare **flussi utente**, quindi selezionare **nuovo flusso utente**.

   ![Pulsante per l'aggiunta di un nuovo flusso utente](media/self-service-sign-up-user-flow/new-user-flow.png)

5. Nella pagina **Crea** immettere un **Nome** per il flusso utente. Si noti che al nome viene automaticamente aggiunto il prefisso **B2X_1_** .
6. Nell'elenco **provider di identità** selezionare uno o più provider di identità che gli utenti esterni potranno usare per accedere all'applicazione. Per impostazione predefinita è selezionata la voce **Iscrizione ad Azure Active Directory**. Per informazioni su come aggiungere i provider di identità, vedere la sezione [Prima di iniziare](#before-you-begin) più indietro in questo articolo.
7. In **Attributi utente** scegliere gli attributi da raccogliere dall'utente. Per visualizzare attributi aggiuntivi, selezionare **Mostra più**. Ad esempio, selezionare **Mostra più** e quindi scegliere gli attributi e le attestazioni per **Paese/Area geografica**, **Nome visualizzato** e **Codice postale**. Selezionare **OK**.

   ![Pagina di creazione di un nuovo flusso utente](media/self-service-sign-up-user-flow/create-user-flow.png)

> [!NOTE]
> È possibile raccogliere gli attributi solo quando un utente si iscrive per la prima volta. Quando un utente si iscrive, non verrà più richiesto di raccogliere informazioni sugli attributi, anche se si modifica il flusso utente.

8. Selezionare **Crea**.
9. Il nuovo flusso utente viene visualizzato nell'elenco **flussi utente** . Se necessario, aggiornare la pagina.

## <a name="select-the-layout-of-the-attribute-collection-form"></a>Selezionare il layout del modulo di raccolta degli attributi

È possibile scegliere l'ordine in cui gli attributi vengono visualizzati nella pagina di iscrizione. 

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory**.
2. Selezionare **identità esterne**, selezionare **flussi utente**.
3. Selezionare il flusso utente di iscrizione self-service dall'elenco.
4. In **Personalizza**, selezionare **Layout di pagina**.
5. Vengono elencati gli attributi scelti per la raccolta. Per modificare l'ordine di visualizzazione, selezionare un attributo, quindi selezionare **Sposta su**, **Sposta giù**, **Sposta all'inizio** o **Sposta alla fine**.
6. Selezionare **Salva**.

## <a name="add-applications-to-the-self-service-sign-up-user-flow"></a>Aggiungere le applicazioni al flusso utente di iscrizione self-service

A questo punto è possibile associare le applicazioni al flusso utente.

1. Accedere al [portale di Azure](https://portal.azure.com) come amministratore di Azure AD.
2. In **Servizi di Azure** selezionare **Azure Active Directory**.
3. Nel menu a sinistra selezionare **Identità esterne**.
4. In **iscrizione self-service** selezionare **flussi utente**.
5. Selezionare il flusso utente di iscrizione self-service dall'elenco.
6. Nel menu a sinistra selezionare **Applicazioni** in **Usa**.
7. Selezionare **Aggiungi applicazione**.

   ![Assegnare un'applicazione al flusso utente](media/self-service-sign-up-user-flow/assign-app-to-user-flow.png)

8. Selezionare l'applicazione nell'elenco. In alternativa, usare la casella di ricerca per trovare l'applicazione, quindi selezionarla.
9. Fare clic su **Seleziona**.

## <a name="next-steps"></a>Passaggi successivi

- [Aggiungere Google all'elenco di provider di identità basati su social network](google-federation.md)
- [Aggiungere Facebook all'elenco di provider di identità basati su social network](facebook-federation.md)
- [Usare i connettori API per personalizzare ed estendere i flussi utente tramite API Web](api-connectors-overview.md)
- [Aggiungere un flusso di lavoro di approvazione personalizzato al flusso utente](self-service-sign-up-add-approvals.md)
