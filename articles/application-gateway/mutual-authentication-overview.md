---
title: Panoramica dell'autenticazione reciproca sul gateway applicazione Azure
description: Questo articolo è una panoramica dell'autenticazione reciproca nel gateway applicazione.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: caya
ms.openlocfilehash: a63697c2c9c32dfb48e77248a5e7a601677b8b3e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231529"
---
# <a name="overview-of-mutual-authentication-with-application-gateway-preview"></a>Panoramica dell'autenticazione reciproca con il gateway applicazione (anteprima)

L'autenticazione reciproca, o l'autenticazione client, consente al gateway applicazione di autenticare le richieste di invio del client. In genere solo il client sta autenticando il gateway applicazione; l'autenticazione reciproca consente sia il client che il gateway applicazione possono autenticarsi a vicenda. 

> [!NOTE]
> Si consiglia di usare TLS 1,2 con l'autenticazione reciproca perché TLS 1,2 verrà richiesto in futuro. 

## <a name="mutual-authentication"></a>Autenticazione reciproca

Il gateway applicazione supporta l'autenticazione reciproca basata su certificati in cui è possibile caricare un certificato della CA client attendibile nel gateway applicazione e il gateway userà tale certificato per autenticare il client che invia una richiesta al gateway. Grazie alla crescita dei casi d'uso e ai requisiti di sicurezza più elevati nei settori, l'autenticazione reciproca consente di gestire e controllare quali client possono comunicare con il gateway applicazione. 

Per configurare l'autenticazione reciproca, è necessario caricare un certificato della CA client attendibile come parte della parte di autenticazione client di un profilo SSL. Il profilo SSL dovrà quindi essere associato a un listener per completare la configurazione dell'autenticazione reciproca. Nel certificato client caricato deve essere sempre presente un certificato CA radice. È anche possibile caricare una catena di certificati, ma la catena deve includere un certificato CA radice oltre al numero di certificati CA intermedi desiderati. 

Se, ad esempio, il certificato client contiene un certificato CA radice, più certificati della CA intermedia e un certificato foglia, verificare che il certificato CA radice e tutti i certificati della CA intermedia siano caricati nel gateway applicazione in un unico file. Per ulteriori informazioni su come estrarre un certificato CA client attendibile, vedere [come estrarre certificati CA client attendibili](./mutual-authentication-certificate-management.md).

Se si sta caricando una catena di certificati con i certificati CA radice e CA intermedia, la catena di certificati deve essere caricata come file PEM o CER nel gateway. 

> [!NOTE] 
> L'autenticazione reciproca è disponibile solo in Standard_v2 e WAF_v2 SKU. 

### <a name="certificates-supported-for-mutual-authentication"></a>Certificati supportati per l'autenticazione reciproca

Il gateway applicazione supporta i tipi di certificati seguenti:

- Certificato dell'autorità di certificazione (CA): un certificato CA è un certificato digitale emesso da un'autorità di certificazione (CA)
- Certificati CA autofirmati: i browser client non considerano attendibili questi certificati e avvisano l'utente che il certificato del servizio virtuale non fa parte di una catena di trust. I certificati CA autofirmati sono utili per i test o gli ambienti in cui gli amministratori controllano i client e possono ignorare in modo sicuro gli avvisi di sicurezza del browser. I carichi di lavoro di produzione non devono mai usare certificati CA autofirmati.

Per altre informazioni su come configurare l'autenticazione reciproca, vedere [configurare l'autenticazione reciproca con il gateway applicazione](./mutual-authentication-portal.md).

> [!IMPORTANT]
> Quando si usa l'autenticazione reciproca, assicurarsi di caricare l'intera catena di certificati CA client attendibili nel gateway applicazione. 

## <a name="additional-client-authentication-validation"></a>Convalida dell'autenticazione client aggiuntiva

### <a name="verify-client-certificate-dn"></a>Verificare il DN del certificato client

È possibile verificare l'emittente immediata del certificato client e consentire al gateway applicazione di considerare attendibile l'autorità emittente. Questa opzione è disattivata per impostazione predefinita, ma è possibile abilitarla tramite il portale, PowerShell o l'interfaccia della riga di comando di Azure. 

Se si sceglie di abilitare il gateway applicazione per verificare l'emittente immediata del certificato client, di seguito viene illustrato come determinare quale verrà estratto il DN dell'autorità emittente del certificato client dai certificati caricati. 
* **Scenario 1:** La catena di certificati include: certificato radice-certificato intermedio-certificato foglia 
    * Il nome soggetto del *certificato intermedio* è il gateway applicazione che verrà estratto come DN dell'autorità emittente del certificato client e verrà verificato in base a. 
* **Scenario 2:** La catena di certificati include: radice certificate-intermediate1 certificate-intermediate2 certificate-foglia certificate
    * Il nome del soggetto del *certificato Intermediate2* verrà estratto come DN dell'autorità emittente del certificato client e verrà verificato rispetto a. 
* **Scenario 3:** La catena di certificati include: certificato radice-certificato foglia 
    * Il nome *del soggetto del certificato radice* verrà estratto e utilizzato come DN dell'autorità emittente del certificato client.
* **Scenario 4:** Più catene di certificati con la stessa lunghezza nello stesso file. La catena 1 include: certificato radice-certificato intermediate1-certificato foglia. Chain 2 include: certificato radice-certificato intermediate2-certificato foglia. 
    * Il nome *del soggetto del certificato Intermediate1* verrà estratto come DN dell'autorità emittente del certificato client.  
* **Scenario 5:** Più catene di certificati di lunghezze diverse nello stesso file. La catena 1 include: certificato radice-certificato intermediate1-certificato foglia. La catena 2 include il certificato radice-certificato intermediate2-certificato intermediate3-certificato foglia. 
    * Il nome *del soggetto del certificato Intermediate3* verrà estratto come DN dell'autorità emittente del certificato client. 

> [!IMPORTANT]
> Si consiglia di caricare solo una catena di certificati per ogni file. Questa operazione è particolarmente importante se si Abilita la verifica del DN del certificato client. Caricando più catene di certificati in un file, si finirà nello scenario quattro o cinque e si potrebbero riscontrare problemi in un secondo momento, quando il certificato client presentato non corrisponde al gateway applicazione DN dell'autorità emittente del certificato client Estratto dalle catene. 

Per ulteriori informazioni su come estrarre le catene di certificati CA client attendibili, vedere [come estrarre catene di certificati CA client attendibili](./mutual-authentication-certificate-management.md).

## <a name="server-variables"></a>Variabili del server 

Con l'autenticazione reciproca, sono disponibili altre variabili server che è possibile usare per passare informazioni sul certificato client ai server back-end dietro il gateway applicazione. Per altre informazioni sulle variabili server disponibili e su come usarle, vedere [variabili server](./rewrite-http-headers-url.md#mutual-authentication-server-variables-preview).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver appreso l'autenticazione reciproca, vedere [configurare il gateway applicazione con autenticazione reciproca in PowerShell](./mutual-authentication-powershell.md) per creare un gateway applicazione usando l'autenticazione reciproca. 

