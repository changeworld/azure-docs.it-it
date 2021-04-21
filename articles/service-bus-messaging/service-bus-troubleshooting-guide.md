---
title: Guida alla risoluzione dei problemi bus di servizio di Azure | Microsoft Docs
description: Informazioni su suggerimenti e consigli per la risoluzione dei problemi che possono verificarsi quando si usa bus di servizio di Azure.
ms.topic: article
ms.date: 03/03/2021
ms.openlocfilehash: 27249d7e016ea8aee0552bbbf1687647760d4b6f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786568"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Guida alla risoluzione dei problemi per bus di servizio di Azure
Questo articolo fornisce suggerimenti e consigli per la risoluzione dei problemi che possono verificarsi quando si usa bus di servizio di Azure. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problemi di connettività, certificato o timeout
La procedura seguente può essere utile per la risoluzione dei problemi di connettività/certificato/timeout per tutti i servizi in *.servicebus.windows.net. 

- Passare a o [wget](https://www.gnu.org/software/wget/) `https://<yournamespace>.servicebus.windows.net/` . Consente di verificare se si dispone di filtri IP o problemi di rete virtuale o catena di certificati, che sono comuni quando si usa Java SDK.

    Un esempio di messaggio di esito positivo:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Un esempio di messaggio di errore di errore:

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Eseguire il comando seguente per verificare se una porta è bloccata nel firewall. Le porte usate sono 443 (HTTPS), 5671 (AMQP) e 9354 (Net Messaging/SBMP). A seconda della libreria usata, vengono usate anche altre porte. Ecco il comando di esempio che controlla se la porta 5671 è bloccata. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    In Linux:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- In caso di problemi di connettività intermittenti, eseguire il comando seguente per verificare se sono presenti pacchetti eliminati. Questo comando tenterà di stabilire 25 connessioni TCP diverse ogni 1 secondo con il servizio. È quindi possibile controllare il numero di tentativi riusciti/non riusciti e vedere anche la latenza di connessione TCP. È possibile scaricare lo `psping` strumento da [qui.](/sysinternals/downloads/psping)

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    È possibile usare comandi equivalenti se si usano altri strumenti, ad esempio `tnc` `ping` , e così via. 
- Ottenere una traccia di rete se i passaggi precedenti non consentono di analizzarla usando strumenti come [Wireshark](https://www.wireshark.org/). Contattare [Supporto tecnico Microsoft](https://support.microsoft.com/) se necessario. 
- Per trovare gli indirizzi IP da aggiungere all'elenco elementi consentiti per le connessioni, vedere Quali indirizzi IP è necessario [aggiungere all'elenco elementi consentiti.](service-bus-faq.yml#what-ip-addresses-do-i-need-to-add-to-allow-list-) 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>Problemi che possono verificarsi con gli aggiornamenti/riavvii del servizio

### <a name="symptoms"></a>Sintomi
- Le richieste possono essere temporaneamente limitate.
- È possibile che si sia verificata una discesa di messaggi/richieste in ingresso.
- Il file di log può contenere messaggi di errore.
- Le applicazioni potrebbero essere disconnesse dal servizio per alcuni secondi.

### <a name="cause"></a>Causa
Gli aggiornamenti e i riavvii del servizio back-end possono causare questi problemi nelle applicazioni.

### <a name="resolution"></a>Soluzione
Se il codice dell'applicazione usa l'SDK, i criteri di ripetizione dei tentativi sono già predefiniti e attivi. L'applicazione si riconnetterà senza un impatto significativo sull'applicazione o sul flusso di lavoro.

## <a name="unauthorized-access-send-claims-are-required"></a>Accesso non autorizzato: l'invio di attestazioni è obbligatorio

### <a name="symptoms"></a>Sintomi 
Questo errore può essere visualizzato quando si tenta di accedere a un argomento del bus di servizio da Visual Studio in un computer locale usando un'identità gestita assegnata dall'utente con autorizzazioni di invio.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>Causa
L'identità non ha le autorizzazioni per accedere all'argomento del bus di servizio. 

### <a name="resolution"></a>Soluzione
Per risolvere questo errore, installare la [libreria Microsoft.Azure.Services.AppAuthentication.](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/)  Per altre informazioni, vedere [Autenticazione per lo sviluppo locale.](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication) 

Per informazioni su come assegnare autorizzazioni ai ruoli, vedere [Autenticare](service-bus-managed-service-identity.md)un'identità gestita con Azure Active Directory accedere bus di servizio di Azure risorse .

## <a name="service-bus-exception-put-token-failed"></a>Eccezione del bus di servizio: Il token put non è riuscito

### <a name="symptoms"></a>Sintomi
Quando si tenta di inviare più di 1000 messaggi usando la stessa connessione del bus di servizio, verrà visualizzato il messaggio di errore seguente: 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>Causa
È previsto un limite al numero di token usati per inviare e ricevere messaggi usando una singola connessione a uno spazio dei nomi del bus di servizio. È 1000. 

### <a name="resolution"></a>Soluzione
Aprire una nuova connessione allo spazio dei nomi del bus di servizio per inviare altri messaggi.

## <a name="adding-virtual-network-rule-using-powershell-fails"></a>L'aggiunta di una regola di rete virtuale tramite PowerShell ha esito negativo

### <a name="symptoms"></a>Sintomi
Sono state configurate due subnet da una singola rete virtuale in una regola di rete virtuale. Quando si tenta di rimuovere una subnet usando il cmdlet [Remove-AzServiceBusVirtualNetworkRule,](/powershell/module/az.servicebus/remove-azservicebusvirtualnetworkrule) la subnet non viene rimosso dalla regola di rete virtuale. 

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName $resourceGroupName -Namespace $serviceBusName -SubnetId $subnetId
```

### <a name="cause"></a>Causa
L Azure Resource Manager ID specificato per la subnet potrebbe non essere valido. Questo problema può verificarsi quando la rete virtuale si trova in un gruppo di risorse diverso da quello con lo spazio dei nomi del bus di servizio. Se non si specifica in modo esplicito il gruppo di risorse della rete virtuale, il comando dell'interfaccia della riga di comando crea l'ID Azure Resource Manager usando il gruppo di risorse dello spazio dei nomi del bus di servizio. Non riesce quindi a rimuovere la subnet dalla regola di rete. 

### <a name="resolution"></a>Soluzione
Specificare l'ID Azure Resource Manager completo della subnet che include il nome del gruppo di risorse con la rete virtuale. Ad esempio:

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName myRG -Namespace myNamespace -SubnetId "/subscriptions/SubscriptionId/resourcegroups/ResourceGroup/myOtherRG/providers/Microsoft.Network/virtualNetworks/myVNet/subnets/mySubnet"
```

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti: 

- [Azure Resource Manager eccezioni .](service-bus-resource-manager-exceptions.md) Elenca le eccezioni generate quando si interagisce con bus di servizio di Azure usando Azure Resource Manager (tramite modelli o chiamate dirette).
- [Eccezioni di messaggistica](service-bus-messaging-exceptions.md). Fornisce un elenco di eccezioni generate da .NET Framework per bus di servizio di Azure.