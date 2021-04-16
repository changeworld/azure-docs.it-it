---
title: Risolvere i problemi di reindirizzamento all'URL del servizio app
titleSuffix: Azure Application Gateway
description: Questo articolo fornisce informazioni su come risolvere il problema di reindirizzamento quando gateway applicazione di Azure viene usato con Servizio app di Azure
services: application-gateway
author: jaesoni
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/15/2021
ms.author: jaysoni
ms.openlocfilehash: 6aad1cf1269a7c3dc082482c39fdc4a079fc3240
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514887"
---
# <a name="troubleshoot-app-service-issues-in-application-gateway"></a>Risolvere i problemi del servizio app nel gateway applicazione

Informazioni su come diagnosticare e risolvere i problemi che possono verificarsi quando Servizio app di Azure viene usato come destinazione back-end con gateway applicazione di Azure.

## <a name="overview"></a>Panoramica

In questo articolo si apprenderà come risolvere i problemi seguenti:

* L'URL del servizio app viene esposto nel browser quando è presente un reindirizzamento.
* Il dominio del cookie ARRAffinity del servizio app è impostato sul nome host del servizio app, example.azurewebsites.net, anziché sull'host originale.

Quando un'applicazione back-end invia una risposta di reindirizzamento, potrebbe essere necessario reindirizzare il client a un URL diverso da quello specificato dall'applicazione back-end. È possibile eseguire questa operazione quando un servizio app è ospitato dietro un gateway applicazione e richiede al client di eseguire un reindirizzamento al percorso relativo. Un esempio è un reindirizzamento da contoso.azurewebsites.net/path1 a contoso.azurewebsites.net/path2. 

Quando il servizio app invia una risposta di reindirizzamento, usa lo stesso nome host nell'intestazione della posizione della risposta di quella nella richiesta ricevuta dal gateway applicazione. Ad esempio, il client effettua la richiesta direttamente contoso.azurewebsites.net/path2 invece di passare attraverso il gateway applicazione contoso.com/path2. Non si vuole ignorare il gateway applicazione.

Questo problema può verificarsi per i motivi principali seguenti:

- Il reindirizzamento è configurato nel servizio app. Il reindirizzamento può essere semplice come l'aggiunta di una barra finale alla richiesta.
- È stata Azure Active Directory autenticazione, che causa il reindirizzamento.

Inoltre, quando si usano i servizi app dietro un gateway applicazione, il nome di dominio associato al gateway applicazione (example.com) è diverso dal nome di dominio del servizio app (ad esempio, example.azurewebsites.net). Il valore di dominio per il cookie ARRAffinity impostato dal servizio app example.azurewebsites.net nome di dominio, che non è consigliabile. Il nome host originale, example.com, deve essere il valore del nome di dominio nel cookie.

## <a name="sample-configuration"></a>Configurazione di esempio

- Listener HTTP: Basic o multisosto
- Pool di indirizzi back-end: Servizio app
- Impostazioni HTTP: **selezionare il nome host dall'indirizzo back-end abilitato**
- Probe: **Selezionare il nome host dalle impostazioni HTTP abilitate**

## <a name="cause"></a>Causa

Il servizio app è un servizio multi-tenant, quindi usa l'intestazione host nella richiesta per instradare la richiesta all'endpoint corretto. Il nome di dominio predefinito di Servizi app, *.azurewebsites.net (ad esempio, contoso.azurewebsites.net), è diverso dal nome di dominio del gateway applicazione (ad esempio, contoso.com). 

La richiesta originale dal client ha il nome di dominio del gateway applicazione, contoso.com, come nome host. È necessario configurare il gateway applicazione per modificare il nome host nella richiesta originale con il nome host del servizio app quando instrada la richiesta al back-end del servizio app. Usare l'opzione **Seleziona nome host da indirizzo back-end** nella configurazione dell'impostazione HTTP del gateway applicazione. Usare l'opzione Pick Hostname from Backend HTTP Settings (Seleziona nome host da impostazioni **HTTP back-end)** nella configurazione del probe di integrità.



![Il gateway applicazione modifica il nome host](./media/troubleshoot-app-service-redirection-app-service-url/appservice-1.png)

Quando il servizio app esegue un reindirizzamento, usa il nome host sottoposto a override contoso.azurewebsites.net nell'intestazione del percorso anziché il nome host originale contoso.com, a meno che non sia configurato diversamente. Controllare le intestazioni di richiesta e risposta di esempio seguenti.
```
## Request headers to Application Gateway:

Request URL: http://www.contoso.com/path

Request Method: GET

Host: www.contoso.com

## Response headers:

Status Code: 301 Moved Permanently

Location: http://contoso.azurewebsites.net/path/

Server: Microsoft-IIS/10.0

Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=contoso.azurewebsites.net

X-Powered-By: ASP.NET
```
Nell'esempio precedente si noti che l'intestazione della risposta ha un codice di stato 301 per il reindirizzamento. L'intestazione del percorso ha il nome host del servizio app anziché il nome host originale `www.contoso.com` .

## <a name="solution-rewrite-the-location-header"></a>Soluzione: riscrivere l'intestazione del percorso

