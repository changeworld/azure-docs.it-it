---
title: Versioni Service Fabric azure
description: Note sulla versione per Azure Service Fabric. Include informazioni sulle funzionalità e i miglioramenti più recenti in Service Fabric.
ms.date: 04/13/2021
ms.topic: conceptual
hide_comments: true
hideEdit: true
ms.openlocfilehash: 2a035f531e03dc42e8be4f3dab403406eb7c8f14
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518627"
---
# <a name="service-fabric-releases"></a>Service Fabric versioni

Questo articolo fornisce altre informazioni sulle versioni più recenti e sugli aggiornamenti per il runtime Service Fabric SDK.

Sono disponibili anche le risorse seguenti:
- <a href="https://github.com/Azure/Service-Fabric-Troubleshooting-Guides" target="blank">Guide alla risoluzione dei problemi</a> 
- <a href="https://github.com/Azure/service-fabric-issues" target="blank">Rilevamento dei problemi</a> 
- <a href="/azure/service-fabric/service-fabric-support" target="blank">Opzioni di supporto</a> 
- <a href="/azure/service-fabric/service-fabric-versions" target="blank">Versioni supportate</a> 
- <a href="https://azure.microsoft.com/resources/samples/?service=service-fabric&sort=0" target="blank">Esempi di codice</a>


## <a name="service-fabric-80"></a>Service Fabric 8.0

Siamo entusiasti di annunciare che la versione 8.0 del runtime di Service Fabric ha iniziato l'implementazione nelle varie aree di Azure insieme agli strumenti e agli aggiornamenti dell'SDK. Gli aggiornamenti per .NET SDK, Java SDK e Service Fabric runtime sono disponibili tramite Installazione guidata piattaforma Web, pacchetti NuGet e repository Maven.

### <a name="key-announcements"></a>Annunci chiave

- **Disponibilità generale** del supporto per .NET 5 per Windows
- **Disponibilità generale** di [NodeTypes senza stato](https://docs.microsoft.com/azure/service-fabric/service-fabric-stateless-node-types)
- Possibilità di spostare istanze del servizio senza stato
- Possibilità di aggiungere DefaultLoad con parametri nel manifesto dell'applicazione
- Per gli aggiornamenti della replica singleton: possibilità di definire alcune impostazioni a livello di cluster a livello di applicazione
- Possibilità di posizionamento intelligente in base ai tag di nodo
- Possibilità di definire la soglia percentuale di nodi non integri che influiscono sull'integrità del cluster
- Possibilità di eseguire query su servizi caricati di livello superiore
- Possibilità di aggiungere un nuovo intervallo per i nuovi codici di errore
- Possibilità di contrassegnare l'istanza del servizio come completata
- Supporto per il modello di distribuzione basato su onde per gli aggiornamenti automatici
- Aggiunta del probe di idoneità per le applicazioni in contenitori
- Abilitare UseSeparateSecondaryMoveCost su true per impostazione predefinita
- Correzione di StateManager per rilasciare il riferimento non appena possibile
- Bloccare la rimozione di Central Secret Service durante l'archiviazione dei segreti utente


### <a name="service-fabric-80-releases"></a>Service Fabric 8.0
| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 8 aprile 2021 | [Azure Service Fabric 8.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-8-0-release/ba-p/2260016)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_80.md)|


## <a name="previous-versions"></a>Versioni precedenti

### <a name="service-fabric-72"></a>Service Fabric 7.2

#### <a name="key-announcements"></a>Annunci principali

