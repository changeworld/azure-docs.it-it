---
title: Identità gestite per le risorse di Azure con il bus di servizio
description: Questo articolo descrive come usare le identità gestite per accedere con bus di servizio di Azure entità (code, argomenti e sottoscrizioni).
ms.topic: article
ms.date: 01/21/2021
ms.openlocfilehash: cac254ef6b57f1878620b1e3ca30e757d7f39a88
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529467"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-service-bus-resources"></a>Autenticare un'identità gestita con Azure Active Directory accedere bus di servizio di Azure risorse
Le [identità gestite per le risorse di Azure](../active-directory/managed-identities-azure-resources/overview.md) offrono una funzionalità per l'intera piattaforma Azure che consente di creare un'identità sicura associata alla distribuzione in cui viene eseguito il codice dell'applicazione. È quindi possibile associare l'identità ai ruoli di controllo di accesso che concedono autorizzazioni personalizzate per l'accesso a risorse di Azure specifiche necessarie per l'applicazione.

Con le identità gestite, la piattaforma Azure gestisce questa identità di runtime. Non è necessario archiviare e proteggere le chiavi di accesso nel codice o nella configurazione dell'applicazione, né per l'identità stessa, né per le risorse a cui è necessario accedere. Un'app client del bus di servizio in esecuzione in un'applicazione di Servizio app di Azure o in una macchina virtuale con il supporto per le identità gestite per le risorse di Azure abilitato non deve quindi gestire le chiavi e le regole di firma di accesso condiviso o altri token di accesso. L'applicazione client necessita solo dell'indirizzo dell'endpoint dello spazio dei nomi della messaggistica del bus di servizio. Quando l'app si connette, il bus di servizio associa il contesto dell'entità gestita al client in un'operazione che viene illustrata in un esempio riportato più avanti in questo articolo. Una volta eseguita l'associazione a un'identità gestita, il client del bus di servizio può eseguire tutte le operazioni autorizzate. L'autorizzazione viene concessa associando un'entità gestita ai ruoli del bus di servizio. 

## <a name="overview"></a>Panoramica
Quando un'entità di sicurezza (un utente, un gruppo o un'applicazione) tenta di accedere a un'entità del bus di servizio, la richiesta deve essere autorizzata. Con Azure AD, l'accesso a una risorsa è un processo in due passaggi. 

 1. Innanzitutto, viene autenticata l'identità dell'entità di sicurezza e viene restituito un token OAuth 2.0. Il nome della risorsa per richiedere un token è `https://servicebus.azure.net` .
 1. Successivamente, il token viene passato come parte di una richiesta al servizio del bus di servizio per autorizzare l'accesso alla risorsa specificata.

Il passaggio di autenticazione richiede che una richiesta dell'applicazione contenga un token di accesso OAuth 2.0 in fase di esecuzione. Se un'applicazione è in esecuzione all'interno di un'entità di Azure, ad esempio una macchina virtuale di Azure, un set di scalabilità di macchine virtuali o un'app per le funzioni di Azure, può usare un'identità gestita per accedere alle risorse. 

Il passaggio di autorizzazione richiede l'assegnazione di uno o più ruoli di Azure all'entità di sicurezza. bus di servizio di Azure fornisce ruoli di Azure che includono set di autorizzazioni per le risorse del bus di servizio. I ruoli assegnati a un'entità di sicurezza determinano le autorizzazioni che l'entità avrà. Per altre informazioni sull'assegnazione di ruoli di Azure bus di servizio di Azure, vedere Ruoli predefiniti [di Azure per](#azure-built-in-roles-for-azure-service-bus)bus di servizio di Azure . 

Le applicazioni native e le applicazioni Web che effettuano richieste al bus di servizio possono anche autorizzare con Azure AD. Questo articolo illustra come richiedere un token di accesso e usarlo per autorizzare le richieste per le risorse del bus di servizio. 


