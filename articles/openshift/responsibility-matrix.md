---
title: Azure Red Hat OpenShift di assegnazione di responsabilità
description: Informazioni sulla proprietà delle responsabilità per il funzionamento di un cluster Azure Red Hat OpenShift cluster
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 4/12/2021
author: sakthi-vetrivel
ms.author: suvetriv
keywords: aro, openshift, az aro, red hat, cli, RACI, support
ms.openlocfilehash: 364b0a04e3394efcacba00c538218ecee2355f89
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495038"
---
# <a name="overview-of-responsibilities-for-azure-red-hat-openshift"></a>Panoramica delle responsabilità per Azure Red Hat OpenShift

Questo documento illustra le responsabilità di Microsoft, Red Hat e dei clienti per Azure Red Hat OpenShift cluster. Per altre informazioni sui Azure Red Hat OpenShift e sui relativi componenti, vedere la definizione Azure Red Hat OpenShift servizio.

Mentre Microsoft e Red Hat gestiscono il Azure Red Hat OpenShift, il cliente condivide la responsabilità delle funzionalità del cluster. Anche Azure Red Hat OpenShift i cluster sono ospitati nelle risorse di Azure nelle sottoscrizioni di Azure dei clienti, l'accesso viene eseguito in modalità remota. La piattaforma sottostante e la sicurezza dei dati sono di proprietà di Microsoft e Red Hat.

## <a name="overview"></a>Panoramica
<table>
  <tr>
   <td><strong>Risorsa</strong>
   </td>
   <td><strong><a href="#incident-and-operations-management">Gestione degli eventi imprevisti e delle operazioni</a></strong>
   </td>
   <td><strong><a href="#change-management">Gestione delle modifiche</a></strong>
   </td>
   <td><strong><a href="#identity-and-access-management">Gestione delle identità e degli accessi</a></strong>
   </td>
   <td><strong><a href="#security-and-regulation-compliance">Conformità alla sicurezza e alle normative</a></strong>
   </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Dati dei clienti</a>
   </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Applicazioni dei clienti</a>
   </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
  </tr>
  <tr>
   <td><a href="#customer-data-and-applications">Servizi per sviluppatori </a>
   </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
   <td>Customer </td>
  </tr>
  <tr>
   <td>Monitoraggio della piattaforma </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
  </tr>
  <tr>
   <td>Registrazione </td>
   <td>Microsoft e Red Hat </td>
   <td>Condiviso </td>
   <td>Condiviso </td>
   <td>Condiviso </td>
  </tr>
  <tr>
   <td>Rete dell'applicazione </td>
   <td>Condiviso </td>
   <td>Condiviso </td>
   <td>Condiviso </td>
   <td>Microsoft e Red Hat </td>
  </tr>
  <tr>
   <td>Rete del cluster </td>
   <td>Microsoft e Red Hat </td>
   <td>Condiviso </td>
   <td>Condiviso </td>
   <td>Microsoft e Red Hat </td>
  </tr>
  <tr>
   <td>Reti virtuali </td>
   <td>Condiviso </td>
   <td>Condiviso </td>
   <td>Condiviso </td>
   <td>Condiviso </td>
  </tr>
  <tr>
   <td>Nodi del piano di controllo </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
  </tr>
  <tr>
   <td>Nodi di lavoro </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
  </tr>
  <tr>
   <td>Versione del cluster </td>
   <td>Microsoft e Red Hat </td>
   <td>Condiviso </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
  </tr>
  <tr>
   <td>Capacity Management </td>
   <td>Microsoft e Red Hat </td>
   <td>Condiviso </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
  </tr>
  <tr>
   <td>Archiviazione virtuale </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
  </tr>
  <tr>
   <td>Infrastruttura fisica e sicurezza </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
   <td>Microsoft e Red Hat </td>
  </tr>
</table>


Tabella 1. Responsabilità in base alla risorsa


## <a name="tasks-for-shared-responsibilities-by-area"></a>Attività per le responsabilità condivise in base all'area 

