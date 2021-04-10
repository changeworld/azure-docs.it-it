---
title: Domande frequenti sul collegamento privato di Azure
description: Informazioni sul collegamento privato di Azure.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: allensu
ms.openlocfilehash: d06e90a691389b99d8f439364203b921f49b2305
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496474"
---
# <a name="azure-private-link-frequently-asked-questions-faq"></a>Domande frequenti sul collegamento privato di Azure

## <a name="private-link"></a>Collegamento privato

### <a name="what-is-azure-private-endpoint-and-azure-private-link-service"></a>Che cos'è l'endpoint privato di Azure e il servizio di collegamento privato di Azure?

- **[Endpoint privato](private-endpoint-overview.md)** di Azure: l'endpoint privato di Azure è un'interfaccia di rete che si connette privatamente e in modo sicuro a un servizio basato su collegamento privato di Azure. È possibile usare endpoint privati per connettersi a un servizio Azure PaaS che supporta il collegamento privato o al servizio di collegamento privato.
- **[Servizio di collegamento privato di Azure](private-link-service-overview.md)**: il servizio di collegamento privato di Azure è un servizio creato da un provider di servizi. Attualmente, un servizio di collegamento privato può essere collegato alla configurazione IP front-end di un Load Balancer Standard. 

### <a name="how-is-traffic-being-sent-when-using-private-link"></a>Come viene inviato il traffico quando si usa un collegamento privato?
Il traffico viene inviato privatamente usando la backbone Microsoft. Non attraversa Internet. Il collegamento privato di Azure non archivia i dati dei clienti.
 
### <a name="what-is-the-difference-between-service-endpoints-and-private-endpoints"></a>Qual è la differenza tra gli endpoint del servizio e gli endpoint privati?
- Gli endpoint privati concedono l'accesso di rete a risorse specifiche associate a un determinato servizio che fornisce segmentazione granulare. Il traffico può raggiungere la risorsa del servizio da locale senza usare endpoint pubblici.
- Un endpoint del servizio rimane un indirizzo IP instradabile pubblicamente.  Un endpoint privato è un indirizzo IP privato nello spazio degli indirizzi della rete virtuale in cui è configurato l'endpoint privato.

### <a name="what-is-the-relationship-between-private-link-service-and-private-endpoint"></a>Qual è la relazione tra il servizio di collegamento privato e l'endpoint privato?
Più tipi di risorse di collegamento privato supportano l'accesso tramite endpoint privato. Le risorse includono i servizi PaaS di Azure e il servizio di collegamento privato. Si tratta di una relazione uno-a-molti. 

Un servizio di collegamento privato riceve connessioni da più endpoint privati. Un endpoint privato si connette a un servizio di collegamento privato.    

### <a name="do-i-need-to-disable-network-policies-for-private-link"></a>È necessario disabilitare I criteri di rete per il collegamento privato
Sì. Sia l'endpoint privato che il servizio di collegamento privato devono disabilitare i criteri di rete per funzionare correttamente. Entrambi hanno proprietà indipendenti l'una dall'altra.

## <a name="private-endpoint"></a>Endpoint privato 
 
### <a name="can-i-create-multiple-private-endpoints-in-same-vnet-can-they-connect-to-different-services"></a>È possibile creare più endpoint privati nella stessa VNet? È possibile connettersi a servizi diversi? 
Sì. È possibile avere più endpoint privati nella stessa VNet o nella stessa subnet. Possono connettersi a servizi diversi.  
 
### <a name="do-i-require-a-dedicated-subnet-for-private-endpoints"></a>È necessaria una subnet dedicata per gli endpoint privati? 
No. Non è necessaria una subnet dedicata per gli endpoint privati. È possibile scegliere un indirizzo IP di endpoint privato da qualsiasi subnet dal VNet in cui viene distribuito il servizio.  
 
### <a name="can-a-private-endpoint-connect-to-private-link-services-across-azure-active-directory-tenants"></a>Un endpoint privato può connettersi a servizi di collegamento privato in Azure Active Directory tenant? 
Sì. Gli endpoint privati possono connettersi a servizi di collegamento privato o a una PaaS di Azure tra Azure Active Directory tenant. Gli endpoint privati che si connettono tra i tenant richiedono l'approvazione manuale della richiesta. 
 
### <a name="can-private-endpoint-connect-to-azure-paas-resources-across-azure-regions"></a>L'endpoint privato può connettersi alle risorse PaaS di Azure tra aree di Azure?
Sì. Gli endpoint privati possono connettersi alle risorse PaaS di Azure tra aree di Azure.

### <a name="can-i-modify-my-private-endpoint-network-interface-nic-"></a>È possibile modificare l'interfaccia di rete (NIC) dell'endpoint privato?
Quando viene creato un endpoint privato, viene assegnata una scheda di interfaccia di rete di sola lettura. Questa operazione non può essere modificata e rimarrà per il ciclo di vita dell'endpoint privato.

### <a name="how-do-i-achieve-availability-while-using-private-endpoints-in-case-of-regional-failures-"></a>Ricerca per categorie ottenere la disponibilità usando endpoint privati in caso di errori a livello di area?