- **Anteprima:** [**Service Fabric i cluster gestiti**](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-managed-clusters-are-now-in-public-preview/ba-p/1721572) sono ora in anteprima pubblica. Service Fabric cluster gestiti mirano a semplificare la distribuzione e la gestione del cluster incapsulando le risorse sottostanti che costituiscono un cluster Service Fabric in una singola risorsa arm. Per altri dettagli, vedere panoramica [Service Fabric cluster gestito.](./overview-managed-cluster.md)
- **Anteprima:** [**il supporto dei servizi senza stato con un numero di istanze**](./service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) maggiore del numero di nodi è ora disponibile in anteprima pubblica. Un criterio di posizionamento consente la creazione di più istanze senza stato di una partizione in un nodo.
- [**FabricObserver (FO) 3.0**](https://aka.ms/sf/fabricobserver) è ora disponibile.
    - È ora possibile eseguire FabricObserver nei cluster Linux e Windows.
    - È ora possibile creare plug-in observer personalizzati. Per informazioni [dettagliate e codice, vedere il file Leggimi](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Plugins.md) dei [plug-in](https://github.com/microsoft/service-fabric-observer/tree/master/SampleObserverPlugin) e il progetto di plug-in di esempio.
    - È ora possibile modificare qualsiasi impostazione dell'osservatore tramite l'aggiornamento dei parametri dell'applicazione. Ciò significa che non è più necessario ridistribuire fo per modificare impostazioni specifiche dell'osservatore. Vedere [l'esempio](https://github.com/microsoft/service-fabric-observer/blob/master/Documentation/Using.md#parameterUpdates).
- [**Supporto per le immagini del contenitore OneBox di Ubuntu 18.04**](https://hub.docker.com/_/microsoft-service-fabric-onebox).
- **Anteprima:** [ **Informazioni di riferimento su KeyVault per Service Fabric applicazioni **supportano SOLO segreti con controllo delle versioni.** I segreti senza versioni non sono supportati.**](./service-fabric-keyvault-references.md)
- SF SDK richiede l'aggiornamento più recente di Visual Studio 2019 16.7.6 o 16.8 Preview 4 per poter creare nuovi progetti .NET Framework senza stato/con stato/actors. Se non si ha l'aggiornamento di Visual Studio più recente, dopo aver creato il progetto di servizio, usare Gestione pacchetti per installare Microsoft.ServiceFabric.Services (versione 4.2.x) per i progetti con o senza stato e Microsoft.ServiceFabric.Actors (versione 4.2.x) per i progetti actor da nuget.org.
- **RunToCompletion:** Service Fabric supporta il concetto di esecuzione fino al completamento per gli eseguibili guest. Con questo aggiornamento, una volta completata l'esecuzione della replica, verranno rilasciate le risorse del cluster allocate a questa replica.
- [**Il supporto per la governance delle risorse è stato migliorato,**](./service-fabric-resource-governance.md)consentendo le richieste e limitando le specifiche per le risorse di CPU e memoria.

#### <a name="service-fabric-72-releases"></a>Service Fabric 7.2
| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 21 ottobre 2020 | [Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-release/ba-p/1805653)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72-releasenotes.md)|
| 9 novembre 2020 | [Versione di Service Fabric 7.2 secondi di Azure](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-second-refresh-release/ba-p/1874738) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU2-releasenotes.md) |
| 10 novembre 2020  | Terzo aggiornamento di Azure Service Fabric 7.2 | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU3-releasenotes.md) |
| 2 dicembre 2020 | [Versione di Service Fabric 7.2 di Azure](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fourth-refresh-release/ba-p/1950584) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU4.md)
| 25 gennaio 2021 | [Quinta versione di aggiornamento di Azure Service Fabric 7.2](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-2-fifth-refresh-release/ba-p/2096575) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU5-ReleaseNotes.md)
| 17 febbraio 2021 | [Azure Service Fabric 7.2 Sixth Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-sixth-refresh-release/ba-p/2144685) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU6-ReleaseNotes.md)
| 10 marzo 2021 | [Azure Service Fabric 7.2 Rilascio del settimo aggiornamento](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-seventh-refresh-release/ba-p/2201100) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-72CU7-releasenotes.md)


### <a name="service-fabric-71"></a>Service Fabric 7.1

A causa dell'attuale emergenza di COVID-19 e tenendo conto delle sfide affrontate dai clienti, microsoft rende disponibile la versione 7.1, ma non aggiorna automaticamente i cluster impostati per ricevere gli aggiornamenti automatici. Gli aggiornamenti automatici vengono sospendere fino a nuovo avviso per garantire che i clienti possano applicare gli aggiornamenti quando più appropriati, per evitare interruzioni impreviste.

Sarà possibile eseguire l'aggiornamento alla versione 7.1 tramite il portale di Azure [o](./service-fabric-cluster-upgrade-version-azure.md#manual-upgrades-with-azure-portal) tramite una [Azure Resource Manager distribuzione .](./service-fabric-cluster-upgrade-version-azure.md#resource-manager-template)

