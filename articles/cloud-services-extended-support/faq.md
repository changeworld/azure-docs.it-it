---
title: Domande frequenti per i servizi cloud di Azure (supporto esteso)
description: Domande frequenti per i servizi cloud di Azure (supporto esteso)
ms.topic: conceptual
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 33bafac9247f007978fef568469d643f1a1098df
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/05/2021
ms.locfileid: "106383587"
---
# <a name="frequently-asked-questions-for-azure-cloud-services-extended-support"></a>Domande frequenti per Servizi cloud di Azure (supporto "Extended")
Questo articolo descrive le domande frequenti relative ai servizi cloud di Azure (supporto esteso).

## <a name="general"></a>Generale

### <a name="what-is-the-resource-name-for-cloud-services-classic--cloud-services-extended-support"></a>Qual è il nome della risorsa per servizi cloud (versione classica) & servizi cloud (supporto esteso)?
- Servizi cloud (versione classica): `microsoft.classiccompute/domainnames`
- Servizi cloud (supporto esteso): `microsoft.compute/cloudservices`

### <a name="what-locations-are-available-to-deploy-cloud-services-extended-support"></a>Quali sono le località disponibili per la distribuzione di servizi cloud (supporto esteso)?
Servizi cloud (supporto esteso) è disponibile in tutte le aree del cloud pubblico.

### <a name="how-does-my-quota-change"></a>In che modo cambia la quota? 
I clienti dovranno richiedere una quota usando gli stessi processi di qualsiasi altro prodotto Azure Resource Manager. La quota in Azure Resource Manager è regionale ed è necessaria una richiesta di quota separata per ogni area.

### <a name="why-dont-i-see-a-production--staging-slot-anymore"></a>Perché non viene più visualizzato uno slot di gestione temporanea di produzione &?
Servizi cloud (supporto esteso) non supporta il concetto logico di un servizio ospitato, che include due slot (produzione & gestione temporanea). Ogni distribuzione è una distribuzione del servizio cloud indipendente (supporto esteso). Per eseguire il test e la gestione temporanea di una nuova versione di un servizio cloud, distribuire un servizio cloud (supporto esteso) e contrassegnarlo come indirizzo VIP scambiabile con un altro servizio cloud (supporto esteso)

### <a name="why-cant-i-create-an-empty-cloud-service-anymore"></a>Perché non è più possibile creare un servizio cloud vuoto?
Il concetto di nomi di servizi ospitati non esiste più, non è possibile creare un servizio cloud vuoto (supporto esteso).

### <a name="does-cloud-services-extended-support-support-resource-health-check-rhc"></a>I servizi cloud (supporto esteso) supportano Integrità risorse check (RHC)?
No, servizi cloud (supporto esteso) non supporta Integrità risorse check (RHC).

### <a name="how-are-role-instance-metrics-changing"></a>In che modo cambiano le metriche dell'istanza del ruolo?
Non sono state apportate modifiche alle metriche dell'istanza del ruolo. 

### <a name="how-are-web--worker-roles-changing"></a>In che modo vengono modificati i ruoli di lavoro & Web?
Non sono state apportate modifiche alla progettazione, all'architettura o ai componenti dei ruoli Web e di lavoro. 

### <a name="how-are-role-instances-changing"></a>In che modo vengono modificate le istanze del ruolo?
Non sono state apportate modifiche alla progettazione, all'architettura o ai componenti delle istanze del ruolo. 

### <a name="how-will-guest-os-updates-change"></a>Come cambieranno gli aggiornamenti del sistema operativo guest?
 Non sono state apportate modifiche al metodo di implementazione. I servizi cloud (versione classica) e i servizi cloud (supporto esteso) otterranno gli stessi aggiornamenti.
 
### <a name="does-cloud-services-extended-support-support-stopped-allocated-and-stopped-deallocated-states"></a>I servizi cloud (supporto esteso) supportano gli Stati con allocazione arrestata e arrestata?

La distribuzione di servizi cloud (supporto esteso) supporta solo lo stato arrestato-allocato, che viene visualizzato come "interrotto" nel portale di Azure. Arrestato: lo stato deallocato non è supportato. 

### <a name="do-cloud-services-extended-support-deployments-support-scaling-across-clusters-availability-zones-and-regions"></a>Le distribuzioni di servizi cloud (supporto esteso) supportano il ridimensionamento tra cluster, zone di disponibilità e aree?
Le distribuzioni di servizi cloud (supporto esteso) non possono essere ridimensionate in più cluster, zone di disponibilità e aree. 

