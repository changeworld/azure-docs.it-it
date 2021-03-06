---
title: Scalabilità con distribuzione geografica
description: Informazioni sulla scalabilità orizzontale delle app mediante la distribuzione a livello geografico con Gestione traffico e ambienti del servizio app
author: stefsch
ms.assetid: c1b05ca8-3703-4d87-a9ae-819d741787fb
ms.topic: article
ms.date: 09/07/2016
ms.author: stefsch
ms.custom: seodec18, references_regions, devx-track-azurepowershell
ms.openlocfilehash: 215132888749a54996b3341e43ef8d91c101a460
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834301"
---
# <a name="geo-distributed-scale-with-app-service-environments"></a>Scalabilità distribuita a livello geografico con ambienti del servizio app
## <a name="overview"></a>Panoramica

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Gli scenari applicativi che richiedono una scalabilità molto elevata possono superare la capacità delle risorse di calcolo disponibile per una singola distribuzione di un'app.  Applicazioni di voto, eventi sportivi ed eventi di intrattenimento televisivi sono tutti esempi di scenari che richiedono una scalabilità estremamente elevata. I requisiti di scalabilità elevata possono essere soddisfatti scalando orizzontalmente le app. Per gestire i requisiti di carico estremi, è possibile eseguire molte distribuzioni di app all'interno di una singola area e tra aree.

Gli ambienti del servizio app sono una piattaforma ideale per la scalabilità orizzontale. Dopo aver selezionato una configurazione ambiente del servizio app in grado di supportare una frequenza di richieste nota, gli sviluppatori possono distribuire altri ambienti del servizio app in modalità "cookie cutter" per ottenere una capacità di carico di picco desiderata.

Si supponga, ad esempio, che un'app in esecuzione in una configurazione ambiente del servizio app sia stata testata per gestire 20.000 richieste al secondo (RPS).  Se la capacità di carico di picco desiderata è 100.000 RPS, è possibile creare e configurare cinque (5) ambienti del servizio app per garantire che l'applicazione possa gestire il carico massimo proiettato.

Poiché in genere i clienti accedono alle app usando un dominio personalizzato, o personale, gli sviluppatori hanno bisogno di un modo per distribuire le richieste di app tra tutte le istanze di Ambiente del servizio app.  Un ottimo modo per raggiungere questo obiettivo è risolvere il dominio personalizzato usando un [profilo Gestione traffico di Azure personalizzato.][AzureTrafficManagerProfile]  Il profilo di Gestione traffico può essere configurato in modo che punti a tutti i singoli ambienti del servizio app.  Gestione traffico gestirà automaticamente la distribuzione dei clienti in tutti gli ambienti del servizio app, in base alle impostazioni di bilanciamento del carico nel profilo di Gestione traffico.  Questo approccio funziona indipendentemente dal fatto che tutti gli ambienti del servizio app siano distribuiti in una singola area di Azure o a livello mondiale tra più aree di Azure.

Inoltre, poiché i clienti accedono alle app tramite il dominio personale, non conoscono il numero di ambienti del servizio app che eseguono un'app.  Di conseguenza, gli sviluppatori possono aggiungere e rimuovere in modo rapido e facile ambienti del servizio app in base al carico di traffico osservato.

Il diagramma concettuale seguente illustra un'app con scalabilità orizzontale fra tre ambienti del servizio app in una singola area geografica.

![Architettura concettuale][ConceptualArchitecture] 

Il resto di questo argomento illustra in dettaglio la procedura prevista per configurare una topologia distribuita per l'app di esempio con più ambienti del servizio app.

## <a name="planning-the-topology"></a>Pianificazione della topologia
Prima di compilare il footprint di un'app distribuita, è utile avere anticipatamente alcune informazioni.