## <a name="assigning-azure-roles-for-access-rights"></a>Assegnazione di ruoli di Azure per i diritti di accesso
Azure Active Directory (Azure AD) autorizza i diritti di accesso alle risorse protette tramite il controllo degli accessi in base al [ruolo di Azure.](../role-based-access-control/overview.md) bus di servizio di Azure definisce un set di ruoli predefiniti di Azure che includono set comuni di autorizzazioni usate per accedere alle entità del bus di servizio ed è anche possibile definire ruoli personalizzati per l'accesso ai dati.

Quando un ruolo di Azure viene assegnato a un'Azure AD di sicurezza, Azure concede l'accesso a tali risorse per tale entità di sicurezza. L'ambito dell'accesso può essere il livello di sottoscrizione, il gruppo di risorse o lo spazio dei nomi del bus di servizio. Un'Azure AD di sicurezza può essere un utente, un gruppo, un'entità servizio dell'applicazione o un'identità gestita per le risorse di Azure.

## <a name="azure-built-in-roles-for-azure-service-bus"></a>Ruoli predefiniti di Azure per bus di servizio di Azure
Ad bus di servizio di Azure, la gestione degli spazi dei nomi e di tutte le risorse correlate tramite il portale di Azure e l'API di gestione delle risorse di Azure è già protetta usando il modello controllo degli accessi in base al ruolo di Azure. Azure offre i ruoli predefiniti di Azure seguenti per autorizzare l'accesso a uno spazio dei nomi del bus di servizio:

