---
title: Funzionalità di anteprima di Azure Firewall Premium
description: Azure Firewall Premium è un servizio di sicurezza di rete gestito e basato sul cloud che protegge le risorse della rete virtuale di Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: 22ee920c38a8cacaf37a1e3a6cfc5165dfa0bb44
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419950"
---
# <a name="azure-firewall-premium-preview-features"></a>Funzionalità di anteprima di Azure Firewall Premium

Logo di certificazione:::image type="content" source="media/premium-features/pci-logo.png" alt-text="PCI" border="false"::: del :::image type="content" source="media/premium-features/icsa-cert-firewall-small.png" alt-text="logo ICSA" border="false":::


> [!IMPORTANT]
> Azure Firewall Premium è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Azure Firewall Premium Preview è un firewall di nuova generazione con funzionalità necessarie per ambienti altamente sensibili e regolamentati.

:::image type="content" source="media/premium-features/premium-overview.png" alt-text="Diagramma della panoramica Premium di Azure firewall":::

Azure Firewall Premium Preview usa criteri firewall, una risorsa globale che può essere usata per gestire i firewall in modo centralizzato usando gestione firewall di Azure. A partire da questa versione, tutte le nuove funzionalità possono essere configurate solo tramite criteri firewall. Le regole del firewall (classiche) continuano a essere supportate e possono essere usate per configurare le funzionalità firewall standard esistenti.  I criteri firewall possono essere gestiti in modo indipendente o con gestione firewall di Azure. Un criterio firewall associato a un singolo firewall non prevede alcun addebito.

> [!IMPORTANT]
> Attualmente lo SKU Premium del firewall non è supportato nelle distribuzioni di hub sicure e nelle configurazioni del tunnel forzato. 

Azure Firewall Premium Preview include le funzionalità seguenti:

- **Ispezione TLS** : consente di decrittografare il traffico in uscita, elaborare i dati e quindi crittografare i dati e inviarli alla destinazione.
- **IDP** : un sistema di rilevamento e prevenzione delle intrusioni nella rete consente di monitorare le attività di rete per attività dannose, registrare informazioni su questa attività, segnalarle e, facoltativamente, provare a bloccarlo.
- **Filtro URL** : estende la funzionalità di filtro FQDN del firewall di Azure per considerare un intero URL. Ad esempio, `www.contoso.com/a/c` anziché `www.contoso.com` .
- **Categorie Web** : gli amministratori possono consentire o negare l'accesso utente alle categorie di siti Web, ad esempio siti Web per il gioco d'azzardo, social media e altri.


## <a name="tls-inspection"></a>Ispezione TLS

Azure Firewall Premium termina le connessioni in uscita e TLS ad ovest. L'ispezione TLS in ingresso è supportata con [applicazione Azure gateway](../web-application-firewall/ag/ag-overview.md) che consente la crittografia end-to-end. Il firewall di Azure esegue le funzioni di sicurezza a valore aggiunto necessarie e crittografa di nuovo il traffico inviato alla destinazione originale.

> [!TIP]
> TLS 1,0 e 1,1 saranno deprecati e non saranno supportati. Le versioni TLS 1,0 e 1,1 di TLS/Secure Sockets Layer (SSL) sono risultate vulnerabili e, mentre attualmente lavorano per consentire la compatibilità con le versioni precedenti, non sono consigliate. Eseguire la migrazione a TLS 1,2 il prima possibile.

Per altre informazioni sui requisiti del certificato della CA intermedia di Azure Firewall Premium Preview, vedere [certificati di anteprima Premium del firewall di Azure](premium-certificates.md).

## <a name="idps"></a>IDP

Un sistema di rilevamento e prevenzione delle intrusioni nella rete consente di monitorare la rete per attività dannose, registrare informazioni su questa attività, segnalarla e, facoltativamente, provare a bloccarlo. 

Azure Firewall Premium Preview offre IDP basati sulla firma per consentire il rilevamento rapido degli attacchi cercando modelli specifici, ad esempio sequenze di byte nel traffico di rete o sequenze di istruzioni dannose note usate da malware. Le firme IDP sono completamente gestite e aggiornate in modo continuo.

Le firme/RuleSet del firewall di Azure includono:
- Un'enfasi sull'impronta digitale effettiva di malware, comandi e controlli, exploit kit e nell'attività selvaggia dannosa persa dai tradizionali metodi di prevenzione.
- Più di 35.000 regole in oltre 50 categorie.
    - Le categorie includono il comando e il controllo malware, gli attacchi DoS, le botnet, gli eventi informativi, gli exploit, le vulnerabilità, i protocolli di rete SCADA, l'attività del kit exploit e altro ancora.