* **Dominio personalizzato per l'app:**  definire il nome del dominio personalizzato che i clienti useranno per accedere all'app.  Per l'app di esempio, il nome di dominio personalizzato è `www.scalableasedemo.com` .
* **Dominio di Gestione traffico:** Scegliere un nome di dominio quando si crea [un Gestione traffico di Azure profilo .][AzureTrafficManagerProfile]  Questo nome sarà combinato con il suffisso *trafficmanager.net* per registrare una voce di dominio gestita da Gestione traffico.  Per l'app di esempio il nome scelto è *scalable-ase-demo*.  Il nome di dominio completo gestito da Gestione traffico sarà quindi *scalable-ase-demo.trafficmanager.net*.
* **Strategia per la scalabilità del footprint dell'app:**  stabilire se il footprint dell'applicazione sarà distribuito tra più ambienti del servizio app in una singola area geografica,  in più aree geografiche  o con una combinazione di entrambi gli approcci.  Basare la decisione sulle aspettative di origine del traffico dei clienti e sulla scalabilità del resto dell'infrastruttura back-end di supporto di un'app.  Ad esempio, con un'applicazione senza stato al 100%, un'app può essere ridimensionata in modo massico usando una combinazione di molti ambienti del servizio app in ogni area di Azure, moltiplicati per gli ambienti del servizio app distribuiti in molte aree di Azure.  Con oltre 15 aree globali di Azure tra cui scegliere, i clienti possono realmente creare un footprint di applicazioni su vasta scala su vasta scala.  Per l'app di esempio usata per questo articolo, sono stati creati tre ambienti del servizio app in una singola area di Azure (Stati Uniti centro-meridionali).
* **Convenzione di denominazione per gli ambienti del servizio app:**  ogni ambiente del servizio app richiede un nome univoco.  Oltre a uno o due ambienti del servizio app, è utile avere una convenzione di denominazione per identificare ogni ambiente del servizio app.  Per l'app di esempio è stata usata una convenzione di denominazione semplice.  I nomi dei tre ambienti del servizio app sono *fe1ase*, *fe2ase* e *fe3ase*.
* **Convenzione di denominazione per le app:**  poiché verranno distribuite più istanze dell'app, è necessario definire un nome per ogni istanza dell'app distribuita.  Una funzionalità poco nota ma comoda degli ambienti del servizio app è che lo stesso nome dell'app può essere usato in più ambienti del servizio app.  Poiché ogni ambiente del servizio app ha un suffisso di dominio univoco, gli sviluppatori possono scegliere di riutilizzare esattamente lo stesso nome di app in ogni ambiente.  Ad esempio, uno sviluppatore può avere app denominate come segue:  *myapp.foo1.p.azurewebsites.net*, *myapp.foo2.p.azurewebsites.net*, *myapp.foo3.p.azurewebsites.net* e così via.  Per l'app di esempio, tuttavia, ogni istanza dell'app ha anche un nome univoco.  I nomi delle istanze dell'app usati sono *webfrontend1*, *webfrontend2* e *webfrontend3*.

## <a name="setting-up-the-traffic-manager-profile"></a>Configurazione del profilo di Gestione traffico
Dopo la distribuzione di più istanze di un'app in più ambienti del servizio app, è possibile registrare le singole istanze con Gestione traffico.  Per l'app di esempio è necessario un profilo di Gestione traffico *per* scalable-ase-demo.trafficmanager.net in grado di indirizzare i clienti a una delle istanze di app distribuite seguenti:

* **webfrontend1.fe1ase.p.azurewebsites.net:**  istanza dell'app di esempio distribuita nel primo ambiente del servizio app.
* **webfrontend2.fe2ase.p.azurewebsites.net:**  istanza dell'app di esempio distribuita nel secondo ambiente del servizio app.
* **webfrontend3.fe3ase.p.azurewebsites.net:**  istanza dell'app di esempio distribuita nel terzo ambiente del servizio app.

Il modo più semplice per registrare più endpoint Servizio app di Azure, tutti in esecuzione nella stessa area di **Azure,** è con PowerShell [Azure Resource Manager Gestione traffico .][ARMTrafficManager]  

Come primo passaggio creare un profilo di Gestione traffico di Azure.  Il codice seguente illustra come è stato creato il profilo per l'app di esempio:

```azurepowershell-interactive
$profile = New-AzureTrafficManagerProfile –Name scalableasedemo -ResourceGroupName yourRGNameHere -TrafficRoutingMethod Weighted -RelativeDnsName scalable-ase-demo -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Si noti che il parametro *RelativeDnsName* è stato impostato su *scalable-ase-demo*.  Questo parametro fa sì che il *nome di scalable-ase-demo.trafficmanager.net* da creare e associare a un profilo di Gestione traffico.

Il *parametro TrafficRoutingMethod* definisce i criteri di bilanciamento del carico che Gestione traffico userà per determinare come distribuire il carico dei clienti tra tutti gli endpoint disponibili.  In questo esempio è *stato scelto il metodo Weighted.*  Per questa scelta, le richieste dei clienti verranno distribuite tra tutti gli endpoint applicazione registrati in base ai pesi relativi associati a ogni endpoint. 

Dopo la creazione del profilo, ogni istanza dell'app viene aggiunta al profilo come endpoint nativo di Azure.  Il codice seguente recupera un riferimento a ogni app Web front-end. Aggiunge quindi ogni app come endpoint di Gestione traffico tramite il *parametro TargetResourceId.*

```azurepowershell-interactive
$webapp1 = Get-AzWebApp -Name webfrontend1
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend1 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp1.Id –EndpointStatus Enabled –Weight 10

$webapp2 = Get-AzWebApp -Name webfrontend2
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend2 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp2.Id –EndpointStatus Enabled –Weight 10

$webapp3 = Get-AzWebApp -Name webfrontend3
Add-AzureTrafficManagerEndpointConfig –EndpointName webfrontend3 –TrafficManagerProfile $profile –Type AzureEndpoints -TargetResourceId $webapp3.Id –EndpointStatus Enabled –Weight 10

