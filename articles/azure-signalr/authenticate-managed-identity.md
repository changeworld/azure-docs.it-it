---
title: Autenticazione di un'identità gestita con Azure Active Directory
description: Questo articolo fornisce informazioni sull'autenticazione di un'identità gestita con Azure Active Directory per accedere al servizio Azure SignalR
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: c561bb507a5178f4a838b370a3da8af9447829f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092559"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Autenticare un'identità gestita con Azure Active Directory per accedere alle risorse di Azure SignalR
Il servizio Azure SignalR supporta l'autenticazione Azure Active Directory (Azure AD) con [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md). Le identità gestite per le risorse di Azure possono autorizzare l'accesso alle risorse del servizio Azure SignalR usando Azure AD credenziali di applicazioni in esecuzione in macchine virtuali (VM) di Azure, app per le funzioni, set di scalabilità di macchine virtuali e altri servizi. Usando le identità gestite per le risorse di Azure con l'autenticazione di Azure AD, è possibile evitare di archiviare le credenziali con le applicazioni eseguite nel cloud.

Questo articolo illustra come autorizzare l'accesso a un servizio Azure SignalR usando un'identità gestita da una macchina virtuale di Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Abilitare le identità gestite su una macchina virtuale
Prima di poter usare le identità gestite per le risorse di Azure per autorizzare le risorse del servizio Azure SIgnalr dalla VM, è necessario innanzitutto abilitare le identità gestite per le risorse di Azure nella macchina virtuale. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Azure portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Librerie client di Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Concedere le autorizzazioni a un'identità gestita in Azure AD
Per autorizzare una richiesta al servizio Azure SignalR da un'identità gestita nell'applicazione, configurare prima le impostazioni di controllo degli accessi in base al ruolo (RBAC) per tale identità gestita. Il servizio Azure SignalR definisce i ruoli RBAC che includono le autorizzazioni per l'acquisizione di `AccessKey` o `ClientToken` . Quando il ruolo RBAC viene assegnato a un'identità gestita, all'identità gestita viene concesso l'accesso al servizio Azure SignalR nell'ambito appropriato.

Per altre informazioni sull'assegnazione di ruoli RBAC, vedere [eseguire l'autenticazione con Azure Active Directory per l'accesso alle risorse del servizio Azure SignalR](authorize-access-azure-active-directory.md).

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>Connettersi al servizio Azure SignalR con identità gestite
Per connettersi al servizio Azure SignalR con le identità gestite, è necessario assegnare all'identità il ruolo e l'ambito appropriato. La procedura descritta in questa sezione usa un'applicazione semplice che viene eseguita con un'identità gestita e accede alle risorse del servizio Azure SignalR.

Qui viene usata una risorsa di macchina virtuale di Azure di esempio.

1. Passare a **Impostazioni** e selezionare **Identity (identità**). 
1. Selezionare lo **stato** **su on**. 
1. Selezionare **Salva** per salvare l'impostazione. 

    ![Identità gestita per una macchina virtuale](./media/authenticate/identity-virtual-machine.png)

Una volta abilitata questa impostazione, viene creata una nuova identità del servizio nel Azure Active Directory (Azure AD) e configurata nell'host del servizio app.

A questo punto, assegnare l'identità del servizio a un ruolo nell'ambito necessario nelle risorse del servizio Azure SignalR.

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

## <a name="samples-code-while-configuring-your-app-server"></a>Esempi di codice durante la configurazione del server app

Aggiungere le opzioni seguenti quando `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni su RBAC, vedere informazioni sul [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../role-based-access-control/overview.md).
- Per informazioni su come assegnare e gestire le assegnazioni di ruolo di Azure con Azure PowerShell, l'interfaccia della riga di comando di Azure o l'API REST, vedere questi articoli:
    - [Gestire il controllo degli accessi in base al ruolo con Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure](../role-based-access-control/role-assignments-cli.md)
    - [Gestire il controllo degli accessi in base al ruolo con l'API REST](../role-based-access-control/role-assignments-rest.md)
    - [Gestire il controllo degli accessi in base al ruolo (RBAC) con modelli di Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Vedere gli articoli correlati seguenti:
- [Autenticare un'applicazione con Azure Active Directory per accedere al servizio Azure SignalR](authenticate-application.md)
- [Autorizzare l'accesso al servizio Azure SignalR usando Azure Active Directory](authorize-access-azure-active-directory.md)