### <a name="how-can-i-get-the-deployment-id-for-my-cloud-service-extended-support"></a>Come è possibile ottenere l'ID distribuzione per il servizio cloud (supporto esteso)
È possibile accedere all'ID di distribuzione, noto come ID privato, usando l'API [CloudServiceInstanceView](https://docs.microsoft.com/rest/api/compute/cloudservices/getinstanceview#cloudserviceinstanceview) . È disponibile anche nella portale di Azure nel pannello ruolo e istanze del servizio cloud (supporto esteso)

### <a name="are-there-any-pricing-differences-between-cloud-services-classic-and-cloud-services-extended-support"></a>Esistono differenze di prezzo tra i servizi cloud (versione classica) e i servizi cloud (supporto esteso)?
Servizi cloud (supporto esteso) USA Azure Key Vault e indirizzi IP pubblici di base (ARM).I clienti che richiedono certificati devono usare Azure Key Vault per la gestione dei certificati.[altre](https://azure.microsoft.com/pricing/details/key-vault/) informazioni sui prezzi Azure Key Vault.   Ogni indirizzo IP pubblico per i servizi cloud (supporto esteso) viene addebitato separatamente ([altre](https://azure.microsoft.com/pricing/details/ip-addresses/) informazioni sui prezzi degli indirizzi IP pubblici). 
## <a name="resources"></a>Risorse 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-resource-group"></a>Quali risorse collegate a una distribuzione di servizi cloud (supporto esteso) devono risiedere nello stesso gruppo di risorse?
I bilanciamenti del carico, i gruppi di sicurezza di rete e le tabelle di route devono risiedere nella stessa area e nel gruppo di risorse. 

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-region"></a>Quali risorse collegate a una distribuzione di servizi cloud (supporto esteso) devono risiedere nella stessa area?
Key Vault, rete virtuale, indirizzi IP pubblici, gruppi di sicurezza di rete e tabelle di route devono risiedere nella stessa area.

### <a name="what-resources-linked-to-a-cloud-services-extended-support-deployment-need-to-live-in-the-same-virtual-network"></a>Quali risorse collegate a una distribuzione di servizi cloud (supporto esteso) devono risiedere nella stessa rete virtuale?
Gli indirizzi IP pubblici, i bilanciamenti del carico, i gruppi di sicurezza di rete e le tabelle di route devono risiedere nella stessa rete virtuale. 

## <a name="deployment-files"></a>File di distribuzione 

### <a name="how-can-i-use-a-template-to-deploy-or-manage-my-deployment"></a>Come è possibile usare un modello per distribuire o gestire la distribuzione?
I file di modello e di parametri possono essere passati come parametri usando REST, PowerShell e l'interfaccia della riga di comando. Possono essere caricati anche usando il portale di Azure.  

### <a name="do-i-need-to-maintain-four-files-now-template-parameter-csdef-cscfg"></a>È necessario mantenere quattro file adesso? (modello, parametro, csdef, cscfg)
I file di modello e di parametri vengono usati solo per l'automazione della distribuzione. Come servizi cloud (versione classica), è possibile creare manualmente le risorse dipendenti e quindi una distribuzione di servizi cloud (supporto esteso) usando PowerShell, i comandi dell'interfaccia della riga di comando o tramite il portale con csdef esistente, cscfg.

### <a name="how-does-my-application-code-change-on-cloud-services-extended-support"></a>Come cambia il codice dell'applicazione nei servizi cloud (supporto esteso)
Non sono necessarie modifiche per il codice dell'applicazione in pacchetto in cspkg. Le applicazioni esistenti continueranno a funzionare come prima. 

### <a name="does-cloud-services-extended-support-allow-ctp-package-format"></a>Servizi cloud (supporto esteso) consente il formato di pacchetto CTP?
Il formato del pacchetto CTP non è supportato in servizi cloud (supporto esteso). Tuttavia, consente un limite di dimensioni del pacchetto maggiore di 800 MB

## <a name="migration"></a>Migrazione

### <a name="will-cloud-services-extended-support-mitigate-the-failures-due-to-allocation-failures"></a>I servizi cloud (supporto esteso) mitigano gli errori causati da errori di allocazione?
No, le distribuzioni del servizio cloud (supporto esteso) sono associate a un cluster come servizi cloud (versione classica). Gli errori di allocazione continueranno pertanto a esistere se il cluster è pieno. 

### <a name="when-do-i-need-to-migrate"></a>Quando è necessario eseguire la migrazione? 
La stima del tempo necessario e della migrazione della complessità dipende da un intervallo di variabili. La pianificazione è il passaggio più efficace per comprendere l'ambito di lavoro, i blocchi e la complessità della migrazione.

## <a name="networking"></a>Rete 

### <a name="why-cant-i-create-a-deployment-without-virtual-network"></a>Perché non è possibile creare una distribuzione senza rete virtuale?
Le reti virtuali sono una risorsa obbligatoria per qualsiasi distribuzione in Azure Resource Manager. La distribuzione dei servizi cloud (supporto esteso) deve risiedere all'interno di una rete virtuale. 

### <a name="why-am-i-now-seeing-so-many-networking-resources"></a>Perché sono ora presenti così tante risorse di rete? 
In Azure Resource Manager, i componenti della distribuzione dei servizi cloud (supporto esteso) vengono esposti come una risorsa per una migliore visibilità e un maggiore controllo. Gli stessi tipi di risorse venivano usati in servizi cloud (versione classica) ma erano semplicemente nascosti. Un esempio di risorsa di questo tipo è la Load Balancer pubblica, che ora è una risorsa di "sola lettura" esplicita creata automaticamente dalla piattaforma

### <a name="what-restrictions-apply-for-a-subnet-with-respective-to-cloud-services-extended-support"></a>Quali restrizioni si applicano a una subnet con i rispettivi servizi cloud (supporto esteso)?
Una subnet contenente le distribuzioni di servizi cloud (supporto esteso) non può essere condivisa con distribuzioni di altri prodotti di calcolo, ad esempio macchine virtuali, set di scalabilità di macchine virtuali, Service Fabric e così via.

### <a name="what-ip-allocation-methods-are-supported-on-cloud-services-extended-support"></a>Quali metodi di allocazione IP sono supportati nei servizi cloud (supporto esteso)?
Servizi cloud (supporto esteso) supporta i metodi di allocazione IP statici & dinamici. Agli indirizzi IP statici viene fatto riferimento come IP riservati nel file cscfg.

### <a name="why-am-i-getting-charged-for-ip-addresses"></a>Perché vengono addebitati gli indirizzi IP?
Ai clienti viene addebitato l'uso di indirizzi IP nei servizi cloud (supporto esteso), così come gli utenti vengono fatturati per gli indirizzi IP associati alle macchine virtuali. 

### <a name="can-i-use-a-dns-name-with-cloud-services-extended-support"></a>È possibile usare un nome DNS con servizi cloud (supporto esteso)? 
Sì. Ai servizi cloud (supporto esteso) può essere assegnato anche un nome DNS. Con Azure Resource Manager, l'etichetta DNS è una proprietà facoltativa dell'indirizzo IP pubblico assegnato al servizio cloud. Il formato del nome DNS per le distribuzioni basate su Azure Resource Manager è `<userlabel>.<region>.cloudapp.azure.com`

### <a name="can-i-update-or-change-the-virtual-network-reference-for-an-existing-cloud-service-extended-support"></a>È possibile aggiornare o modificare il riferimento alla rete virtuale per un servizio cloud esistente (supporto esteso)? 
No. Il riferimento alla rete virtuale è obbligatorio durante la creazione di un servizio cloud. Per un servizio cloud esistente, il riferimento alla rete virtuale non può essere modificato. Lo spazio degli indirizzi della rete virtuale può essere modificato usando le API di VNet. 

## <a name="certificates--key-vault"></a>Certificati & Key Vault

### <a name="why-do-i-need-to-manage-my-certificates-on-cloud-services-extended-support"></a>Perché è necessario gestire i certificati nei servizi cloud (supporto esteso)?
Servizi cloud (supporto esteso) ha adottato lo stesso processo di altre offerte di calcolo in cui i certificati si trovano all'interno di insiemi di credenziali delle chiavi gestite dal cliente. Questo consente ai clienti di avere il controllo completo sui segreti & certificati. 

### <a name="can-i-use-one-key-vault-for-all-my-deployments-in-all-regions"></a>È possibile usare un Key Vault per tutte le distribuzioni in tutte le aree?
No. Key Vault è una risorsa a livello di area e i clienti hanno bisogno di un Key Vault in ogni area. Tuttavia, è possibile usare una Key Vault per tutte le distribuzioni all'interno di una determinata area.

## <a name="next-steps"></a>Passaggi successivi
Per iniziare a usare i servizi cloud (supporto esteso), vedere [distribuire un servizio cloud (supporto esteso) con PowerShell](deploy-powershell.md)
