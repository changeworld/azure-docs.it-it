---
title: Autenticare un'applicazione per accedere al servizio Azure SignalR
description: Questo articolo fornisce informazioni sull'autenticazione di un'applicazione con Azure Active Directory per accedere al servizio Azure SignalR
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 597b69c1180ea1fb2a6812d648f8b8ad37707d66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092611"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>Autenticare un'applicazione con Azure Active Directory per accedere al servizio Azure SignalR
Microsoft Azure offre la gestione integrata del controllo di accesso per le risorse e le applicazioni basata su Azure Active Directory (Azure AD). Un vantaggio fondamentale dell'uso di Azure AD con il servizio Azure SignalR è che non è più necessario archiviare le credenziali nel codice. È invece possibile richiedere un token di accesso OAuth 2,0 dalla piattaforma di identità Microsoft. Il nome della risorsa per richiedere un token è `https://signalr.azure.com/` . Azure AD autentica l'entità di sicurezza (un'applicazione, un gruppo di risorse o un'entità servizio) che esegue l'applicazione. Se l'autenticazione ha esito positivo, Azure AD restituisce un token di accesso all'applicazione e l'applicazione può quindi usare il token di accesso per autorizzare la richiesta alle risorse del servizio Azure SignalR.

Quando un ruolo viene assegnato a un'entità di sicurezza Azure AD, Azure concede l'accesso a tali risorse per l'entità di sicurezza. L'ambito di accesso può essere limitato al livello di sottoscrizione, al gruppo di risorse o alla risorsa di Azure SignalR. Un Azure AD sicurezza può assegnare ruoli a un utente, a un gruppo, a un'entità servizio dell'applicazione o a un' [identità gestita per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Una definizione di ruolo è una raccolta di autorizzazioni, Il controllo degli accessi in base al ruolo controlla il modo in cui queste autorizzazioni vengono applicate tramite l'assegnazione di ruolo. Un'assegnazione di ruolo è costituita da tre elementi: entità di sicurezza, definizione del ruolo e ambito. Per ulteriori informazioni, vedere [Understanding the different Roles](../role-based-access-control/overview.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Registrare l'applicazione nel tenant di Azure AD
Il primo passaggio nell'uso di Azure AD per autorizzare le risorse del servizio Azure SignalR è la registrazione dell'applicazione con un tenant di Azure AD dal [portale di Azure](https://portal.azure.com/). Quando si registra l'applicazione, è necessario fornire informazioni sull'applicazione ad Active Directory. Azure AD fornisce quindi un ID client (noto anche come ID applicazione) che è possibile usare per associare l'applicazione a Azure AD Runtime. Per altre informazioni sull'ID client, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Nelle immagini seguenti vengono illustrati i passaggi per la registrazione di un'applicazione Web:

![Registrare un'applicazione](./media/authenticate/app-registrations-register.png)

> [!Note]
> Se si registra l'applicazione come applicazione nativa, è possibile specificare qualsiasi URI valido per l'URI di reindirizzamento. Per le applicazioni native, questo valore non deve essere un URL reale. Per le applicazioni Web, l'URI di reindirizzamento deve essere un URI valido perché specifica l'URL a cui vengono forniti i token.

Dopo aver registrato l'applicazione, l' **ID applicazione (client)** verrà visualizzato in **Impostazioni**:

![ID applicazione dell'applicazione registrata](./media/authenticate/application-id.png)

Per altre informazioni sulla registrazione di un'applicazione in Azure AD, vedere [Integrazione di applicazioni con Azure Active Directory](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Creare un segreto client   
L'applicazione richiede un segreto client per dimostrare la propria identità quando richiede un token. Per aggiungere il segreto client, attenersi alla seguente procedura.

1. Passare alla registrazione dell'app nel portale di Azure.
1. Selezionare l'impostazione **certificati & segreti** .
1. In **segreti client** selezionare **nuovo segreto client** per creare un nuovo segreto.
1. Specificare una descrizione per il segreto e scegliere l'intervallo di scadenza desiderato.
1. Copiare immediatamente il valore del nuovo segreto in una posizione sicura. Il valore di riempimento viene visualizzato una sola volta.

![Creazione di un segreto client](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>Caricamento di un certificato

È anche possibile caricare una certificazione anziché creare un segreto client.

![Caricare una certificazione](./media/authenticate/certification.png)

## <a name="assign-azure-roles-using-the-azure-portal"></a>Assegnare i ruoli di Azure usando il portale di Azure  
Per altre informazioni sulla gestione dell'accesso alle risorse di Azure con il controllo degli accessi in base al ruolo e la portale di Azure, vedere [questo articolo](..//role-based-access-control/role-assignments-portal.md). 

Dopo aver determinato l'ambito appropriato per un'assegnazione di ruolo, passare a tale risorsa nel portale di Azure. Visualizzare le impostazioni di controllo di accesso (IAM) per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

1. Nella [portale di Azure](https://portal.azure.com/)passare alla risorsa SignalR.
1. Selezionare **controllo di accesso (IAM)** per visualizzare le impostazioni di controllo di accesso per Azure SignalR. 
1. Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo. Selezionare il pulsante **Aggiungi** sulla barra degli strumenti e quindi selezionare **Aggiungi assegnazione ruolo**. 

    ![Pulsante Aggiungi sulla barra degli strumenti](./media/authenticate/role-assignments-add-button.png)

1. Nella pagina **Aggiungi assegnazione ruolo** eseguire le operazioni seguenti:
    1. Selezionare il **ruolo Azure SignalR** che si vuole assegnare. 
    1. Eseguire una ricerca per individuare l' **entità di sicurezza** (utente, gruppo, entità servizio) a cui si desidera assegnare il ruolo.
    1. Selezionare **Save (Salva** ) per salvare l'assegnazione di ruolo. 

        ![Assegnare un ruolo a un'applicazione](./media/authenticate/assign-role-to-application.png)

    1. L'identità cui è assegnato il ruolo viene visualizzata nell'elenco in corrispondenza del ruolo. Ad esempio, l'immagine seguente mostra che l'applicazione `signalr-dev` e `signalr-service` si trovano nel ruolo del server app SignalR. 
        
        ![Elenco di assegnazioni di ruolo](./media/authenticate/role-assignment-list.png)

È possibile seguire una procedura simile per assegnare un ruolo con ambito al gruppo di risorse o alla sottoscrizione. Dopo aver definito il ruolo e il relativo ambito, è possibile testare questo comportamento con gli esempi [in questo percorso di GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).

## <a name="sample-codes-while-configuring-your-app-server"></a>Codici di esempio durante la configurazione del server app.

Aggiungere le opzioni seguenti quando `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

In alternativa, è sufficiente configurare `ConnectionString` nel `appsettings.json` file.

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

Quando si usa `certificate` , modificare il `clientSecret` in in modo `clientCert` analogo al seguente:

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni su RBAC, vedere informazioni sul [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md).
- Per informazioni su come assegnare e gestire le assegnazioni di ruolo di Azure con Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST, vedere questi articoli:
    - [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gestire il controllo degli accessi in base al ruolo (RBAC) con modelli di Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Vedere gli articoli correlati seguenti:
- [Autenticare un'identità gestita con Azure Active Directory per accedere al servizio Azure SignalR](authenticate-managed-identity.md)
- [Autorizzare l'accesso al servizio Azure SignalR usando Azure Active Directory](authorize-access-azure-active-directory.md)