Impostare il nome host nell'intestazione del percorso sul nome di dominio del gateway applicazione. A tale scopo, creare una [regola di riscrittura](./rewrite-http-headers.md) con una condizione che valuta se l'intestazione della posizione nella risposta contiene azurewebsites.net. Deve anche eseguire un'azione per riscrivere l'intestazione del percorso in modo che abbia il nome host del gateway applicazione. Per altre informazioni, vedere le istruzioni su [come riscrivere l'intestazione location](./rewrite-http-headers.md#modify-a-redirection-url).

> [!NOTE]
> Il supporto per la riscrittura dell'intestazione HTTP è disponibile solo per Standard_v2 e [WAF_v2 SKU](./application-gateway-autoscaling-zone-redundant.md) del gateway applicazione. È [consigliabile eseguire la migrazione alla versione 2](./migrate-v1-v2.md) per la riscrittura dell'intestazione e [altre](./application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) funzionalità avanzate disponibili con lo SKU v2.

## <a name="alternate-solution-use-a-custom-domain-name"></a>Soluzione alternativa: usare un nome di dominio personalizzato

L'uso della funzionalità Custom Domain servizio app è un'altra soluzione per reindirizzare sempre il traffico al nome di dominio del gateway applicazione ( `www.contoso.com` in questo esempio). Questa configurazione funge anche da soluzione per il problema del cookie di affinità ARR. Per impostazione predefinita, il dominio del cookie ARRAffinity è impostato sul nome host predefinito del servizio app (example.azurewebsites.net) anziché sul nome di dominio del gateway applicazione. Di conseguenza, il browser in questi casi rifiuterà il cookie a causa della differenza nei nomi di dominio della richiesta e del cookie.

È possibile seguire il metodo specificato sia per i problemi di mancata corrispondenza del dominio cookie di Reindirizzamento che di ARRAffinity. Questo metodo dovrà disporre dell'accesso alla zona DNS del dominio personalizzato.

**Passaggio 1:** impostare un Custom Domain nel servizio app e verificare la proprietà del dominio aggiungendo il [record DNS CNAME & TXT](../app-service/app-service-web-tutorial-custom-domain.md#get-a-domain-verification-id).
I record hanno un aspetto simile a
-  `www.contoso.com` IN CNAME `contoso.azurewebsite.net`
-  `asuid.www.contoso.com` IN TXT " `<verification id string>` "


**Passaggio 2:** il record CNAME nel passaggio precedente era necessario solo per la verifica del dominio. In definitiva, è necessario che il traffico sia instradato tramite il gateway applicazione. È quindi possibile modificare il nome CNAME di 'ora in modo che punti al nome di `www.contoso.com` dominio completo del gateway applicazione. Per impostare un nome di dominio completo per il gateway applicazione, passare alla relativa risorsa Indirizzo IP pubblico e assegnare un'etichetta di nome DNS. Il record CNAME aggiornato dovrebbe ora essere simile a 
-  `www.contoso.com` IN CNAME `contoso.eastus.cloudapp.azure.com`


**Passaggio 3:** disabilitare "Seleziona nome host da indirizzo back-end" per l'impostazione HTTP associata.

In PowerShell non usare `-PickHostNameFromBackendAddress` l'opzione nel `Set-AzApplicationGatewayBackendHttpSettings` comando .


**Passaggio 4:** per determinare il back-end come integro e un traffico operativo, impostare un probe di integrità personalizzato con il campo Host come dominio personalizzato o predefinito del servizio app.

In PowerShell non usare l'opzione nel comando e usare il dominio personalizzato o predefinito del servizio app nell'opzione `-PickHostNameFromBackendHttpSettings` `Set-AzApplicationGatewayProbeConfig` -HostName del probe.

Per implementare i passaggi precedenti usando PowerShell per un'installazione esistente, usare lo script di PowerShell di esempio seguente. Si noti che non sono state usate le opzioni **-PickHostname** nella configurazione del probe e delle impostazioni HTTP.

```azurepowershell-interactive
$gw=Get-AzApplicationGateway -Name AppGw1 -ResourceGroupName AppGwRG
Set-AzApplicationGatewayProbeConfig -ApplicationGateway $gw -Name AppServiceProbe -Protocol Http -HostName "example.azurewebsites.net" -Path "/" -Interval 30 -Timeout 30 -UnhealthyThreshold 3
$probe=Get-AzApplicationGatewayProbeConfig -Name AppServiceProbe -ApplicationGateway $gw
Set-AzApplicationGatewayBackendHttpSettings -Name appgwhttpsettings -ApplicationGateway $gw -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 30
Set-AzApplicationGateway -ApplicationGateway $gw
```
  ```
  ## Request headers to Application Gateway:

  Request URL: http://www.contoso.com/path

  Request Method: GET

  Host: www.contoso.com

  ## Response headers:

  Status Code: 301 Moved Permanently

  Location: http://www.contoso.com/path/

  Server: Microsoft-IIS/10.0

  Set-Cookie: ARRAffinity=b5b1b14066f35b3e4533a1974cacfbbd969bf1960b6518aa2c2e2619700e4010;Path=/;HttpOnly;Domain=www.contoso.com

  X-Powered-By: ASP.NET
  ```
  ## <a name="next-steps"></a>Passaggi successivi

Se i passaggi precedenti non hanno risolto il problema, aprire un [ticket di supporto](https://azure.microsoft.com/support/options/).