Service Fabric i cluster con aggiornamenti automatici abilitati inizieranno a ricevere automaticamente l'aggiornamento 7.1 dopo la ripresa della procedura di implementazione standard. Verrà fornito un altro annuncio prima dell'inizio dell'implementazione standard [nel sito Service Fabric Tech Community.](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)
Sono stati pubblicati anche aggiornamenti fino alla data di fine del supporto per le versioni principali a partire dalla versione 6.5 fino alla 7.1 [qui.](./service-fabric-versions.md) 

#### <a name="key-announcements"></a>Annunci principali

- **Disponibilità generale** delle [ **Service Fabric gestite per le Service Fabric applicazioni**](./concepts-managed-identity.md)
- [**Supporto per Ubuntu 18.04**](./service-fabric-tutorial-create-vnet-and-linux-cluster.md)
 - Anteprima: Supporto dei dischi del sistema operativo [**phemeral del set**](./service-fabric-cluster-azure-deployment-preparation.md#use-ephemeral-os-disks-for-virtual-machine-scale-sets)di scalabilità di macchine virtuali **: i dischi del sistema operativo phemeral vengono creati nella macchina virtuale locale e non vengono salvati in Archiviazione di Azure. Sono consigliati per tutti i Service Fabric di nodo (primario e secondario), perché rispetto ai tradizionali dischi persistenti del sistema operativo, i dischi del sistema operativo phemeral:
      -  Ridurre la latenza di lettura/scrittura nel disco del sistema operativo
      -  Abilitare operazioni di reimpostazione/ri-immagine più veloci per la gestione dei nodi
      -  Ridurre i costi complessivi (i dischi sono gratuiti e non comportano costi di archiviazione aggiuntivi)
- Supporto per la dichiarazione di [**certificati dell'endpoint di servizio Service Fabric applicazioni in base al nome comune del soggetto**](./service-fabric-service-manifest-resources.md).
- [**Supporto per i probe di integrità per i servizi in contenitori:**](./probes-codepackage.md)supporto per il meccanismo di probe di liveness per le applicazioni in contenitori. Il probe di liveness consente di annunciare l'efficacia dell'applicazione in contenitori e, quando non rispondono in modo orario, verrà riavviato. 
- [**Supporto per i pacchetti di codice dell'inizializzatore**](./initializer-codepackages.md) [per contenitori](./service-fabric-containers-overview.md) e [applicazioni eseguibili guest.](./service-fabric-guest-executables-introduction.md) Ciò consente l'esecuzione di pacchetti di codice (ad esempio contenitori), in un ordine specificato, per eseguire l'inizializzazione del pacchetto del servizio.
- **FabricObserver e ClusterObserver** sono applicazioni senza stato che acquisiscono Service Fabric telemetria correlati a diversi aspetti di un cluster sf. Entrambe queste applicazioni sono pronte per la distribuzione nei cluster di produzione Windows per acquisire dati di telemetria dettagliati con il supporto implementato per ApplicationInsights, EventSource e LogAnalytics.
    - [**FabricObserver (FO) 2.0:**](https://github.com/microsoft/service-fabric-observer)viene eseguito in tutti i nodi, genera eventi di integrità e genera dati di telemetria quando vengono raggiunte le soglie di utilizzo delle risorse configurate dall'utente. Questa versione contiene diversi miglioramenti per il monitoraggio, la gestione dei dati, i dettagli degli eventi di integrità e i dati di telemetria strutturati.
     - [**ClusterObserver (CO) 1.1:**](https://github.com/microsoft/service-fabric-observer/tree/master/ClusterObserver) viene eseguito in un nodo e acquisisce i dati di telemetria sull'integrità a livello di cluster. In questa versione, ClusterObserver monitora anche lo stato del nodo e genera dati di telemetria quando il nodo è in stato di in disattivazione/disabilitazione/disabilitato per un periodo di tempo superiore a quello specificato dall'utente.

#### <a name="improve-application-life-cycle-experience"></a>Migliorare l'esperienza del ciclo di vita dell'applicazione

- **[Anteprima:Svuotamento richieste:](./service-fabric-application-upgrade-advanced.md#avoid-connection-drops-during-stateless-service-planned-downtime)** durante la manutenzione pianificata del servizio, ad esempio gli aggiornamenti del servizio o la disattivazione del nodo, si vuole consentire ai servizi di svuotare normalmente le connessioni. Questa funzionalità aggiunge una durata del ritardo di chiusura dell'istanza nella configurazione del servizio. Durante le operazioni pianificate, SF rimuoverà l'indirizzo del servizio dall'individuazione e quindi attenderà questa durata prima di arrestare il servizio.
- **[Rilevamento e bilanciamento automatici dei sottocluster:](./cluster-resource-manager-subclustering.md)** il cluster secondario si verifica quando i servizi con vincoli di posizionamento diversi hanno una metrica [di carico comune.](./service-fabric-cluster-resource-manager-metrics.md) Se il carico sui diversi set di nodi differisce in modo significativo, il Service Fabric gestione risorse cluster ritiene che il cluster sia sbilanciato, anche quando ha il miglior equilibrio possibile a causa dei vincoli di posizionamento. Di conseguenza, tenta di ribilanciare il cluster, causando potenzialmente movimenti del servizio non necessari (poiché lo "sbilanciamento" non può essere sostanzialmente migliorato). A partire da questa versione, il gestione risorse cluster tenterà ora di rilevare automaticamente questi tipi di configurazioni e di capire quando è possibile risolvere lo squilibrio tramite lo spostamento e quando invece deve lasciare le cose in pace perché non è possibile migliorare in modo sostanziale.  
- [**Costo di spostamento**](./service-fabric-cluster-resource-manager-movement-cost.md)diverso per le repliche secondarie: è stato introdotto il nuovo valore di costo di spostamento VeryHigh che offre maggiore flessibilità in alcuni scenari per definire se usare un costo di spostamento separato per le repliche secondarie.
- Meccanismo [**probe liveness**](./probes-codepackage.md) abilitato per le applicazioni in contenitori. Liveness Probe consente di annunciare la disponibilità dell'applicazione in contenitori e, quando non rispondono in modo orario, verrà riavviato.
- [**Esegui fino al completamento/una volta per i servizi**](./run-to-completion.md)**

#### <a name="image-store-improvements"></a>archivio immagini miglioramenti
 - Service Fabric 7.1 usa il trasporto personalizzato per proteggere il trasferimento **di file tra nodi per impostazione predefinita.** La dipendenza dalla condivisione file SMB viene rimossa dalla versione 7.1. Le condivisioni file SMB protette esistono ancora nei nodi che contengono una replica del servizio archivio immagini per la scelta del cliente di rifiutare esplicitamente l'impostazione predefinita e per l'aggiornamento e il downgrade alla versione precedente.
       
 #### <a name="reliable-collections-improvements"></a>Miglioramenti di Reliable Collections

- Solo in memoria è possibile archiviare il supporto per i servizi con stato che usano [**Reliable Collections:**](./service-fabric-work-with-reliable-collections.md#volatile-reliable-collections)le raccolte Reliable Collections volatili consentono di rendere persistenti i dati su disco per la durabilità in caso di interruzioni su larga scala. Possono essere usate per carichi di lavoro come la cache replicata, ad esempio, in cui può essere tollerata una perdita occasionale di dati. In base alle limitazioni e alle restrizioni delle raccolte [Reliable Collections](./service-fabric-reliable-services-reliable-collections-guidelines.md#volatile-reliable-collections)volatili, è consigliabile usare questa opzione per i carichi di lavoro che non necessitano di persistenza, per i servizi che gestiscono le rare occasioni di perdita del quorum.
- [**Anteprima: Service Fabric Explorer di Backup:**](https://github.com/microsoft/service-fabric-backup-explorer)per semplificare la gestione dei backup di Reliable Collections per Service Fabric applicazioni con stato, Service Fabric Explorer di Backup consente agli utenti di
    - Controllare ed esaminare il contenuto delle raccolte Reliable Collections,
    - Aggiornare lo stato corrente a una vista coerente
    - Creare il backup dello snapshot corrente delle raccolte Reliable Collections
    - Correggere il danneggiamento dei dati
                 
#### <a name="service-fabric-71-releases"></a>Service Fabric 7.1
| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 20 aprile 2020 | [Azure Service Fabric 7.1](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-release/ba-p/1311373)  | [Note sulla versione](https://github.com/microsoft/service-fabric/tree/master/release_notes/Service-Fabric-71-releasenotes.md)|
| 16 giugno 2020 | [Microsoft Azure Service Fabric 7.1 Primo aggiornamento](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-first-refresh-release/ba-p/1466517) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU1-releasenotes.md)
| 20 luglio 2020 | [Microsoft Azure Service Fabric aggiornamento di 7,1 secondi](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-second-refresh-release/ba-p/1534246) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU2-releasenotes.md)
| 12 agosto 2020 | [Microsoft Azure Service Fabric 7.1 Terzo aggiornamento](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-third-refresh-release/ba-p/1587586) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU3-releasenotes.md)
| 10 settembre 2020 | [Microsoft Azure Service Fabric 7.1 Quarto aggiornamento](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-1-fourth-refresh-release/ba-p/1653859)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU5-releasenotes.md)|
| 7 ottobre 2020 | Microsoft Azure Service Fabric 7.1 Sesto aggiornamento | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU6-releasenotes.md)|
| 23 novembre 2020 | Microsoft Azure Service Fabric 7.1 Eighth Refresh | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-71CU8-releasenotes.md)|


### <a name="service-fabric-70"></a>Service Fabric 7.0

Azure Service Fabric 7.0 è ora disponibile. Sarà possibile eseguire l'aggiornamento alla versione 7.0 tramite il portale di Azure o tramite una Azure Resource Manager distribuzione. A causa dei commenti e suggerimenti dei clienti sulle versioni intorno al periodo flitto, non verrà avviata l'aggiornamento automatico dei cluster impostati per ricevere gli aggiornamenti automatici fino a gennaio.
A gennaio verrà ripresa la procedura di implementazione standard e i cluster con aggiornamenti automatici abilitati inizieranno a ricevere automaticamente l'aggiornamento 7.0. Verrà fornito un altro annuncio prima dell'inizio dell'implementazione.
Verranno aggiornate anche le date di rilascio pianificate per indicare che si prendono in considerazione questi criteri. Cercare qui gli aggiornamenti nelle [pianificazioni delle versioni future.](https://github.com/Microsoft/service-fabric/#service-fabric-release-schedule)

#### <a name="key-announcements"></a>Annunci principali
 - [**Supporto di KeyVaultReference**](./service-fabric-keyvault-references.md)per i segreti dell'applicazione [](./concepts-managed-identity.md) (anteprima): le applicazioni Service Fabric che hanno abilitato identità gestite possono ora fare riferimento direttamente a un URL segreto Key Vault come variabile di ambiente, parametro dell'applicazione o credenziale del repository del contenitore. Service Fabric risolverà automaticamente il segreto usando l'identità gestita dell'applicazione. 
     
- **Miglioramento della sicurezza dell'aggiornamento** per i servizi senza stato: per garantire [](/dotnet/api/system.fabric.description.statelessservicedescription) la disponibilità durante un aggiornamento dell'applicazione, sono state introdotte nuove configurazioni per definire il numero minimo di istanze che i servizi senza stato devono essere considerati disponibili. In precedenza questo valore era 1 per tutti i servizi e non era modificabile. Con questo nuovo controllo di sicurezza per servizio, è possibile assicurarsi che i servizi mantenino un numero minimo di istanze durante gli aggiornamenti delle applicazioni, gli aggiornamenti del cluster e altre attività di manutenzione che si basano sui controlli di integrità e sicurezza di Service Fabric.
  
- [**Limiti delle risorse per i**](./service-fabric-resource-governance.md#enforcing-the-resource-limits-for-user-services)servizi utente: gli utenti possono impostare limiti delle risorse per i servizi utente in un nodo per evitare scenari come l'esaurimento delle risorse dei Service Fabric di sistema. 
  
- [**Costo di spostamento del servizio molto elevato**](./service-fabric-cluster-resource-manager-movement-cost.md) per un tipo di replica. Le repliche con un costo di spostamento molto elevato verranno spostate solo se nel cluster è presente una violazione di vincolo che non può essere corretta in altro modo. Fare riferimento al documento collegato per altre informazioni su quando l'utilizzo di un costo di spostamento "Molto elevato" è ragionevole e per considerazioni aggiuntive.
  
-  **Controlli di sicurezza aggiuntivi del cluster:** in questa versione è stato introdotto un controllo di sicurezza del quorum del nodo di seeding configurabile. In questo modo è possibile personalizzare il numero di nodi di inizializzazione che devono essere disponibili durante gli scenari di ciclo di vita e gestione del cluster. Le operazioni che accettano il cluster al di sotto del valore configurato vengono bloccate. Attualmente il valore predefinito è sempre un quorum dei nodi di seed, ad esempio se si dispone di 7 nodi di seed, un'operazione che potrebbe portare al di sotto di 5 nodi di seed viene bloccata per impostazione predefinita. Con questa modifica, è possibile impostare il valore minimo di sicurezza 6, che consente di non visualizzare più un solo nodo di seed alla volta.
   
- Aggiunta del supporto [**per la gestione del servizio Backup e ripristino in Service Fabric Explorer**](./service-fabric-backuprestoreservice-quickstart-azurecluster.md). In questo modo è possibile eseguire le attività seguenti direttamente da SFX: individuazione del servizio di backup e ripristino, creazione di criteri di backup, abilitazione dei backup automatici, esecuzione di backup adhoc, attivazione delle operazioni di ripristino ed esplorazione dei backup esistenti.

- Annuncio della disponibilità di [**ReliableCollectionsMissingTypesTool:**](https://github.com/hiadusum/ReliableCollectionsMissingTypesTool)questo strumento consente di verificare che i tipi usati nelle raccolte reliable collections siano compatibili con le versioni precedenti e precedenti durante un aggiornamento in sequenza dell'applicazione. Ciò consente di evitare errori di aggiornamento o perdita di dati e danneggiamento dei dati a causa di tipi mancanti o incompatibili.

- [**Abilita le operazioni di lettura stabili**](./service-fabric-reliable-services-configuration.md#configuration-names-1)nelle repliche secondarie: le operazioni di lettura stabili limitano le repliche secondarie ai valori restituiti che sono stati acked quorum.

Questa versione contiene anche altre nuove funzionalità, correzioni di bug e miglioramenti di supporto, affidabilità e prestazioni. Per l'elenco completo delle modifiche, vedere le [note sulla versione](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md).

#### <a name="service-fabric-70-releases"></a>Service Fabric versioni 7.0

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 18 novembre 2019 | [Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Service-Fabric-7-0-Release/ba-p/1015482)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_70.md)|
| 30 gennaio 2020 | [Versione di aggiornamento di Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-second-refresh-release/ba-p/1137690)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU2-releasenotes.md)|
| 6 febbraio 2020 | [Versione di aggiornamento di Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-third-refresh-release/ba-p/1156508)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU3-releasenotes.md)|
| 2 marzo 2020 | [Versione di aggiornamento di Azure Service Fabric 7.0](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-fourth-refresh-release/ba-p/1205414)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU4-releasenotes.md)
| 6 maggio 2020 | [Azure Service Fabric 7.0 Sixth Refresh Release](https://techcommunity.microsoft.com/t5/azure-service-fabric/azure-service-fabric-7-0-sixth-refresh-release/ba-p/1365709) | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU6-releasenotes.md)|
| 9 ottobre 2020 | Azure Service Fabric 7.0 Nono aggiornamento | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service-Fabric-70CU9-releasenotes.md)|

### <a name="service-fabric-65"></a>Service Fabric 6.5

Questa versione include miglioramenti a livello di supporto, affidabilità e prestazioni, nuove funzionalità, correzioni di bug e miglioramenti per semplificare la gestione del ciclo di vita di cluster e applicazioni.

> [!IMPORTANT]
> Service Fabric 6.5 è la versione finale con Service Fabric strumenti di supporto in Visual Studio 2015. I clienti sono invitati a passare a [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) in futuro.

Novità di Service Fabric 6.5:

- Service Fabric Explorer include un [visualizzatore archivio immagini per](service-fabric-visualizing-your-cluster.md#image-store-viewer) controllare le applicazioni caricate nell'archivio immagini.

- [Patch Orchestration Application (POA)](service-fabric-patch-orchestration-application.md) versione [1.4.0](https://github.com/microsoft/Service-Fabric-POA/releases/tag/v1.4.0) include molti miglioramenti di auto-diagnostica. I clienti di POA sono consigliati di passare a questa versione.

- [Il servizio EventStore è abilitato per impostazione](service-fabric-visualizing-your-cluster.md#event-store) predefinita per i cluster Service Fabric 6.5, a meno che non sia stato scelto esplicitamente.

- Aggiunta degli [eventi del ciclo di vita della](service-fabric-diagnostics-event-generation-operational.md#replica-events) replica per i servizi con stato.

- [Migliore visibilità dello stato del nodo](service-fabric-understand-and-troubleshoot-with-system-health-reports.md#seed-node-status)di seeding, inclusi gli avvisi a livello di cluster se un nodo di seeding non è integro (*Down*, *Removed* o *Unknown*).

- [Service Fabric application disaster recovery tool](https://github.com/Microsoft/Service-Fabric-AppDRTool) consente Service Fabric ripristino rapido dei servizi con stato quando il cluster primario rileva un'emergenza. I dati del cluster primario vengono sincronizzati continuamente nell'applicazione di standby secondario usando il backup e il ripristino periodici.

- Visual Studio supporto per la [pubblicazione di app .NET Core in cluster basati su Linux.](service-fabric-how-to-publish-linux-app-vs.md)

- L'interfaccia della riga di comando di [Azure Service Fabric (SFCTL)](./service-fabric-cli.md) verrà installata automaticamente per Service Fabric 6.5 (e versioni successive) quando si aggiorna o si crea un nuovo cluster Linux in Azure.

- [SFCTL viene](./service-fabric-cli.md) installato per impostazione predefinita nei cluster OneBox MacOS/Linux.

Per altre informazioni, vedere le [note sulla versione Service Fabric 6.5](https://github.com/Azure/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf).

#### <a name="service-fabric-65-releases"></a>Service Fabric versioni 6.5

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 11 giugno 2019 | [Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65.pdf)|
| 2 luglio 2019 | [Versione di aggiornamento di Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU1.pdf)  |
| 29 luglio 2019 | [Versione di aggiornamento di Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Second-Refresh-Release/ba-p/800523)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU2.pdf)  |
| Ago 23, 2019 | [Versione di aggiornamento di Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Third-Refresh-Release/ba-p/818599)  | [Note sulla versione](https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU3.pdf)  |
| 14 ottobre 2019 | [Versione di aggiornamento di Azure Service Fabric 6.5](https://techcommunity.microsoft.com/t5/Azure-Service-Fabric/Azure-Service-Fabric-6-5-Fifth-Refresh-Release/ba-p/913296)  | [Note sulla versione] (https://github.com/microsoft/service-fabric/blob/master/release_notes/Service_Fabric_ReleaseNotes_65CU5.md  |


### <a name="service-fabric-64-releases"></a>Service Fabric versione 6.4

| Data di rilascio | Versione | Altre informazioni |
|---|---|---|
| 30 novembre 2018 | [Azure Service Fabric 6.4](https://blogs.msdn.microsoft.com/azureservicefabric/2018/11/30/azure-service-fabric-6-4-release/)  | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2018/12/Service-Fabric-6.4-Release.pdf)|
| 12 dicembre 2018 | [Versione di aggiornamento di Azure Service Fabric 6.4 per cluster Windows](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)  | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2018/12/Links.pdf)  |
| 4 febbraio 2019 | [Versione di aggiornamento di Azure Service Fabric 6.4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/02/Service-Fabric-6.4CU3-Release-Notes.pdf) |
| 4 marzo 2019 | [Versione di aggiornamento di Azure Service Fabric 6.4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/03/Service-Fabric-6.4CU4-Release-Notes.pdf)
| 8 aprile 2019 | [Versione di aggiornamento di Azure Service Fabric 6.4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/04/Service-Fabric-6.4CU5-ReleaseNotes3.pdf)
| 2 maggio 2019 | [Versione di aggiornamento di Azure Service Fabric 6.4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/05/Service-Fabric-64CU6-Release-Notes-V2.pdf)
| 28 maggio 2019 | [Versione di aggiornamento di Azure Service Fabric 6.4](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric) | [Note sulla versione](https://msdnshared.blob.core.windows.net/media/2019/05/Service_Fabric_64CU7_Release_Notes1.pdf)
