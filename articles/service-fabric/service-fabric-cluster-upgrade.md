---
title: Aggiornamento di cluster di Azure Service Fabric
description: Informazioni sulle opzioni per l'aggiornamento del cluster di Azure Service Fabric
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 636d4cb11f7cc6780d560d3d0043a89c69840a4f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105731114"
---
# <a name="upgrading-and-updating-azure-service-fabric-clusters"></a>Aggiornamento e aggiornamento di cluster di Azure Service Fabric

Un cluster di Azure Service Fabric è una risorsa di cui si è proprietari, ma è parzialmente gestito da Microsoft. Questo articolo descrive le opzioni relative a quando e come aggiornare il cluster di Azure Service Fabric.

## <a name="automatic-versus-manual-upgrades"></a>Aggiornamenti automatici e manuali

È fondamentale assicurarsi che il cluster Service Fabric esegua sempre una [versione di runtime supportata](service-fabric-versions.md). Ogni volta che Microsoft annuncia il rilascio di una nuova versione di Service Fabric, la versione precedente viene contrassegnata per la *fine del supporto* dopo un minimo di 60 giorni da tale data. Le nuove versioni vengono annunciate nel [Blog del team di Service Fabric](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric).

Quattordici giorni prima della scadenza della versione in cui è in esecuzione il cluster, viene generato un evento di integrità che inserisce il cluster in uno stato di *avviso* . Il cluster rimane in uno stato di avviso fino a quando non si esegue l'aggiornamento a una versione di runtime supportata.

È possibile impostare il cluster per ricevere gli aggiornamenti automatici del Service Fabric non appena vengono rilasciati da Microsoft oppure è possibile scegliere manualmente da un elenco di versioni attualmente supportate. Queste opzioni sono disponibili nella sezione **aggiornamenti dell'infrastruttura** della risorsa cluster Service Fabric.

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="Selezionare aggiornamenti automatici o manuali nella sezione ' aggiornamenti dell'infrastruttura ' della risorsa cluster in portale di Azure.":::