### <a name="incident-and-operations-management"></a>Gestione di eventi imprevisti e operazioni 

Il cliente e Microsoft e Red Hat condividono la responsabilità del monitoraggio e della manutenzione di un Azure Red Hat OpenShift cluster. Il cliente è responsabile della gestione degli eventi imprevisti e delle operazioni dei dati [dell'applicazione del](#customer-data-and-applications) cliente e di qualsiasi rete personalizzata configurata dal cliente.

<table>
  <tr>
   <td><strong>Risorsa</strong>
   </td>
   <td><strong>Responsabilità di Microsoft e Red Hat</strong>
   </td>
   <td><strong>Responsabilità dei clienti</strong>
   </td>
  </tr>
  <tr>
   <td>Rete dell'applicazione </td>
   <td>
<ul>

<li>Monitorare i servizi di bilanciamento del carico cloud e il servizio router OpenShift nativo e rispondere agli avvisi.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Monitorare l'integrità degli endpoint del servizio di bilanciamento del carico.

<li>Monitorare l'integrità delle route dell'applicazione e degli endpoint dietro di esse.

<li>Segnalare le interruzioni a Microsoft e Red Hat.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Reti virtuali
   </td>
   <td>
<ul>

<li>Monitorare i servizi di bilanciamento del carico cloud, le subnet e i componenti cloud di Azure necessari per la rete della piattaforma predefinita e rispondere agli avvisi.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Monitorare il traffico di rete configurato facoltativamente tramite connessione da rete virtuale a rete virtuale, connessione VPN o connessione collegamento privato per potenziali problemi o minacce per la sicurezza.
</li>
</ul>
   </td>
  </tr>
</table>


Tabella 2. Responsabilità condivise per la gestione di eventi imprevisti e operazioni


### <a name="change-management"></a>Gestione delle modifiche

Microsoft e Red Hat sono responsabili dell'abilitazione delle modifiche all'infrastruttura e ai servizi del cluster che il cliente controlla, nonché della gestione delle versioni disponibili per i nodi master, i servizi di infrastruttura e i nodi di lavoro. Il cliente è responsabile dell'avvio delle modifiche dell'infrastruttura e dell'installazione e della gestione di servizi facoltativi e configurazioni di rete nel cluster, nonché di tutte le modifiche apportate ai dati e alle applicazioni dei clienti.


<table>
  <tr>
   <td><strong>Risorsa</strong>
   </td>
   <td><strong>Responsabilità di Microsoft e Red Hat</strong>
   </td>
   <td><strong>Responsabilità dei clienti</strong>
   </td>
  </tr>
  <tr>
   <td>Registrazione </td>
   <td>
<ul>

<li>Aggregare e monitorare centralmente i log di controllo della piattaforma.

<li>Fornire la documentazione per consentire al cliente di abilitare la registrazione delle applicazioni usando Log Analytics Monitoraggio di Azure per i contenitori.

<li>Fornire i log di controllo su richiesta del cliente.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Installare l'operatore facoltativo di registrazione delle applicazioni predefinito nel cluster.

<li>Installare, configurare e gestire eventuali soluzioni facoltative di registrazione delle app, ad esempio contenitori sidecar di registrazione o applicazioni di registrazione di terze parti.

<li>Ottimizzare le dimensioni e la frequenza dei log delle applicazioni generati dalle applicazioni dei clienti se influiscono sulla stabilità del cluster.

<li>Richiedere log di controllo della piattaforma tramite un caso di supporto per la ricerca di eventi imprevisti specifici.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Rete delle applicazioni
   </td>
   <td>
<ul>

<li>Configurare i servizi di bilanciamento del carico del cloud pubblico

<li>Configurare il servizio router OpenShift nativo. Consente di impostare il router come privato e di aggiungere fino a una partizione del router aggiuntiva.

<li>Installare, configurare e gestire i componenti SDN OpenShift per il traffico interno del pod predefinito.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configurare le autorizzazioni di rete del pod non predefinite per le reti di progetto e pod, l'ingresso del pod e l'uscita del pod usando gli oggetti NetworkPolicy.

<li>Richiedere e configurare eventuali servizi di bilanciamento del carico aggiuntivi per servizi specifici.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Rete del cluster
   </td>
   <td>
<ul>

<li>Configurare i componenti di gestione del cluster, ad esempio gli endpoint di servizio pubblici o privati e l'integrazione necessaria con i componenti di rete virtuale.

<li>Configurare i componenti di rete interni necessari per la comunicazione interna del cluster tra i nodi di lavoro e master.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Fornire intervalli di indirizzi IP facoltativi non predefiniti per cidr del computer, CIDR del servizio e CIDR del pod se necessario tramite OpenShift Cluster Manager quando viene effettuato il provisioning del cluster.

<li>Richiedere che l'endpoint del servizio API sia reso pubblico o privato durante la creazione del cluster o dopo la creazione del cluster tramite l'interfaccia della riga di comando di Azure.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Reti virtuali
   </td>
   <td>
<ul>

<li>Configurare e configurare i componenti di rete virtuale necessari per il provisioning del cluster, tra cui cloud privato virtuale, subnet, servizi di bilanciamento del carico, gateway Internet, gateway NAT e così via.

<li>Offrire al cliente la possibilità di gestire la connettività VPN con le risorse locali, la connettività da rete virtuale a rete virtuale e connettività collegamento privato in base alle esigenze tramite Gestione cluster OpenShift.

<li>Consentire ai clienti di creare e distribuire servizi di bilanciamento del carico cloud pubblici da usare con i servizi di bilanciamento del carico.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configurare e gestire componenti di rete cloud pubblici facoltativi, ad esempio connessione da rete virtuale a rete virtuale, connessione VPN o connessione di collegamento privato.

<li>Richiedere e configurare eventuali servizi di bilanciamento del carico aggiuntivi per servizi specifici.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Versione del cluster
   </td>
   <td>
<ul>

<li>Comunicare la pianificazione e lo stato degli aggiornamenti per le versioni secondarie e di manutenzione

<li>Pubblicare log delle modifiche e note sulla versione per gli aggiornamenti secondari e di manutenzione
</li>
</ul>
   </td>
   <td>
<ul>

<li>Avviare l'aggiornamento del cluster

<li>Testare le applicazioni dei clienti nelle versioni secondarie e di manutenzione per garantire la compatibilità
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Capacity Management
   </td>
   <td>
<ul>

<li>Monitorare l'utilizzo del piano di controllo (nodi master)

<li>Ridimensionare e/o ridimensionare i nodi del piano di controllo per mantenere la qualità del servizio

<li>Monitorare l'utilizzo delle risorse dei clienti, tra cui rete, archiviazione e capacità di calcolo. Se le funzionalità di scalabilità automatica non sono abilitate, avvisare il cliente per eventuali modifiche necessarie alle risorse del cluster, ad esempio nuovi nodi di calcolo da ridimensionare, spazio di archiviazione aggiuntivo e così via)
</li>
</ul>
   </td>
   <td>
<ul>

<li>Usare i controlli OpenShift Cluster Manager forniti per aggiungere o rimuovere altri nodi di lavoro in base alle esigenze.

<li>Rispondere alle notifiche di Microsoft e Red Hat relative ai requisiti delle risorse del cluster.
</li>
</ul>
   </td>
  </tr>
</table>


Tabella 3. Responsabilità condivise per la gestione delle modifiche


### <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso 

La gestione delle identità e degli accessi include tutte le responsabilità per garantire che solo le persone appropriate hanno accesso alle risorse del cluster, dell'applicazione e dell'infrastruttura. Sono incluse attività quali la fornitura di meccanismi di controllo di accesso, l'autenticazione, l'autorizzazione e la gestione dell'accesso alle risorse.


<table>
  <tr>
   <td><strong>Risorsa</strong>
   </td>
   <td><strong>Responsabilità di Microsoft e Red Hat</strong>
   </td>
   <td><strong>Responsabilità dei clienti</strong>
   </td>
  </tr>
  <tr>
   <td>Registrazione </td>
   <td>
<ul>

<li>Rispettare un processo di accesso interno a livelli basato su standard di settore per i log di controllo della piattaforma.

<li>Fornire funzionalità native di Controllo degli accessi in base al ruolo di OpenShift.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configurare il controllo degli accessi in base al ruolo di OpenShift per controllare l'accesso ai progetti e, per estensione, i log applicazioni di un progetto.

<li>Per soluzioni di registrazione di applicazioni personalizzate o di terze parti, il cliente è responsabile della gestione degli accessi.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Rete delle applicazioni
   </td>
   <td>
<ul>

<li>Fornire il controllo degli accessi in base al ruolo di OpenShift nativo e le funzionalità di amministrazione dedicate.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Configurare gli amministratori dedicati di OpenShift e il controllo degli accessi in base al ruolo per controllare l'accesso alla configurazione della route in base alle esigenze.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Rete del cluster
   </td>
   <td>
<ul>

<li>Fornire il controllo degli accessi in base al ruolo di OpenShift nativo e le funzionalità di amministrazione dedicate.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Gestire l'appartenenza dell'organizzazione Red Hat agli account Red Hat.

<li>Gestire gli amministratori dell'organizzazione di Red Hat per concedere l'accesso a OpenShift Cluster Manager.

<li>Configurare gli amministratori dedicati di OpenShift e il controllo degli accessi in base al ruolo per controllare l'accesso alla configurazione della route in base alle esigenze.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Reti virtuali
   </td>
   <td>
<ul>

<li>Fornire i controlli di accesso dei clienti tramite OpenShift Cluster Manager.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Gestire l'accesso utente facoltativo ai componenti del cloud pubblico tramite Gestione cluster OpenShift.
</li>
</ul>
   </td>
  </tr>
</table>


Tabella 4. Responsabilità condivise per la gestione di identità e accessi


### <a name="security-and-regulation-compliance"></a>Conformità alla sicurezza e alle normative 

La sicurezza e la conformità includono tutte le responsabilità e i controlli che garantiscono la conformità alle leggi, ai criteri e alle normative pertinenti.


<table>
  <tr>
   <td><strong>Risorsa</strong>
   </td>
   <td><strong>Responsabilità di Microsoft e Red Hat</strong>
   </td>
   <td><strong>Responsabilità dei clienti</strong>
   </td>
  </tr>
  <tr>
   <td>Registrazione </td>
   <td>
<ul>

<li>Inviare i log di controllo del cluster a Microsoft e Red Hat SIEM per analizzare gli eventi di sicurezza. Mantenere i log di controllo per un periodo di tempo definito per supportare l'analisi forense.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Analizzare i log delle applicazioni per gli eventi di sicurezza. Inviare i log dell'applicazione a un endpoint esterno tramite contenitori sidecar di registrazione o applicazioni di registrazione di terze parti se è necessaria una conservazione più lunga rispetto a quella offerta dallo stack di registrazione predefinito.
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Reti virtuali
   </td>
   <td>
<ul>

<li>Monitorare i componenti di rete virtuale per potenziali problemi e minacce alla sicurezza.

<li>Usare altri strumenti pubblici di Microsoft e Red Hat Azure per un monitoraggio e una protezione aggiuntivi.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Monitorare i componenti di rete virtuale configurati facoltativamente alla ricerca di potenziali problemi e minacce per la sicurezza.

<li>Configurare le regole del firewall o le data center necessarie.
</li>
</ul>
   </td>
  </tr>
</table>


Tabella 5. Responsabilità condivise per la conformità alla sicurezza e alle normative


## <a name="customer-responsibilities-when-using-azure-red-hat-openshift"></a>Responsabilità del cliente quando si usa Azure Red Hat OpenShift 


### <a name="customer-data-and-applications"></a>Dati e applicazioni dei clienti

Il cliente è responsabile delle applicazioni, dei carichi di lavoro e dei dati distribuiti in Azure Red Hat OpenShift. Tuttavia, Microsoft e Red Hat offrono vari strumenti per aiutare i clienti a gestire i dati e le applicazioni sulla piattaforma.


<table>
  <tr>
   <td><strong>Risorsa</strong>
   </td>
   <td><strong>Informazioni su Microsoft e Red Hat</strong>
   </td>
   <td><strong>Responsabilità dei clienti</strong>
   </td>
  </tr>
  <tr>
   <td>Dati dei clienti </td>
   <td>
<ul>

<li>Mantenere gli standard a livello di piattaforma per la crittografia dei dati in base a quanto definito dagli standard di sicurezza e conformità del settore. 

<li>Fornire componenti OpenShift per facilitare la gestione dei dati dell'applicazione, ad esempio i segreti.

<li>Abilitare l'integrazione con servizi dati di terze parti(ad esempio Azure SQL) per archiviare e gestire i dati all'esterno del cluster e/o Microsoft e Red Hat Azure.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Mantenere la responsabilità di tutti i dati dei clienti archiviati nella piattaforma e del modo in cui le applicazioni dei clienti utilizzano ed espongono questi dati.

<li>Crittografia Etcd
</li>
</ul>
   </td>
  </tr>
  <tr>
   <td>Applicazioni dei clienti
   </td>
   <td>
<ul>

<li>Effettuare il provisioning di cluster con componenti OpenShift installati in modo che i clienti possano accedere alle API OpenShift e Kubernetes per distribuire e gestire le applicazioni in contenitori.

<li>Fornire l'accesso alle API OpenShift che un cliente può usare per configurare gli operatori per aggiungere servizi community, di terze parti, Microsoft e Red Hat e Red Hat al cluster. 

<li>Fornire classi di archiviazione e plug-in per supportare volumi permanenti da usare con le applicazioni dei clienti.
</li>
</ul>
   </td>
   <td>
<ul>

<li>Mantenere la responsabilità per le applicazioni, i dati e il ciclo di vita completo di clienti e di terze parti.

<li>Se un cliente aggiunge Red Hat, community, terze parti, servizi propri o altri servizi al cluster usando operatori o immagini esterne, il cliente è responsabile di questi servizi e di lavorare con il provider appropriato (incluso Red Hat) per risolvere eventuali problemi.

<li>Usare gli strumenti e le funzionalità forniti per <a href="https://docs.openshift.com/dedicated/4/architecture/understanding-development.html#application-types">configurare e distribuire</a>; <a href="https://docs.openshift.com/dedicated/4/applications/deployments/deployment-strategies.html">mantenere aggiornati</a>; <a href="https://docs.openshift.com/dedicated/4/applications/working-with-quotas.html">configurare le richieste e i limiti delle risorse;</a> <a href="https://docs.openshift.com/dedicated/4/getting_started/scaling-your-cluster.html">ridimensionare il cluster in modo che abbia risorse sufficienti per eseguire le app;</a> <a href="https://docs.openshift.com/dedicated/4/administering_a_cluster/dedicated-admin-role.html#dedicated-admin-granting-permissions_dedicated-administrator">impostare le autorizzazioni</a>; integrare con altri servizi; <a href="https://docs.openshift.com/dedicated/4/openshift_images/images-understand.html">gestire i flussi di immagini o i modelli distribuiti dal cliente;</a> <a href="https://docs.openshift.com/dedicated/4/cloud_infrastructure_access/dedicated-aws-private-cluster.html">servire esternamente</a>; salvare, eseguire il backup e ripristinare i dati; e in caso contrario gestire i carichi di lavoro a disponibilità elevata e resilienti.

<li>Mantenere la responsabilità del monitoraggio delle applicazioni eseguite Azure Red Hat OpenShift; inclusa l'installazione e il funzionamento del software per raccogliere metriche e creare avvisi.
</li>
</ul>
   </td>
  </tr>
</table>


Tabella 7. Responsabilità dei clienti per i dati dei clienti, le applicazioni e i servizi dei clienti