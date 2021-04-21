---
title: Accedere alle reti virtuali di Azure
description: Panoramica del modo in cui gli ambienti del servizio di integrazione (ISE) consentono alle app per la logica di accedere alle reti virtuali di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 3070083040424b877159955dc2138f15319f05c8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766390"
---
# <a name="access-to-azure-virtual-network-resources-from-azure-logic-apps-by-using-integration-service-environments-ises"></a>Accedere alle risorse di Rete virtuale di Azure da App per la logica di Azure usando ambienti del servizio di integrazione (ISE)

In alcuni casi, le app per la logica devono accedere a risorse protette, ad esempio macchine virtuali (VM) e altri sistemi o servizi, all'interno o connessi a una rete [virtuale di Azure.](../virtual-network/virtual-networks-overview.md) Per configurare questo accesso, è possibile [creare un ambiente del servizio *di* integrazione (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) Un ISE è un'istanza del servizio App per la logica che usa risorse dedicate ed è in esecuzione separatamente dal servizio app per la logica multi-tenant "globale". I dati in un ISE rimangono nella stessa area in [cui si crea e si distribuisce tale ISE.](https://azure.microsoft.com/global-infrastructure/data-residency/)

Ad esempio, alcune reti virtuali di Azure usano endpoint privati, che è possibile configurare tramite [collegamento privato di Azure](../private-link/private-link-overview.md), per fornire l'accesso ai servizi PaaS di Azure, ad esempio Archiviazione di Azure, Azure Cosmos DB o database SQL di Azure, servizi partner o servizi clienti ospitati in Azure. Se le app per la logica devono accedere alle reti virtuali che usano endpoint privati, è necessario creare, distribuire ed eseguire tali app per la logica all'interno di un ISE.

Quando si crea un ISE, Azure lo *inserisce* o lo distribuisce nella rete virtuale di Azure. È quindi possibile usare questo ISE come posizione per le app per la logica e gli account di integrazione che richiedono l'accesso.

![Selezionare l'ambiente del servizio di integrazione](./media/connect-virtual-network-vnet-isolated-environment-overview/select-logic-app-integration-service-environment.png)

Questa panoramica offre altre informazioni sui motivi per cui si vuole usare un ambiente del servizio di integrazione, sulle differenze tra il servizio app per la logica dedicato e [multi-tenant](#difference)e su come accedere direttamente alle risorse [all'interno](#benefits)o alla rete virtuale di Azure connesse.

<a name="benefits"></a>

## <a name="why-use-an-ise"></a>Perché usare un ISE?

L'esecuzione di app per la logica in un'istanza dedicata separata consente di ridurre il potenziale impatto degli altri tenant di Azure sulle prestazioni delle app nel cosiddetto [effetto "noisy neighbor"](https://en.wikipedia.org/wiki/Cloud_computing_issues#Performance_interference_and_noisy_neighbors). Un ISE offre inoltre questi vantaggi:

* Accesso diretto alle risorse interne o connesse alla rete virtuale

  Le app per la logica create ed eseguite in un ISE possono usare connettori appositamente progettati [che vengono eseguiti nell'ISE.](../connectors/managed.md#ise-connectors) Se esiste un connettore ISE per un sistema locale o un'origine dati, è possibile connettersi direttamente senza dover usare il [gateway dati locale.](../logic-apps/logic-apps-gateway-connection.md) Per altre informazioni, vedere Confronto tra dedicato e [multi-tenant](#difference) e [Accesso ai sistemi locali](#on-premises) più avanti in questo argomento.

* Accesso continuo alle risorse esterne o non connesse alla rete virtuale

  Le app per la logica create ed eseguite in un ISE possono comunque usare connettori eseguiti nel servizio App per la logica multi-tenant quando non è disponibile un connettore specifico di ISE. Per altre informazioni, vedere [Dedicato e multi-tenant.](#difference)

* Disponibilità di indirizzi IP statici separati dagli indirizzi IP statici condivisi dalle app per la logica nel servizio multi-tenant. È anche possibile configurare un unico indirizzo IP in uscita pubblico, statico e prevedibile per comunicare con i sistemi di destinazione. In questo modo, non è necessario configurare ulteriori aperture del firewall in questi sistemi di destinazione per ogni ISE.

* Aumento dei limiti per durata dell'esecuzione, conservazione dell'archiviazione, velocità effettiva, timeout di richieste e risposte HTTP, dimensioni dei messaggi e richieste di connettori personalizzati. Per altre informazioni, vedere [Limiti e configurazione per App per la logica di Azure](logic-apps-limits-and-config.md).

<a name="difference"></a>

## <a name="dedicated-versus-multi-tenant"></a>Dedicato e multi-tenant

Quando si creano ed eseguono app per la logica in un ambiente ISE, si ottengono le stesse esperienze utente e funzionalità simili a quelle del servizio App per la logica multi-tenant. È possibile usare tutti gli stessi trigger, azioni e connettori gestiti predefiniti disponibili nel servizio App per la logica multi-tenant. Alcuni connettori gestiti offrono versioni ISE aggiuntive. La differenza tra i connettori ISE e i connettori non ISE esiste nel punto in cui vengono eseguiti e nelle etichette presenti in Progettazione app per la logica quando si lavora all'interno di un ISE.

![Connettori con e senza etichette in un ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/labeled-trigger-actions-integration-service-environment.png)

* I trigger e le azioni predefiniti, ad esempio HTTP, visualizzano l'etichetta **CORE** ed eseguono nello stesso ISE dell'app per la logica.

* I connettori gestiti che visualizzano **l'etichetta ISE** sono appositamente progettati per le ise e vengono sempre eseguiti nello stesso *ISE dell'app per la logica.* Ad esempio, di seguito sono riportati [alcuni connettori che offrono versioni ISE:](../connectors/managed.md#ise-connectors)<p>

  * Archiviazione BLOB, Archiviazione file e Archiviazione tabelle di Azure
  * bus di servizio di Azure, Code di Azure, Hub eventi di Azure
  * Automazione di Azure, Azure Key Vault, Griglia di eventi di Azure e Monitoraggio di Azure log
  * FTP, SFTP-SSH, file system e SMTP
  * SAP, IBM MQ, IBM DB2 e IBM 3270
  * SQL Server, Azure Synapse Analytics, Azure Cosmos DB
  * AS2, X12 ed EDIFACT

  Con rare eccezioni, se è disponibile un connettore ISE per un'origine dati o un sistema locale, è possibile connettersi direttamente senza usare il [gateway dati locale.](../logic-apps/logic-apps-gateway-connection.md) Per altre informazioni, vedere Accesso ai [sistemi locali](#on-premises) più avanti in questo argomento.

* I connettori gestiti che non visualizzano **l'etichetta ISE** continuano a funzionare per le app per la logica all'interno di un ISE. Questi connettori *vengono sempre eseguiti nel servizio App per la logica multi-tenant,* non nell'ISE.

* I connettori personalizzati creati all'esterno di un *ISE,* indipendentemente dal fatto che richiedano o meno il [gateway](../logic-apps/logic-apps-gateway-connection.md)dati locale, continuano a funzionare per le app per la logica all'interno di un ISE. Tuttavia, i connettori personalizzati creati all'interno di *un ISE* non funzionano con il gateway dati locale. Per altre informazioni, [vedere Accesso ai sistemi locali.](#on-premises)

<a name="on-premises"></a>

## <a name="access-to-on-premises-systems"></a>Accesso ai sistemi locali

Le app per la logica eseguite all'interno di un ISE possono accedere direttamente a origini dati e sistemi locali all'interno o connessi a una rete virtuale di Azure usando questi elementi:<p>

* Trigger o azione HTTP, che visualizza **l'etichetta CORE**

* Connettore **ISE,** se disponibile, per un sistema locale o un'origine dati

  Se è disponibile un connettore ISE, è possibile accedere direttamente al sistema o all'origine dati senza il [gateway dati locale.](../logic-apps/logic-apps-gateway-connection.md) Tuttavia, se è necessario accedere SQL Server da un ISE e usare autenticazione di Windows, è necessario usare la versione non ISE del connettore e il gateway dati locale. La versione ISE del connettore non supporta l'autenticazione di Windows. Per altre informazioni, vedere [Connettori ISE](../connectors/managed.md#ise-connectors) e [Connettersi da un ambiente del servizio di integrazione.](../connectors/managed.md#integration-account-connectors)

* Un connettore personalizzato

  * I connettori personalizzati creati all'esterno di un *ISE,* indipendentemente dal fatto che richiedano o meno il [gateway](../logic-apps/logic-apps-gateway-connection.md)dati locale, continuano a funzionare per le app per la logica all'interno di un ISE.

  * I connettori personalizzati creati *all'interno* di un ISE non funzionano con il gateway dati locale. Tuttavia, questi connettori possono accedere direttamente ai sistemi locali e alle origini dati all'interno o connessi alla rete virtuale che ospita l'ISE. Pertanto, le app per la logica che si trovano all'interno di un ISE in genere non necessitano del gateway dati quando accedono a tali risorse.

Per accedere ai sistemi locali e alle origini dati che non hanno connettori ISE, sono esterni alla rete virtuale o non sono connessi alla rete virtuale, è comunque necessario usare il gateway dati locale. Le app per la logica all'interno di un ISE possono continuare a usare connettori che non hanno **l'etichetta CORE** **o ISE.** Questi connettori vengono eseguiti nel servizio App per la logica multi-tenant, anziché nell'ISE. 

<a name="ise-level"></a>

## <a name="ise-skus"></a>SKU ISE

Quando si crea l'ISE, è possibile selezionare lo SKU sviluppatore o lo SKU Premium. Questa opzione SKU è disponibile solo durante la creazione di ISE e non può essere modificata in un secondo momento. Ecco le differenze tra questi SKU:

* **Developer**

  Offre un ISE a basso costo che è possibile usare per l'esplorazione, gli esperimenti, lo sviluppo e i test, ma non per i test di produzione o delle prestazioni. Lo SKU per sviluppatori include trigger e azioni predefiniti, connettori Standard, connettori Enterprise e un singolo account di integrazione del livello gratuito a un [prezzo mensile fisso.](https://azure.microsoft.com/pricing/details/logic-apps) [](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) 

  > [!IMPORTANT]
  > Questo SKU non ha alcun contratto di servizio, funzionalità di scalabilità verticale o ridondanza durante il riciclo, il che significa che potrebbero verificarsi ritardi o tempi di inattività. Gli aggiornamenti back-end potrebbero interrompere il servizio in modo intermittente.

  Per informazioni sulla capacità e sui limiti, vedere [Limiti ISE in App per la logica di Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). Per informazioni sul funzionamento della fatturazione per le ISE, vedere il modello [tariffario app per la logica](../logic-apps/logic-apps-pricing.md#fixed-pricing).

* **Premium**

  Fornisce un ISE che è possibile usare per i test di produzione e prestazioni. Lo SKU Premium include supporto del contratto di servizio, trigger e azioni predefiniti, connettori Standard, connettori Enterprise, un singolo account di integrazione del livello [Standard,](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits) funzionalità di aumento delle dimensioni e ridondanza durante il riciclo [a](https://azure.microsoft.com/pricing/details/logic-apps)un prezzo mensile fisso.

  Per informazioni sulla capacità e sui limiti, vedere [Limiti ISE in App per la logica di Azure](logic-apps-limits-and-config.md#integration-service-environment-ise). Per informazioni sul funzionamento della fatturazione per le ISE, vedere il modello [tariffario app per la logica](../logic-apps/logic-apps-pricing.md#fixed-pricing).

<a name="endpoint-access"></a>

## <a name="ise-endpoint-access"></a>Accesso agli endpoint ISE

Quando si crea l'ISE, è possibile scegliere di usare endpoint di accesso interni o esterni. La selezione determina se i trigger di richiesta o webhook nelle app per la logica nell'ISE possono ricevere chiamate dall'esterno della rete virtuale. Questi endpoint influiscono anche sul modo in cui è possibile accedere agli input e agli output dalla cronologia di esecuzione delle app per la logica.

> [!IMPORTANT]
> È possibile selezionare l'endpoint di accesso solo durante la creazione dell'ISE e non è possibile modificare questa opzione in un secondo momento.

* **Interno:** gli endpoint privati consentono le chiamate alle app per la logica nell'ISE, dove è possibile visualizzare e accedere agli input e agli output dalla cronologia delle esecuzioni delle app per la logica solo dall'interno della *rete virtuale.*

  > [!IMPORTANT]
  > Se è necessario usare questi trigger basati su webhook, usare endpoint *esterni,* non endpoint interni, quando si crea l'ISE:
  > 
  > * Azure DevOps
  > * Griglia di eventi di Azure
  > * Common Data Service
  > * Office 365
  > * SAP (versione ISE)
  > 
  > Assicurarsi inoltre di disporre della connettività di rete tra gli endpoint privati e il computer da cui si vuole accedere alla cronologia di esecuzione. In caso contrario, quando si tenta di visualizzare la cronologia di esecuzione dell'app per la logica, viene visualizzato l'errore "Errore imprevisto. Impossibile recuperare".
  >
  > ![Archiviazione di Azure un errore di azione risultante dall'impossibilità di inviare traffico attraverso il firewall](./media/connect-virtual-network-vnet-isolated-environment-overview/integration-service-environment-error.png)
  >
  > Ad esempio, il computer client può esistere all'interno della rete virtuale dell'ISE o all'interno di una rete virtuale connessa alla rete virtuale dell'ISE tramite peering o una rete privata virtuale. 

* **Esterno:** gli endpoint pubblici consentono le chiamate alle app per la logica nell'ISE, dove è possibile visualizzare e accedere agli input e agli output dalla cronologia delle esecuzioni delle app per la logica dall'esterno *della rete virtuale.* Se si usano gruppi di sicurezza di rete( NSG), assicurarsi che siano impostati con regole in ingresso per consentire l'accesso agli input e agli output della cronologia di esecuzione. Per altre informazioni, vedere [Abilitare l'accesso per ISE.](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access)

Per determinare se l'ISE usa un endpoint di accesso interno o esterno, nel menu dell'ISE in Impostazioni **selezionare** Proprietà e trovare la proprietà **Endpoint di** accesso:

![Trovare l'endpoint di accesso ISE](./media/connect-virtual-network-vnet-isolated-environment-overview/find-ise-access-endpoint.png)

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Modello di prezzi

Le app per la logica, i trigger predefiniti, le azioni predefinite e i connettori eseguiti nell'ISE usano un piano tariffario fisso diverso dal piano tariffario basato sul consumo. Per altre informazioni, vedere Modello di determinazione [prezzi di App per la logica.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Per informazioni sui prezzi, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/).

<a name="create-integration-account-environment"></a>

## <a name="integration-accounts-with-ise"></a>Account di integrazione con ambiente del servizio di integrazione

È possibile usare gli account di integrazione con App per la logica all'interno di un ambiente del servizio di integrazione (ISE). È necessario tuttavia che gli account di integrazione usino lo *stesso ISE* delle app per la logica collegate. Le app per la logica in un ambiente del servizio di integrazione possono fare riferimento solo agli account di integrazione che sono nello stesso ambiente del servizio di integrazione. Quando si crea un account di integrazione, è possibile selezionare l'ambiente del servizio di integrazione come posizione per l'account di integrazione. Per informazioni sul funzionamento dei prezzi e della fatturazione per gli account di integrazione con un ISE, vedere il modello di determinazione prezzi di [App per la logica.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Per informazioni sui prezzi, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/). Per informazioni sui limiti, vedere [Limiti dell'account di integrazione.](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits)

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi alle reti virtuali di Azure tramite le App per la logica di Azure](../logic-apps/connect-virtual-network-vnet-isolated-environment.md)
* Altre informazioni su [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md)
* Informazioni sull'[integrazione della rete virtuale per i servizi di Azure](../virtual-network/virtual-network-for-azure-services.md)