È anche possibile impostare la modalità di aggiornamento del cluster e selezionare una versione di runtime [usando un modello di gestione risorse](service-fabric-cluster-upgrade-version-azure.md#resource-manager-template).

Gli aggiornamenti automatici sono la modalità di aggiornamento consigliata, in quanto questa opzione garantisce che il cluster rimanga in uno stato supportato e possa sfruttare le correzioni e le funzionalità più recenti, consentendo allo stesso tempo di pianificare gli aggiornamenti in modo che sia meno problematico per i carichi di lavoro tramite una strategia di [distribuzione Wave](#wave-deployment-for-automatic-upgrades) .

## <a name="wave-deployment-for-automatic-upgrades"></a>Distribuzione Wave per aggiornamenti automatici

Con la distribuzione Wave è possibile ridurre al minimo l'interferenza di un aggiornamento nel cluster selezionando il livello di maturità di un aggiornamento, a seconda del carico di lavoro. Ad esempio, è possibile configurare una   ->  pipeline di distribuzione Wave di produzione di una *fase* di test  ->   per i diversi cluster di Service Fabric per testare la compatibilità di un aggiornamento del runtime prima di applicarlo ai carichi di lavoro di produzione.

Per acconsentire esplicitamente alla distribuzione Wave, specificare uno dei valori Wave seguenti per il cluster (nel modello di distribuzione):

* **Wave 0**: i cluster vengono aggiornati non appena viene rilasciata una nuova compilazione Service Fabric. Progettato per i cluster di test/sviluppo.
* **Wave 1**: i cluster vengono aggiornati una settimana (sette giorni) dopo il rilascio di una nuova compilazione. Progettato per i cluster pre-produzione/gestione temporanea.
* **Wave 2**: i cluster vengono aggiornati due settimane (14 giorni) dopo il rilascio di una nuova compilazione. Progettato per i cluster di produzione.

È possibile eseguire la registrazione per le notifiche di posta elettronica con collegamenti a ulteriori informazioni in caso di errore dell'aggiornamento del cluster. Per iniziare, vedere [distribuzione Wave per gli aggiornamenti automatici](service-fabric-cluster-upgrade-version-azure.md#wave-deployment-for-automatic-upgrades) .

## <a name="phases-of-automatic-upgrade"></a>Fasi di aggiornamento automatico

Microsoft gestisce il codice e la configurazione di Runtime Service Fabric eseguiti in un cluster di Azure. Gli aggiornamenti monitorati automaticamente al software vengono eseguiti in base alle esigenze. Gli aggiornamenti possono interessare il codice, la configurazione o entrambi. Per ridurre al minimo l'effetto di questi aggiornamenti sulle applicazioni, vengono eseguiti nelle fasi seguenti:

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>Fase 1: Un aggiornamento viene eseguito usando tutti i criteri di integrità del cluster

In questa fase gli aggiornamenti interessano un dominio di aggiornamento per volta e le applicazioni in esecuzione sul cluster non subiscono tempi di inattività. I criteri di integrità del cluster (per integrità del nodo e integrità dell'applicazione) vengono rispettati durante l'aggiornamento.

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento e viene inviato un messaggio di posta elettronica al proprietario della sottoscrizione. contenente le informazioni seguenti:

* Notifica che era necessario eseguire il rollback di un aggiornamento del cluster.
* Eventuali azioni correttive suggerite.
* Il numero di giorni (*n*) finché non viene eseguita la fase 2.

Si tenta di eseguire più volte lo stesso aggiornamento nel caso in cui non sia riuscito a causa di problemi di infrastruttura. Dopo gli *n* giorni dalla data di invio del messaggio di posta elettronica, continuiamo con la fase 2.

Se i criteri di integrità del cluster sono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o in una delle riesecuzioni di aggiornamento in questa fase. Nessun messaggio di posta elettronica di conferma della corretta esecuzione, per evitare l'invio di messaggi di posta elettronica eccessivi. La ricezione di un messaggio di posta elettronica indica un'eccezione alle normali operazioni. Si prevede che la maggior parte degli aggiornamenti del cluster abbia esito positivo, senza alcun impatto sulla disponibilità dell'applicazione.

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>Fase 2: Un aggiornamento viene eseguito usando solo i criteri di integrità predefiniti

I criteri di integrità in questa fase vengono impostati in modo tale che il numero di applicazioni integre all'inizio dell'aggiornamento rimanga invariato durante il processo di aggiornamento. Come nella fase 1, in questa fase gli aggiornamenti interessano un dominio di aggiornamento per volta e le applicazioni in esecuzione sul cluster non subiscono tempi di inattività. Durante l'aggiornamento vengono rispettati i criteri di integrità del cluster (una combinazione dell'integrità del nodo e dell'integrità di tutte le applicazioni in esecuzione nel cluster).

Se i criteri di integrità del cluster in vigore non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Al proprietario della sottoscrizione verrà quindi inviato un messaggio di posta elettronica contenente le informazioni seguenti:

* Notifica che era necessario eseguire il rollback di un aggiornamento del cluster.
* Eventuali azioni correttive suggerite.
* Il numero di giorni (n) prima dell'avvio della fase 3.

Si tenta di eseguire più volte lo stesso aggiornamento nel caso in cui non sia riuscito a causa di problemi di infrastruttura. Viene inviato un messaggio di sollecito alcuni giorni prima della scadenza degli n giorni. Dopo che gli n giorni dalla data di invio del messaggio di posta elettronica sono trascorsi, si passa alla fase 3. I messaggi di posta elettronica inviati nella fase 2 devono essere tenuti in alta considerazione e devono essere intraprese azioni correttive.

Se i criteri di integrità del cluster sono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o in una delle repliche previste in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita.

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>Fase 3: Un aggiornamento viene eseguito usando criteri di integrità aggressivi

Tali criteri di integrità in questa fase sono pensati per il completamento dell'aggiornamento piuttosto che per l'integrità delle applicazioni. In questa fase vengono eseguiti pochi aggiornamenti del cluster. Se il cluster raggiunge questa fase, è possibile che l'applicazione diventi non integra e/o perda la disponibilità.

In modo analogo alle altre due fasi, gli aggiornamenti della fase 3 procedono in base a un dominio di aggiornamento alla volta.

Se i criteri di integrità del cluster non vengono soddisfatti, viene eseguito il rollback dell'aggiornamento. Si tenta di eseguire più volte lo stesso aggiornamento nel caso in cui non sia riuscito a causa di problemi di infrastruttura. A questo punto, il cluster viene bloccato in modo che non riceva più supporto e/o aggiornamenti.

Al proprietario della sottoscrizione viene inviato un messaggio di posta elettronica contenente queste informazioni e le azioni correttive. Se la fase 3 ha esito negativo, si prevede che il cluster non venga impostato su alcun tipo di stato.

Se i criteri di integrità del cluster sono soddisfatti, l'aggiornamento si considera riuscito e viene contrassegnato come completato. Questa situazione può verificarsi durante l'aggiornamento iniziale o in una delle repliche previste in questa fase. Non viene inviato alcun messaggio di posta elettronica di conferma in caso di esecuzione riuscita.

## <a name="custom-policies-for-manual-upgrades"></a>Criteri personalizzati per gli aggiornamenti manuali

È possibile specificare criteri personalizzati per gli aggiornamenti manuali del cluster. Questi criteri vengono applicati ogni volta che si seleziona una nuova versione di runtime, che attiva il sistema per avviare l'aggiornamento del cluster. Se non si esegue l'override dei criteri, vengono usati quelli predefiniti. Per altre informazioni, vedere [impostare criteri personalizzati per gli aggiornamenti manuali](service-fabric-cluster-upgrade-version-azure.md#custom-policies-for-manual-upgrades).

## <a name="other-cluster-updates"></a>Altri aggiornamenti del cluster

Al di fuori dell'aggiornamento del runtime, potrebbe essere necessario eseguire una serie di altre azioni per rendere aggiornato il cluster, inclusi i seguenti:

### <a name="managing-certificates"></a>Gestione di certificati

Service Fabric usa i [certificati server X.509](service-fabric-cluster-security.md) specificati quando si crea un cluster per proteggere le comunicazioni tra i nodi del cluster ed eseguire l'autenticazione client. È possibile aggiungere, aggiornare o eliminare certificati per il cluster e il client nel [portale di Azure](https://portal.azure.com) o tramite Azure PowerShell o l'interfaccia della riga di comando di Azure.  Per altre informazioni, vedere [Aggiungere o rimuovere certificati](service-fabric-cluster-security-update-certs-azure.md)

### <a name="opening-application-ports"></a>Apertura di porte dell'applicazione

È possibile modificare le porte dell'applicazione modificando le proprietà della risorsa del servizio di bilanciamento del carico associate al tipo di nodo. È possibile usare il portale di Azure oppure PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Aprire le porte dell'applicazione per un cluster](create-load-balancer-rule.md).

### <a name="defining-node-properties"></a>Definizione delle proprietà del nodo

È talvolta necessario assicurarsi che carichi di lavoro specifici vengano eseguiti solo in tipi di nodo specifici nel cluster. Ad esempio, è possibile che alcuni carichi di lavoro richiedano GPU o SSD, mentre altri no. Per ogni tipo di nodo in un cluster è possibile aggiungere proprietà personalizzate ai nodi corrispondenti. I vincoli di posizionamento sono le istruzioni collegate ai singoli servizi che selezionano una o più proprietà del nodo. Definiscono la posizione in cui i servizi devono essere eseguiti.

Per informazioni dettagliate sull'uso e sulla definizione dei vincoli di posizionamento e delle proprietà dei nodi, vedere [Proprietà dei nodi e vincoli di posizionamento](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints).

### <a name="adding-capacity-metrics"></a>Aggiunta di metriche di capacità

Per ogni tipo di nodo è possibile aggiungere metriche di capacità personalizzate da usare nelle applicazioni per creare report sul carico. Per informazioni dettagliate sull'uso di metriche di capacità per la segnalazione del carico, vedere i documenti di Gestione risorse del cluster di Service Fabric relativi a [descrizione del cluster](service-fabric-cluster-resource-manager-cluster-description.md) e [metriche e carico](service-fabric-cluster-resource-manager-metrics.md).

### <a name="customizing-settings-for-your-cluster"></a>Personalizzazione delle impostazioni per il cluster

In un cluster è possibile personalizzare molte impostazioni di configurazione diverse, ad esempio il livello di affidabilità del cluster e le proprietà dei nodi. Per altre informazioni, vedere [Impostazioni di un cluster di Service Fabric](service-fabric-cluster-fabric-settings.md).

### <a name="upgrading-os-images-for-cluster-nodes"></a>Aggiornamento delle immagini del sistema operativo per i nodi del cluster

È consigliabile abilitare gli aggiornamenti automatici delle immagini del sistema operativo per i nodi del cluster Service Fabric. A tale scopo, è necessario eseguire diversi requisiti e passaggi del cluster. Un'altra opzione consiste nell'usare patch Orchestration Application (POA, un'applicazione Service Fabric che consente di automatizzare l'applicazione di patch al sistema operativo in un cluster Service Fabric senza tempi di inattività. Per ulteriori informazioni su queste opzioni, vedere [applicare patch al sistema operativo Windows nel cluster Service Fabric](service-fabric-patch-orchestration-application.md).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire gli aggiornamenti di Service Fabric](service-fabric-cluster-upgrade-version-azure.md)
* Personalizzare le [impostazioni del cluster di Service Fabric](service-fabric-cluster-fabric-settings.md)
* [Ridimensionare il cluster all'interno e all'esterno](service-fabric-cluster-scale-in-out.md)
* Informazioni sugli [aggiornamenti delle applicazioni](service-fabric-application-upgrade.md)
