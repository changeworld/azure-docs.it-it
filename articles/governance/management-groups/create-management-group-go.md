---
title: 'Guida introduttiva: creare un gruppo di gestione con go'
description: In questa Guida introduttiva si userà Go per creare un gruppo di gestione per organizzare le risorse in una gerarchia di risorse.
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b6ca4d10f2a86ecb55fec2afe72b4aabfbb94f9
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101773"
---
# <a name="quickstart-create-a-management-group-with-go"></a>Guida introduttiva: creare un gruppo di gestione con go

I gruppi di gestione sono contenitori che semplificano la gestione dell'accesso, dei criteri e della conformità tra più sottoscrizioni. Creare questi contenitori per creare una gerarchia efficiente ed efficace utilizzabile con [Criteri di Azure](../policy/overview.md) e [Controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/overview.md). Per altre informazioni sui gruppi di gestione, vedere [Organizzare le risorse con i gruppi di gestione di Azure](overview.md).

La creazione del primo gruppo di gestione nella directory può richiedere fino a 15 minuti. La prima volta vengono eseguiti processi che eseguono la configurazione del servizio dei gruppi di gestione all'interno di Azure per la directory. Al termine dei processi, si riceve una notifica. Per altre informazioni, vedere [Configurazione iniziale dei gruppi di gestione](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

- Un'entità servizio di Azure, tra cui _clientId_ e _clientSecret_. Se non si hanno entità servizio da usare con Criteri di Azure o se ne vuole creare una nuova, vedere [Librerie di gestione di Azure per l'autenticazione .NET](/dotnet/azure/sdk/authentication#mgmt-auth).
  Ignorare il passaggio per installare i pacchetti di .NET Core perché questa operazione verrà descritta nei passaggi successivi.

- Se la [protezione della gerarchia](./how-to/protect-resource-hierarchy.md#setting---require-authorization) non è abilitata, qualsiasi utente di Azure AD nel tenant può creare un gruppo di gestione senza avere autorizzazioni di scrittura assegnate per tale gruppo di gestione. Questo nuovo gruppo di gestione diventa un elemento figlio del gruppo di gestione radice o del [gruppo di gestione predefinito](./how-to/protect-resource-hierarchy.md#setting---default-management-group) e all'autore viene assegnato il ruolo "Proprietario". Il servizio del gruppo di gestione offre questa possibilità per evitare che siano necessarie assegnazioni di ruolo a livello di radice. Nessun utente ha accesso al gruppo di gestione radice quando viene creato. Per evitare il problema di reperire gli amministratori globali di Azure AD per iniziare a usare i gruppi di gestione, è consentita la creazione dei gruppi di gestione iniziali al livello radice.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>Aggiungere il pacchetto del gruppo di gestione

Per abilitare Vai a Gestisci gruppi di gestione, è necessario aggiungere il pacchetto. Questo pacchetto funziona ovunque sia possibile usare Go, sia con [Bash in Windows 10](/windows/wsl/install-win10) o con installazione locale.

1. Verificare che sia installata la versione più recente di Go (almeno **1,15**). Se non è ancora installata, scaricarla dal sito [Golang.org](https://golang.org/dl/).

1. Controllare che sia installata l'interfaccia della riga di comando di Azure più recente o almeno la versione **2.5.1**. Se non è ancora installata, vedere [Installare l'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli).

   > [!NOTE]
   > L'interfaccia della riga di comando di Azure è necessaria per consentire a Go di usare il metodo `auth.NewAuthorizerFromCLI()` nell'esempio seguente. Per informazioni sulle altre opzioni, vedere [Azure SDK per Go - Altri dettagli sull'autenticazione](https://github.com/Azure/azure-sdk-for-go#more-authentication-details).

1. Eseguire l'autenticazione tramite l'interfaccia della riga di comando di Azure.

   ```azurecli
   az login
   ```

1. Nell'ambiente go scelto installare i pacchetti necessari per i gruppi di gestione:

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>Configurazione dell'applicazione

Con i pacchetti go aggiunti all'ambiente desiderato, è necessario configurare l'applicazione go in grado di creare un gruppo di gestione.

1. Creare l'applicazione Go e salvare l'origine seguente come `mgCreate.go`:

   ```Go
   package main
   
   import (
    "context"
    "fmt"
    "os"
   
    mg "github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]
   
    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }
   
    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }
   
    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. Compilare l'applicazione Go:

   ```bash
   go build mgCreate.go
   ```

1. Creare un gruppo di gestione usando l'applicazione go compilata. Sostituire `<Name>` con il nome del nuovo gruppo di gestione:

   ```bash
   mgCreate "<Name>"
   ```

Il risultato è un nuovo gruppo di gestione nel gruppo di gestione radice.

## <a name="clean-up-resources"></a>Pulire le risorse

Per rimuovere i pacchetti installati dall'ambiente Go, usare il comando seguente:

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stato creato un gruppo di gestione per organizzare la gerarchia delle risorse. Il gruppo di gestione può contenere sottoscrizioni o altri gruppi di gestione.

Per altre informazioni sui gruppi di gestione e su come gestire la gerarchia delle risorse, continuare con:

> [!div class="nextstepaction"]
> Gestire le risorse con i gruppi di gestione