Gli endpoint privati sono risorse a disponibilità elevata con contratto di SLA del 99,99% [[contratto di contratto per il collegamento privato di Azure]](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/). Tuttavia, poiché si tratta di risorse regionali, eventuali interruzioni dell'area di Azure possono avere un effetto sulla disponibilità. Per ottenere la disponibilità in caso di errori a livello di area, è possibile distribuire più PEs connessi alla stessa risorsa di destinazione in aree diverse. In questo modo, se un'area diventa inattiva, è comunque possibile indirizzare il traffico per gli scenari di ripristino tramite PE in un'area diversa per accedere alla risorsa di destinazione. Per informazioni sulle modalità di gestione degli errori a livello di area sul lato del servizio di destinazione, vedere la documentazione del servizio relativa al failover e al ripristino. Il traffico di collegamento privato segue la risoluzione DNS di Azure per l'endpoint di destinazione. 


## <a name="private-link-service"></a>Servizio Collegamento privato
 
### <a name="what-are-the-pre-requisites-for-creating-a-private-link-service"></a>Quali sono i prerequisiti per la creazione di un servizio di collegamento privato? 
I backend del servizio devono trovarsi in una rete virtuale e dietro un Load Balancer Standard.
 
### <a name="how-can-i-scale-my-private-link-service"></a>Come è possibile ridimensionare il servizio di collegamento privato? 
È possibile ridimensionare il servizio di collegamento privato in diversi modi: 
- Aggiungere macchine virtuali back-end al pool dietro la Load Balancer Standard 
- Aggiungere un indirizzo IP al servizio di collegamento privato. Sono consentiti fino a 8 indirizzi IP per servizio di collegamento privato.  
- Aggiungere un nuovo servizio di collegamento privato per Load Balancer Standard. Sono consentiti fino a otto servizi di collegamento privato per ogni servizio di bilanciamento del carico.   

### <a name="what-is-natnetwork-address-translation-ip-configuration-used-in-private-link-service-how-can-i-scale-in-terms-of-available-ports-and-connections"></a>Che cos'è la configurazione IP NAT (Network Address Translation) usata nel servizio di collegamento privato? Come è possibile applicare la scalabilità in termini di porte e connessioni disponibili? 

La configurazione IP NAT garantisce che non esistano conflitti IP tra l'origine (lato utente) e lo spazio degli indirizzi di destinazione (provider di servizi) fornendo la NAT di origine sul traffico dei collegamenti privati sul lato di destinazione (lato provider di servizi). L'indirizzo IP NAT viene visualizzato come IP di origine per tutti i pacchetti ricevuti dal servizio e dall'IP di destinazione per tutti i pacchetti inviati dal servizio.  È possibile scegliere l'indirizzo IP NAT da qualsiasi subnet nella rete virtuale di un provider di servizi. 

Ogni IP NAT fornisce connessioni TCP 64K (porte 64K) per macchina virtuale dietro la Load Balancer Standard. Per ridimensionare e aggiungere altre connessioni, è possibile aggiungere nuovi IP NAT o aggiungere altre macchine virtuali dietro la Load Balancer Standard. Questa operazione consente di ridimensionare la disponibilità delle porte e consentire più connessioni. Le connessioni verranno distribuite tra IP NAT e macchine virtuali dietro la Load Balancer Standard.

### <a name="can-i-connect-my-service-to-multiple-private-endpoints"></a>È possibile connettere il servizio a più endpoint privati?
Sì. Un servizio di collegamento privato può ricevere connessioni da più endpoint privati. Un endpoint privato può tuttavia connettersi a un solo servizio di collegamento privato.  
 
### <a name="how-should-i-control-the-exposure-of-my-private-link-service"></a>Come è possibile controllare l'esposizione del servizio di collegamento privato?
È possibile controllare l'esposizione usando la configurazione di visibilità nel servizio di collegamento privato. Visibility supporta tre impostazioni:

- Le sottoscrizioni solo **None** con accesso RBAC di Azure possono individuare il servizio. 
- Le sottoscrizioni solo **restrittive** approvate e con accesso RBAC di Azure possono individuare il servizio. 
- **Tutti** : tutti gli utenti possono individuare il servizio. 
 
### <a name="can-i-create-a-private-link-service-with-basic-load-balancer"></a>È possibile creare un servizio di collegamento privato con il servizio di bilanciamento del carico di base? 
No. Il servizio di collegamento privato su un servizio di bilanciamento del carico di base non è supportato.
 
### <a name="is-a-dedicated-subnet-required-for-private-link-service"></a>È necessaria una subnet dedicata per il servizio di collegamento privato? 
No. Il servizio di collegamento privato non richiede una subnet dedicata. È possibile scegliere qualsiasi subnet nella VNet in cui viene distribuito il servizio.   

### <a name="im-a-service-provider-using-azure-private-link-do-i-need-to-make-sure-all-my-customers-have-unique-ip-space-and-dont-overlap-with-my-ip-space"></a>Sono un provider di servizi che usa il collegamento privato di Azure. È necessario assicurarsi che tutti i miei clienti abbiano uno spazio IP univoco e non si sovrappongano allo spazio IP? 
No. Il collegamento privato di Azure offre questa funzionalità. Non è necessario disporre di uno spazio di indirizzi non sovrapposto con lo spazio degli indirizzi del cliente. 

##  <a name="next-steps"></a>Passaggi successivi

- Vedere informazioni su [collegamento privato di Azure](private-link-overview.md)
