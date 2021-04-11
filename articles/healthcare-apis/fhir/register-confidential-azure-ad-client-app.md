---
title: Registrare un'app client riservata nell'API Azure AD-Azure per FHIR
description: Registrare un'applicazione client riservata in Azure Active Directory che esegue l'autenticazione per conto di un utente e richiede l'accesso alle applicazioni delle risorse.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: matjazl
ms.openlocfilehash: c10b27d375e2bfb8c64130eceb416a633241cf68
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284435"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrare un'applicazione client riservata in Azure Active Directory

In questa esercitazione si apprenderà come registrare un'applicazione client riservata in Azure Active Directory (Azure AD).  

La registrazione di un'applicazione client è una rappresentazione Azure AD di un'applicazione che può essere usata per eseguire l'autenticazione per conto di un utente e richiedere l'accesso alle [applicazioni delle risorse](register-resource-azure-ad-client-app.md). Un'applicazione client riservata è un'applicazione che può essere considerata attendibile per contenere un segreto e presentare il segreto quando si richiedono token di accesso. Esempi di applicazioni riservate sono applicazioni lato server. 

Per registrare una nuova applicazione client riservata, fare riferimento alla procedura riportata di seguito. 

## <a name="register-a-new-application"></a>Registrare una nuova applicazione

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Azure Active Directory**.

1. Selezionare **Registrazioni per l'app**. 

    :::image type="content" source="media/how-to-aad/portal-aad-new-app-registration.png" alt-text="portale di Azure. Nuova registrazione dell'app.":::

1. Selezionare **Nuova registrazione**.

1. Assegnare all'applicazione un nome visualizzato per l'utente.

1. Per i **tipi di account supportati**, selezionare gli utenti che possono usare l'applicazione o accedere all'API.

1. Opzionale Specificare un **URI di reindirizzamento**. Questi dettagli possono essere modificati in un secondo momento, ma se si conosce l'URL di risposta dell'applicazione, immetterlo ora.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png" alt-text="Nuova registrazione di app client riservate.":::

1. Selezionare **Registra**.

## <a name="api-permissions"></a>Autorizzazioni delle API

Ora che l'applicazione è stata registrata, è necessario selezionare le autorizzazioni API richieste dall'applicazione per conto degli utenti.

1. Selezionare **Autorizzazioni API**.

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png" alt-text="Client riservato. Autorizzazioni API.":::

1. Selezionare **Aggiungi un'autorizzazione**.

    Se si usa l'API di Azure per FHIR, si aggiungerà un'autorizzazione per le API di Azure Healthcare cercando l' **API Healthcare di Azure** in API usate dall' **organizzazione**. Il risultato della ricerca dell'API di Azure Healthcare restituirà solo se è già stata [distribuita l'API di Azure per FHIR](fhir-paas-powershell-quickstart.md).

    Se si fa riferimento a un'applicazione di risorse diversa, selezionare la [registrazione dell'applicazione della risorsa API FHIR](register-resource-azure-ad-client-app.md) creata in precedenza in **API personali**.


    :::image type="content" source="media/conf-client-app/confidential-client-org-api.png" alt-text="Client riservato. API dell'organizzazione" lightbox="media/conf-client-app/confidential-app-org-api-expanded.png":::
    

1. Selezionare gli ambiti (autorizzazioni) che l'applicazione client riservata richiederà per conto di un utente. Selezionare **user_impersonation**, quindi selezionare **Aggiungi autorizzazioni**.

    :::image type="content" source="media/conf-client-app/confidential-client-add-permission.png" alt-text="Client riservato. Autorizzazioni delegate":::


## <a name="application-secret"></a>Segreto dell'applicazione

1. Selezionare **certificati & segreti**, quindi selezionare **nuovo segreto client**. 

    :::image type="content" source="media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png" alt-text="Client riservato. Segreto dell'applicazione.":::

1. Immettere una **Descrizione** per il segreto client. Selezionare il menu a discesa **scadenza** per scegliere un intervallo di tempo di scadenza, quindi fare clic su **Aggiungi**.

   :::image type="content" source="media/how-to-aad/add-a-client-secret.png" alt-text="Aggiungere un segreto client.":::

1. Dopo aver creato la stringa privata del client, copiarne il **valore** e l' **ID** e archiviarli in un percorso sicuro a scelta.

   :::image type="content" source="media/how-to-aad/client-secret-string-password.png" alt-text="Stringa del segreto client."::: 

> [!NOTE]
>La stringa del segreto client è visibile solo una volta nel portale di Azure. Quando si esce dalla pagina Web dei certificati & Secrets e quindi si torna alla pagina, la stringa di valore diventa mascherata. È importante creare una copia della stringa privata del client immediatamente dopo la generazione. Se non si dispone di una copia di backup del segreto client, è necessario ripetere i passaggi precedenti per rigenerarla.
 
## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati illustrati i passaggi per la registrazione di un'applicazione client riservata nel Azure AD. Sono stati inoltre illustrati i passaggi per aggiungere le autorizzazioni API all'API di Azure Healthcare. Infine, è stato illustrato come creare un segreto dell'applicazione. Inoltre, è possibile ottenere informazioni su come accedere al server FHIR tramite il post.
 
>[!div class="nextstepaction"]
>[Accedere all'API di Azure per FHIR con Postman](access-fhir-postman-tutorial.md)