- dal 20 al 40 + le nuove regole vengono rilasciate ogni giorno.
- Classificazione con falsi positivi bassa usando la sandbox di malware all'avanguardia e il ciclo di feedback di rete del sensore globale.

IDP consente di rilevare gli attacchi in tutte le porte e protocolli per il traffico non crittografato. Tuttavia, quando è necessario controllare il traffico HTTPS, il firewall di Azure può usare la funzionalità di ispezione TLS per decrittografare il traffico e rilevare meglio le attività dannose.  

L'elenco di bypass degli IDP consente di non filtrare il traffico verso uno degli indirizzi IP, gli intervalli e le subnet specificati nell'elenco di bypass. 

## <a name="url-filtering"></a>Filtro degli URL

Il filtro URL estende la funzionalità di filtro FQDN del firewall di Azure per considerare un intero URL. Ad esempio, `www.contoso.com/a/c` anziché `www.contoso.com` .  

Il filtro URL può essere applicato sia al traffico HTTP che HTTPS. Quando viene ispezionato il traffico HTTPS, Azure Firewall Premium Preview può usare la funzionalità di ispezione TLS per decrittografare il traffico ed estrarre l'URL di destinazione per verificare se l'accesso è consentito. L'ispezione TLS richiede il consenso esplicito a livello di regola dell'applicazione. Una volta abilitata, è possibile usare gli URL per filtrare con HTTPS. 

## <a name="web-categories"></a>Categorie Web

Le categorie Web consentono agli amministratori di consentire o negare l'accesso degli utenti alle categorie di siti Web, ad esempio siti Web di gioco, siti Web di social media e altri. Le categorie Web saranno incluse anche in Azure firewall standard, ma verranno ottimizzate in Azure Firewall Premium Preview. Rispetto alla funzionalità delle categorie Web dello SKU standard che corrisponde alla categoria basata su un nome di dominio completo, lo SKU Premium corrisponde alla categoria in base all'intero URL per il traffico HTTP e HTTPS. 

Ad esempio, se il firewall di Azure intercetta una richiesta HTTPS per `www.google.com/news` , è prevista la categorizzazione seguente: 

- Standard firewall: verrà esaminata solo la parte FQDN, quindi `www.google.com` verrà categorizzata come *motore di ricerca*. 

- Firewall Premium: verrà esaminato l'URL completo, quindi `www.google.com/news` verranno categorizzate come *notizie*.

Le categorie sono organizzate in base alla gravità sotto **responsabilità**, **larghezza di banda elevata**, **uso aziendale**, **perdita di produttività**, **navigazione generale** e senza **categoria**.

### <a name="category-exceptions"></a>Eccezioni di categoria

È possibile creare eccezioni alle regole di categoria Web. Creare una raccolta di regole Consenti o nega separata con una priorità più alta all'interno del gruppo di raccolta regole. Ad esempio, è possibile configurare una raccolta di regole che consente la `www.linkedin.com` priorità 100, con una raccolta di regole che nega la **rete sociale** con priorità 200. Verrà creata l'eccezione per la categoria Web di **social networking** predefinita.

### <a name="categorization-change"></a>Modifica della categorizzazione

È possibile richiedere una modifica di categorizzazione se:

 - si pensi che un FQDN o un URL debba trovarsi in una categoria diversa 
 
oppure 

- avere una categoria consigliata per un FQDN o un URL senza categoria

È possibile inviare una richiesta all'indirizzo [https://aka.ms/azfw-webcategories-request](https://aka.ms/azfw-webcategories-request) .
 
## <a name="supported-regions"></a>Aree supportate

Azure Firewall Premium Preview è supportato nelle aree seguenti:

- Europa occidentale (pubblico/Europa)
- Stati Uniti orientali (pubblico/Stati Uniti)
- Australia orientale (pubblico/Australia)
- Asia sudorientale (pubblico/Asia Pacifico)
- Regno Unito meridionale (pubblico/Regno Unito)
- Europa settentrionale (pubblica/Europa)
- Stati Uniti orientali 2 (pubblico/Stati Uniti)
- Stati Uniti centro-meridionali (pubblici/Stati Uniti)
- Stati Uniti occidentali 2 (pubblico/Stati Uniti)
- Stati Uniti occidentali (pubblico/Stati Uniti)
- Stati Uniti centrali (pubblico/Stati Uniti)
- Stati Uniti centro-settentrionali (pubblico/Stati Uniti)
- Giappone orientale (pubblico/Giappone)
- Asia orientale (pubblico/Asia Pacifico)
- Canada centrale (pubblico/Canada)
- Francia centrale (pubblico/Francia)
- Sudafrica settentrionale (pubblico/Sudafrica)
- Emirati Arabi Uniti settentrionali (pubblico/UAE)
- Svizzera settentrionale (pubblico/Svizzera)
- Brasile meridionale (pubblico/Brasile)
- Norvegia orientale (pubblico/Norvegia)
- Australia centrale (pubblico/Australia)
- Australia centrale 2 (pubblico/Australia)
- Australia sud-orientale (pubblico/Australia)
- Canada orientale (pubblico/Canada)
- Stati Uniti centrali EUAP (pubblico/canarino (Stati Uniti))
- Francia meridionale (pubblico/Francia)
- Giappone occidentale (pubblico/Giappone)
- Corea meridionale (pubblico/Corea)
- Emirati Arabi Uniti centrali (pubblico/UAE)
- Regno Unito occidentale (pubblico/Regno Unito)
- Stati Uniti centro-occidentali (pubblico/Stati Uniti)
- India occidentale (pubblico/India)


## <a name="known-issues"></a>Problemi noti

Azure Firewall Premium Preview presenta i seguenti problemi noti:

|Problema  |Descrizione  |Strategia di riduzione del rischio  |
|---------|---------|---------|
|Controllo TLS supportato solo sulla porta HTTPS standard|Il controllo TLS supporta solo HTTPS/443. |Nessuna. Le altre porte saranno supportate in GA.|
|Supporto di ESNI per la risoluzione FQDN in HTTPS|Il SNI crittografato non è supportato nell'handshake HTTPS.|Attualmente solo Firefox supporta ESNI tramite la configurazione personalizzata. La soluzione alternativa suggerita è disabilitare questa funzionalità.|
|Certificati client (TLS)|I certificati client vengono utilizzati per creare un trust di identità reciproca tra il client e il server. I certificati client vengono usati durante una negoziazione TLS. Il firewall di Azure rinegozia una connessione con il server e non ha accesso alla chiave privata dei certificati client.|nessuno|
|QUIC/HTTP3|QUIC è la nuova versione principale di HTTP. Si tratta di un protocollo basato su UDP su 80 (piano) e 443 (SSL). L'ispezione FQDN/URL/TLS non sarà supportata.|Configurare il passaggio di UDP 80/443 come regole di rete.|
|Hub sicuro e tunneling forzato non supportati in Premium|Attualmente lo SKU Premium del firewall non è supportato nelle distribuzioni di hub sicure e nelle configurazioni del tunnel forzato.|Correzione pianificata per GA.|
Certificati firmati dal cliente non attendibili|I certificati firmati dal cliente non sono considerati attendibili dal firewall dopo la ricezione da un server Web basato su Intranet.|Correzione pianificata per GA.
|Indirizzi IP di origine e di destinazione errati negli avvisi per gli IDP con ispezione TLS.|Quando si Abilita l'ispezione TLS e IDP emette un nuovo avviso, l'indirizzo IP di origine/destinazione visualizzato è errato (viene visualizzato l'indirizzo IP interno anziché l'indirizzo IP originale).|Correzione pianificata per GA.|
|Indirizzo IP di origine errato negli avvisi con IDP per HTTP (senza ispezione TLS).|Quando è in uso il traffico HTTP in formato testo normale e IDP emette un nuovo avviso e la destinazione è un indirizzo IP pubblico, l'indirizzo IP di origine visualizzato è errato (viene visualizzato l'indirizzo IP interno anziché l'indirizzo IP originale).|Correzione pianificata per GA.|
|Propagazione certificati|Dopo che un certificato CA è stato applicato al firewall, potrebbero essere necessari tra 5-10 minuti affinché il certificato abbia effetto.|Correzione pianificata per GA.|
|Bypass IDP|Il bypass IDP non funziona per il traffico con terminazione TLS e i gruppi di indirizzi IP di origine e di origine non sono supportati.|Correzione pianificata per GA.|
|Supporto di TLS 1,3|TLS 1,3 è parzialmente supportato. Il tunnel TLS dal client al firewall si basa su TLS 1,2 e dal firewall al server Web esterno si basa su TLS 1,3.|È in corso l'analisi degli aggiornamenti.|




## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sui certificati Premium di Azure firewall](premium-certificates.md)
- [Distribuire e configurare Azure Firewall Premium Preview](premium-deploy.md)
- [Eseguire la migrazione all'anteprima Premium di Azure firewall](premium-migrate.md)