Set-AzureTrafficManagerProfile –TrafficManagerProfile $profile
```

Come si noterà, è presente una chiamata a *Add-AzureTrafficManagerEndpointConfig* per ogni singola istanza dell'app.  Il *parametro TargetResourceId* in ogni comando di PowerShell fa riferimento a una delle tre istanze di app distribuite.  Il profilo di Gestione traffico distribuirà il carico tra tutti e tre gli endpoint registrati nel profilo.

Tutti e tre gli endpoint usano lo stesso valore (10) per il parametro *Weight* .  Questa situazione comporta la distribuzione relativamente uniforme delle richieste dei clienti tra tutte e tre le istanze di app da parte di Gestione traffico. 

## <a name="pointing-the-apps-custom-domain-at-the-traffic-manager-domain"></a>Indirizzamento del dominio personalizzato dell'app al dominio di Gestione traffico
Come passaggio finale è necessario che il dominio personalizzato dell'app punti al dominio di Gestione traffico.  Per l'app di esempio, puntare `www.scalableasedemo.com` a `scalable-ase-demo.trafficmanager.net` .  Completare questo passaggio con il registrar che gestisce il dominio personalizzato.  

Usando gli strumenti di gestione del dominio del registrar, è necessario creare un record CNAME che dal dominio personalizzato punti al dominio di Gestione traffico.  L'immagine seguente mostra un esempio di come appare la configurazione di questo CNAME:

![CNAME per il dominio personalizzato][CNAMEforCustomDomain] 

Anche se questo aspetto non viene trattato in questo argomento, tenere presente che per ogni singola istanza dell'app deve essere registrato anche il dominio personalizzato.  In caso contrario, se una richiesta la effettua a un'istanza dell'app e l'applicazione non ha registrato il dominio personalizzato con l'app, la richiesta avrà esito negativo.

In questo esempio il dominio personalizzato è `www.scalableasedemo.com` e a ogni istanza dell'applicazione è associato il dominio personalizzato.

![Dominio personalizzato][CustomDomain] 

Per un riepilogo della registrazione di un dominio personalizzato con le Servizio app di Azure, vedere Registrazione [di domini personalizzati.][RegisterCustomDomain]

## <a name="trying-out-the-distributed-topology"></a>Prova della topologia distribuita
Come risultato finale della configurazione di Gestione traffico e del DNS, il flusso delle richieste per `www.scalableasedemo.com` avviene con la sequenza seguente:

1. Un browser o un dispositivo esegue una ricerca DNS di `www.scalableasedemo.com`
2. La voce CNAME del registrar causa il reindirizzamento della ricerca DNS a Gestione traffico di Azure.
3. Viene eseguita una ricerca DNS di *scalable-ase-demo.trafficmanager.net* in uno dei server DNS di Gestione traffico di Azure.
4. In base ai criteri di bilanciamento del carico specificati in precedenza nel parametro *TrafficRoutingMethod,* Gestione traffico seleziona uno degli endpoint configurati. Restituisce quindi l'FQDN dell'endpoint al browser o al dispositivo.
5. Poiché il nome di dominio completo dell'endpoint è l'URL di un'istanza dell'app in esecuzione in un ambiente del servizio app, il browser o il dispositivo chiederà a un server DNS di Microsoft Azure di risolvere l'FQDN in un indirizzo IP. 
6. Il browser o il dispositivo invierà la richiesta HTTP/S all'indirizzo IP.  
7. La richiesta arriverà a una delle istanze dell'app in esecuzione in uno degli ambienti del servizio app.

L'immagine della console seguente mostra una ricerca DNS per il dominio personalizzato dell'app di esempio. Viene risolto correttamente in un'istanza dell'app eseguita in uno dei tre ambienti del servizio app di esempio(in questo caso, il secondo dei tre ambienti del servizio app):

![Ricerca DNS][DNSLookup] 

## <a name="additional-links-and-information"></a>Informazioni e collegamenti aggiuntivi
La documentazione relativa a PowerShell [Azure Resource Manager Gestione traffico supporta][ARMTrafficManager].  

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[AzureTrafficManagerProfile]: ../../traffic-manager/traffic-manager-manage-profiles.md
[ARMTrafficManager]: ../../traffic-manager/traffic-manager-powershell-arm.md
[RegisterCustomDomain]: ../app-service-web-tutorial-custom-domain.md


<!-- IMAGES -->
[ConceptualArchitecture]: ./media/app-service-app-service-environment-geo-distributed-scale/ConceptualArchitecture-1.png
[CNAMEforCustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CNAMECustomDomain-1.png
[DNSLookup]:  ./media/app-service-app-service-environment-geo-distributed-scale/DNSLookup-1.png
[CustomDomain]:  ./media/app-service-app-service-environment-geo-distributed-scale/CustomDomain-1.png 