- [bus di servizio di Azure Proprietario dati:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-owner)consente l'accesso ai dati allo spazio dei nomi del bus di servizio e alle relative entità (code, argomenti, sottoscrizioni e filtri)
- [bus di servizio di Azure mittente dei dati:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-sender)usare questo ruolo per concedere l'accesso invii allo spazio dei nomi del bus di servizio e alle relative entità.
- [bus di servizio di Azure ricevitore di dati:](../role-based-access-control/built-in-roles.md#azure-service-bus-data-receiver)usare questo ruolo per concedere l'accesso allo spazio dei nomi del bus di servizio e alle relative entità. 

## <a name="resource-scope"></a>Ambito risorsa 
Prima di assegnare un ruolo Controllo degli accessi in base al ruolo a un'entità di sicurezza, determinare l'ambito di accesso che dovrà avere. In base alle procedure consigliate, è sempre preferibile concedere solo l'ambito più restrittivo possibile.

L'elenco seguente descrive i livelli a cui è possibile limitare l'accesso alle risorse del bus di servizio, a partire dall'ambito più ristretto:

- **Coda,** **argomento** o **sottoscrizione:** l'assegnazione di ruolo si applica all'entità del bus di servizio specifica. Attualmente, il portale di Azure non supporta l'assegnazione di utenti/gruppi/identità gestite ai ruoli di Azure del bus di servizio a livello di sottoscrizione. Ecco un esempio di uso del comando dell'interfaccia della riga di comando di Azure: [az-role-assignment-create](/cli/azure/role/assignment?#az-role-assignment-create) per assegnare un'identità a un ruolo di Azure del bus di servizio: 

    ```azurecli
    az role assignment create \
        --role $service_bus_role \
        --assignee $assignee_id \
        --scope /subscriptions/$subscription_id/resourceGroups/$resource_group/providers/Microsoft.ServiceBus/namespaces/$service_bus_namespace/topics/$service_bus_topic/subscriptions/$service_bus_subscription
    ```
- **Spazio dei nomi del bus** di servizio: l'assegnazione di ruolo si estende all'intera topologia del bus di servizio nello spazio dei nomi e al gruppo di consumer associato.
- **Gruppo di risorse:** l'assegnazione di ruolo si applica a tutte le risorse del bus di servizio nel gruppo di risorse.
- **Sottoscrizione:** l'assegnazione di ruolo si applica a tutte le risorse del bus di servizio in tutti i gruppi di risorse nella sottoscrizione.

> [!NOTE]
> Tenere presente che la propagazione delle assegnazioni di ruolo di Azure può richiedere fino a cinque minuti. 

Per altre informazioni sulla definizione dei ruoli predefiniti, vedere [Informazioni sulle definizioni dei ruoli](../role-based-access-control/role-definitions.md#management-and-data-operations). Per informazioni sulla creazione di ruoli personalizzati di Azure, vedere [Ruoli personalizzati di Azure.](../role-based-access-control/custom-roles.md)

## <a name="enable-managed-identities-on-a-vm"></a>Abilitare le identità gestite su una macchina virtuale
Prima di poter usare le identità gestite per le risorse di Azure per autorizzare le risorse del bus di servizio dalla macchina virtuale, è necessario abilitare le identità gestite per le risorse di Azure nella macchina virtuale. Per informazioni su come abilitare identità gestite per le risorse di Azure, vedere uno di questi articoli:

- [Azure portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfaccia della riga di comando di Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Modello di Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager librerie client](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Concedere autorizzazioni a un'identità gestita in Azure AD
Per autorizzare una richiesta al servizio del bus di servizio da un'identità gestita nell'applicazione, configurare prima di tutto le impostazioni del controllo degli accessi in base al ruolo di Azure per tale identità gestita. bus di servizio di Azure definisce i ruoli di Azure che includono le autorizzazioni per l'invio e la lettura dal bus di servizio. Quando il ruolo di Azure viene assegnato a un'identità gestita, all'identità gestita viene concesso l'accesso alle entità del bus di servizio nell'ambito appropriato.

Per altre informazioni sull'assegnazione di ruoli di Azure, vedere Autenticare e autorizzare con Azure Active Directory [per l'accesso alle risorse del bus di servizio.](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)

## <a name="use-service-bus-with-managed-identities-for-azure-resources"></a>Usare il bus di servizio con le identità gestite per le risorse di Azure
Per usare il bus di servizio con identità gestite, è necessario assegnare all'identità il ruolo e l'ambito appropriato. La procedura descritta in questa sezione usa una semplice applicazione che viene eseguita con un'identità gestita e accede alle risorse del bus di servizio.

In questo caso si usa un'applicazione Web di esempio ospitata in [Servizio app di Azure](https://azure.microsoft.com/services/app-service/). Per istruzioni dettagliate sulla creazione di un'applicazione Web, vedere Creare un'app Web [ASP.NET Core in Azure](../app-service/quickstart-dotnetcore.md)

Dopo aver creato l'applicazione, seguire questa procedura: 

1. Passare a **Impostazioni** e selezionare **Identità.** 
1. Selezionare lo **stato** su **On**. 
1. Selezionare **Salva** per salvare l'impostazione. 

    ![Identità gestita per un'app Web](./media/service-bus-managed-service-identity/identity-web-app.png)

Dopo aver abilitato questa impostazione, viene creata una nuova identità del servizio nel Azure Active Directory (Azure AD) e configurata nell'host del servizio app.

> [!NOTE]
> Quando si usa un'identità gestita, la stringa di connessione deve essere nel formato: `Endpoint=sb://<NAMESPACE NAME>.servicebus.windows.net/;Authentication=ManagedIdentity` .

Assegnare ora questa identità del servizio a un ruolo nell'ambito richiesto nelle risorse del bus di servizio.

### <a name="to-assign-azure-roles-using-the-azure-portal"></a>Per assegnare ruoli di Azure usando il portale di Azure
Per assegnare un ruolo a uno spazio dei nomi del bus di servizio, passare allo spazio dei nomi nel portale di Azure. Visualizzare le impostazioni di Controllo di accesso (IAM) per la risorsa e seguire queste istruzioni per gestire le assegnazioni di ruolo:

> [!NOTE]
> La procedura seguente assegna un ruolo di identità del servizio agli spazi dei nomi del bus di servizio. È possibile seguire gli stessi passaggi per assegnare un ruolo in altri ambiti supportati (gruppo di risorse e sottoscrizione). 
> 
> [Creare uno spazio dei nomi di messaggistica](service-bus-create-namespace-portal.md) del bus di servizio, se non se ne ha uno. 

1. Nel portale di Azure passare allo spazio dei nomi del bus di servizio e visualizzare **panoramica** per lo spazio dei nomi. 
1. Selezionare **Controllo di accesso (IAM) nel** menu a sinistra per visualizzare le impostazioni di controllo di accesso per lo spazio dei nomi del bus di servizio.
1.  Selezionare la scheda **Assegnazioni di ruolo** per visualizzare l'elenco di assegnazioni di ruolo.
3.  Selezionare **Aggiungi** e quindi Aggiungi assegnazione **di ruolo**.
4.  Nella pagina **Aggiungi assegnazione di ruolo** seguire questa procedura:
    1. Per **Ruolo** selezionare il ruolo del bus di servizio che si vuole assegnare. In questo esempio è bus di servizio di Azure **proprietario dei dati.**
    1. Per il **campo Assegna accesso a** selezionare Servizio **app** in Identità gestita assegnata **dal sistema.** 
    1. Selezionare la **sottoscrizione** in cui è stata creata l'identità gestita per l'app Web.
    1. Selezionare **l'identità gestita** per l'app Web creata. Il nome predefinito per l'identità corrisponde al nome dell'app Web. 
    1. Selezionare quindi **Salva**.
        
        ![Pagina Aggiungi assegnazione di ruolo](./media/service-bus-managed-service-identity/add-role-assignment-page.png)

    Dopo aver assegnato il ruolo, l'applicazione Web avrà accesso alle entità del bus di servizio nell'ambito definito. 

    > [!NOTE]
    > Per un elenco dei servizi che supportano le identità gestite, vedere Servizi che supportano le [identità gestite per le risorse di Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

### <a name="run-the-app"></a>Eseguire l'app
Modificare ora la pagina predefinita dell'applicazione ASP.NET creata. È possibile usare il codice dell'applicazione Web di [questo repository GitHub](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet).  

La pagina Default.aspx è la pagina di destinazione. Il codice è disponibile nel file Default.aspx.cs. Il risultato è un'applicazione Web minima con pochi campi di immissione e con pulsanti di **invio** e **ricezione** che consentono la connessione al bus di servizio per l'invio o la ricezione di messaggi.

Si noti come viene inizializzato l'oggetto [MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory). Invece di usare il provider di token di firma di accesso condiviso (SAS), il codice crea un provider di token per l'identità gestita con la chiamata a `var msiTokenProvider = TokenProvider.CreateManagedIdentityTokenProvider();`. In questo modo, non ci sono segreti da conservare e usare. Il flusso del contesto dell'identità gestita nel bus di servizio e l'handshake di autorizzazione vengono gestiti automaticamente dal provider di token. Questo modello è più semplice rispetto all'uso della firma di accesso condiviso.

Dopo avere apportato queste modifiche, pubblicare ed eseguire l'applicazione. È possibile ottenere con facilità i dati di pubblicazione corretti scaricando e quindi importando un profilo di pubblicazione in Visual Studio:

![Ottenere il profilo di pubblicazione](./media/service-bus-managed-service-identity/msi3.png)
 
Per inviare o ricevere messaggi, immettere il nome dello spazio dei nomi e il nome dell'entità creata. Fare quindi clic su **Invia** o **Ricevi**.


> [!NOTE]
> - L'identità gestita funziona solo all'interno dell'ambiente di Azure, nei servizi app, nelle macchine virtuali di Azure e nei set di scalabilità. Per le applicazioni .NET, la libreria Microsoft.Azure.Services.AppAuthentication, usata dal pacchetto NuGet del bus di servizio, rappresenta un'astrazione di questo protocollo e supporta un'esperienza di sviluppo locale. Questa libreria consente anche di testare il codice in locale nel computer di sviluppo usando l'account utente da Visual Studio, dall'interfaccia della riga di comando di Azure 2.0 o tramite l'autenticazione integrata di Active Directory. Per altre informazioni sulle opzioni di sviluppo locale con questa libreria, vedere [Autenticazione da servizio a servizio ad Azure Key Vault usando .NET](/dotnet/api/overview/azure/service-to-service-authentication).  

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti:

* [Code, argomenti e sottoscrizioni del bus di servizio](service-bus-queues-topics-subscriptions.md)
* [Introduzione alle code del bus di servizio](service-bus-dotnet-get-started-with-queues.md)
* [Come usare gli argomenti e le sottoscrizioni del bus di servizio](service-bus-dotnet-how-to-use-topics-subscriptions.md)
