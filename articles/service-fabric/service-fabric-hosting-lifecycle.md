---
title: Ciclo di vita di attivazione e disattivazione di Azure Service Fabric
description: Informazioni sul ciclo di vita di un'applicazione e di un ServicePackage in un nodo.
author: tugup
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: tugup
ms.openlocfilehash: d8585d0b39e4a4ef9cf77f40ea878ddb47bcb0de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97831823"
---
# <a name="azure-service-fabric-hosting-life-cycle"></a>Ciclo di vita dell'hosting di Azure Service Fabric

Questo articolo fornisce una panoramica degli eventi che si verificano in Azure Service Fabric quando un'applicazione viene attivata in un nodo. Vengono illustrate diverse configurazioni del cluster che controllano il comportamento.

Prima di continuare, assicurarsi di avere compreso i concetti e le relazioni illustrati in [modellare un'applicazione in Service Fabric][a1]. 

> [!NOTE]
> Questo articolo usa una terminologia in modi specializzati. Se non specificato diversamente:
>
> - *Replica* si riferisce sia a una replica di un servizio con stato che a un'istanza di un servizio senza stato.
> - *CodePackage* viene considerato equivalente a un processo ServiceHost che registra un serviceType. Ospita le repliche dei servizi di tale ServiceType.
>

## <a name="activate-an-applicationpackage-or-servicepackage"></a>Attivare un pacchetto dell'applicazione o ServicePackage

Per attivare un pacchetto dell'applicazione o ServicePackage:

1. Scaricare pacchetto dell'applicazione, ad esempio *ApplicationManifest.xml*.
2. Configurare un ambiente per l'applicazione. I passaggi includono, ad esempio, la creazione di utenti.
3. Avviare il rilevamento dell'applicazione per la disattivazione.
4. Scaricare ServicePackage, ad esempio *ServiceManifest.xml*, codice, file di configurazione e pacchetti di dati.
5. Configurare un ambiente per la ServicePackage. I passaggi includono, ad esempio, la configurazione di un firewall e l'allocazione di porte per gli endpoint.
6. Iniziare a tenere traccia del ServicePackage per la disattivazione.
7. Avviare il SetupEntryPoint per i CodePackage e attenderne il completamento.
8. Avviare MainEntryPoint per i CodePackage.

### <a name="servicetype-blocklisting"></a>Blocklisting ServiceType
`ServiceTypeDisableFailureThreshold` determina il numero di errori di attivazione, download e CodePackage consentiti. Una volta raggiunta la soglia, il ServiceType è pianificato per Blocklisting. Il primo errore di attivazione, l'errore di download o l'arresto anomalo di CodePackage Pianifica il ServiceType Blocklisting. 

La `ServiceTypeDisableGraceInterval` configurazione determina l'intervallo di tolleranza prima che ServiceType sia Blocklisting sul nodo. Poiché questo processo viene eseguito, l'attivazione, il download e il riavvio del CodePackage vengono ritentati in parallelo. Un nuovo tentativo indica, ad esempio, che il CodePackage è pianificato per riavviarsi dopo l'arresto anomalo o Service Fabric tenterà di scaricare nuovamente i pacchetti.

Quando ServiceType è Blocklisting, viene visualizzato un errore di integrità: `'System.Hosting' reported Error for property 'ServiceTypeRegistration:ServiceType'. The ServiceType was disabled on the node.`

ServiceType viene nuovamente abilitato nel nodo se si verifica uno degli eventi seguenti:
- L'attivazione ha esito positivo. In alternativa, se ha esito negativo, raggiunge il `ActivationMaxFailureCount` numero massimo di tentativi.
- Il download ha esito positivo. In alternativa, se ha esito negativo, raggiunge il `DeploymentMaxFailureCount` numero massimo di tentativi.
- Viene avviato un CodePackage che si è arrestato in modo anomalo e il ServiceType viene registrato correttamente.

`ActivationMaxFailureCount` e `DeploymentMaxFailureCount` sono il numero massimo di tentativi Service Fabric farà per attivare o scaricare un'applicazione in un nodo. Una volta raggiunto il limite massimo, Service Fabric Abilita di nuovo il ServiceType per l'attivazione. 

Queste soglie offrono al servizio un'altra opportunità per l'attivazione. Se l'attivazione riesce, il problema viene automaticamente salvato. 

Una replica appena inserita o attivata può attivare una nuova operazione di attivazione o di download. Questa azione avrà esito positivo o attiverà nuovamente il Blocklisting ServiceType.

> [!NOTE]
> Un CodePackage in arresto anomalo che non registra un ServiceType non influisce sul ServiceType. Solo un CodePackage che ospita una replica in modo anomalo influisca sul ServiceType.
>

### <a name="codepackage-crash"></a>Arresto anomalo di CodePackage
Quando si verifica un arresto anomalo di un CodePackage, Service Fabric usa un backoff per riavviarlo. Backoff viene applicato indipendentemente dal fatto che il CodePackage abbia registrato un tipo con il Runtime Service Fabric.

Il valore backoff è `Min(RetryTime, ActivationMaxRetryInterval)` . Il valore viene incrementato in importi costanti, lineari o esponenziali in base all' `ActivationRetryBackoffExponentiationBase` impostazione di configurazione:

- **Costante**: se `ActivationRetryBackoffExponentiationBase == 0` , quindi `RetryTime = ActivationRetryBackoffInterval` .
- **Lineare**: se  `ActivationRetryBackoffExponentiationBase == 0` , quindi `RetryTime = ContinuousFailureCount ActivationRetryBackoffInterval` , dove `ContinuousFailureCount` è il numero di volte in cui un CodePackage si arresta o non viene attivato.
- **Esponenziale**: `RetryTime = (ActivationRetryBackoffInterval in seconds) * (ActivationRetryBackoffExponentiationBase ^ ContinuousFailureCount)` .
    
È possibile controllare il comportamento cambiando i valori. Se ad esempio si desiderano diversi tentativi di riavvio rapido, è possibile utilizzare importi lineari impostando `ActivationRetryBackoffExponentiationBase` su `0` e impostando `ActivationRetryBackoffInterval` su `10` . Se quindi si verifica un arresto anomalo di un CodePackage, l'intervallo di avvio sarà dopo 10 secondi. Se il pacchetto continua ad arrestarsi in modo anomalo, il backoff viene modificato in 20 secondi, 30 secondi, 40 secondi e così via, fino a quando l'attivazione di CodePackage non riesce o viene disattivato il CodePackage. 
    
Intervallo di tempo massimo in cui Service Fabric viene eseguito il backup, ovvero attese, dopo che un errore è regolato da `ActivationMaxRetryInterval` .
    
Se il CodePackage si arresta in modo anomalo e torna attivo, deve rimanere attivo per il periodo di tempo specificato da `CodePackageContinuousExitFailureResetInterval` . Dopo questo intervallo, Service Fabric considera integro il CodePackage. Service Fabric quindi sovrascrive il rapporto di stato di errore precedente come OK e reimposta il `ContinuousFailureCount` .

### <a name="codepackage-not-registering-servicetype"></a>CodePackage che non registra ServiceType
Se un CodePackage rimane attivo e si prevede di registrare un oggetto ServiceType ma non, Service Fabric genera un report sull'integrità di avviso dopo `ServiceTypeRegistrationTimeout` . Il report indica che ServiceType non è stato registrato entro il periodo di tempo previsto.

### <a name="activation-failure"></a>Errore di attivazione
Quando Service Fabric trova un errore durante l'attivazione, viene sempre usato un backoff lineare, come avviene con un arresto anomalo del CodePackage. L'operazione di attivazione restituisce dopo i tentativi a questi intervalli: (0 + 10 + 20 + 30 + 40) = 100 secondi. Il primo tentativo è immediato. Dopo questa sequenza, l'attivazione non viene ritentata.
    
Il numero massimo di backoff di attivazione può essere `ActivationMaxRetryInterval` . Il tentativo può essere `ActivationMaxFailureCount` .

### <a name="download-failure"></a>Errore di download
Service Fabric utilizza sempre un backoff lineare quando viene rilevato un errore durante il download. L'operazione di attivazione restituisce dopo i tentativi a questi intervalli: (0 + 10 + 20 + 30 + 40) = 100 secondi. Il primo tentativo è immediato. Dopo questa sequenza, il download non viene ritentato. 

Il backoff lineare per un download è uguale a `ContinuousFailureCount`  *  `DeploymentRetryBackoffInterval` . Il valore massimo di backoff può essere `DeploymentMaxRetryInterval` . Analogamente alle attivazioni, le operazioni di download possono ritentare il `ActivationMaxFailureCount` limite.

> [!NOTE]
> Prima di modificare queste impostazioni, tenere presenti gli esempi seguenti:
>
>* Se il CodePackage continua a bloccarsi e viceversa, il ServiceType verrà disabilitato. Tuttavia, se la configurazione dell'attivazione prevede un riavvio rapido, il CodePackage può risalire alcune volte prima che ServiceType sia effettivamente Blocklisting. 
>
>    Supponiamo, ad esempio, che il CodePackage venga visualizzato, registri il ServiceType con Service Fabric e quindi arresti in modo anomalo. In tal caso, dopo che l'hosting riceve una registrazione del tipo, il `ServiceTypeDisableGraceInterval` periodo viene annullato. Questo processo può essere ripetuto fino a quando il CodePackage non torna a un valore maggiore del `ServiceTypeDisableGraceInterval` periodo. Il ServiceType sarà quindi Blocklisting nel nodo. Il Blocklisting ServiceType potrebbe richiedere più tempo del previsto.
>
>* In caso di attivazione, quando un sistema Service Fabric deve inserire una replica in un nodo, l'agente di riconfigurazione chiede al sottosistema di hosting di attivare l'applicazione. Ritenta la richiesta di attivazione ogni 15 secondi. La durata è regolata dall'impostazione di `RAPMessageRetryInterval` configurazione. Service Fabric non è in grado di rilevare che ServiceType è stato Blocklisting a meno che l'operazione di attivazione nell'hosting non sia attiva per un periodo più lungo rispetto all'intervallo tra tentativi e `ServiceTypeDisableGraceInterval` . 
>
>    Si supponga, ad esempio, che il cluster sia `ActivationMaxFailureCount` impostato su 5 e che `ActivationRetryBackoffInterval` sia impostato su 1 secondo. In questo caso, l'operazione di attivazione restituisce un intervallo compreso tra (0 + 1 + 2 + 3 + 4) = 10 secondi. Il primo tentativo è immediato. Dopo questa sequenza, l'hosting restituisce un nuovo tentativo. L'operazione di attivazione viene completata senza ritentare dopo 15 secondi. 
>
>    Service Fabric ha esaurito tutti i tentativi consentiti entro 15 secondi. Quindi, ogni tentativo dell'agente di riconfigurazione crea una nuova operazione di attivazione nel sottosistema di hosting e il modello continua a ripetere. Di conseguenza, ServiceType non è mai Blocklisting sul nodo. Poiché il ServiceType non sarà Blocklisting nel nodo, la replica non verrà spostata e tentata su un nodo diverso.
> 

## <a name="deactivation"></a>Disattivazione

Quando un ServicePackage viene attivato in un nodo, viene rilevato per la disattivazione. La disattivazione funziona in due modi:

- **Disattivazione periodica**: ogni `DeactivationScanInterval` sistema controlla i pacchetti del servizio che non hanno *mai* ospitato una replica e li contrassegna come candidati per la disattivazione.
- **Disattivazione ReplicaClose**: se una replica viene chiusa, il Deactivator ottiene un `DecrementUsageCount` . Un conteggio è pari a 0 quando ServicePackage non ospita alcuna replica, pertanto ServicePackage è un candidato per la disattivazione.

La modalità di attivazione determina quando i candidati sono pianificati per la disattivazione. In modalità condivisa, i candidati per la disattivazione vengono pianificati dopo `DeactivationGraceInterval` . In modalità esclusiva sono pianificate dopo `ExclusiveModeDeactivationGraceInterval` . Se arriva un nuovo posizionamento della replica tra questi orari, la disattivazione viene annullata. 

Per altre informazioni, vedere [modalità esclusiva e modalità condivisa][a2].

### <a name="periodic-deactivation"></a>Disattivazione periodica
Di seguito sono riportati alcuni esempi di disattivazione periodica:

* **Esempio 1**: Supponiamo che la disattivazione avvii un'analisi all'ora T (il `DeactivationScanInterval` ). L'analisi successiva sarà a 2T. Si supponga che l'attivazione di ServicePackage sia stata eseguita a T + 1. Questo ServicePackage non ospita una replica, quindi deve essere disattivata. 

    Per essere un candidato per la disattivazione, il ServicePackage deve ospitare nessuna replica per almeno T tempo. Sarà idoneo per la disattivazione a 2T + 1. Quindi, l'analisi in 2T non identificherà questo ServicePackage come candidato per la disattivazione. 

    Il successivo ciclo di disattivazione, 3T, pianifica questo ServicePackage per la disattivazione, perché ora il pacchetto è in uno stato senza replica per l'ora T.  

* **Esempio 2**: Supponiamo che un ServicePackage venga attivato all'ora t-1 e che il Deactivator avvii un'analisi a t. Il ServicePackage non ospita una replica. Alla successiva analisi, 2T, il ServicePackage verrà identificato come candidato per la disattivazione, quindi verrà pianificato per la disattivazione.  

* **Esempio 3**: Supponiamo che un ServicePackage venga attivato a t-1 e che il Deactivator avvii un'analisi a t. Il ServicePackage non ospita ancora una replica. Ora in T + 1 viene posizionata una replica. In altre parole, l'hosting ottiene un oggetto `IncrementUsageCount` , ovvero viene creata una replica. 

    A 2T, questo ServicePackage non verrà pianificato per la disattivazione. Poiché il pacchetto contiene una replica, la disattivazione seguirà la logica ReplicaClose, come illustrato nella sezione successiva di questo articolo.

* **Esempio 4**: Supponiamo che il ServicePackage sia di 10 GB. Poiché il pacchetto è di grandi dimensioni, il download sul nodo richiede tempo. Quando un'applicazione viene attivata, il disattivatore tiene traccia del ciclo di vita. Se `DeactivationScanInterval` è impostato su un valore ridotto, è possibile che il ServicePackage non disponga del tempo necessario per l'attivazione sul nodo a causa del tempo necessario per il download. Per ovviare a questo problema, è possibile [scaricare il ServicePackage in anticipo sul nodo][p1] o aumentare il `DeactivationScanInterval` . 

> [!NOTE]
> Un ServicePackage può essere disattivato in un punto qualsiasi tra ( `DeactivationScanInterval` a 2 * `DeactivationScanInterval` ) + `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 
>

### <a name="replicaclose-deactivation"></a>Disattivazione di ReplicaClose

> [!NOTE]
> Questa sezione si riferisce alle impostazioni di configurazione seguenti:
> - **DeactivationGraceInterval** / **ExclusiveModeDeactivationGraceInterval**: tempo assegnato a un ServicePackage per ospitare un'altra replica se è già stata ospitata una replica. 
> - **DeactivationScanInterval**: tempo minimo concesso a un ServicePackage per ospitare una replica se non è *mai* stata ospitata alcuna replica, ovvero se non è stata usata.
>

Il sistema mantiene il conteggio delle repliche mantenute da un ServicePackage. Se un ServicePackage contiene una replica e la replica è chiusa o inattiva, l'hosting ottiene un `DecrementUsageCount` . Quando una replica viene aperta, l'hosting ottiene un `IncrementUsageCount` . 

Il decremento indica che il numero di repliche ospitate da ServicePackage è stato ridotto di una replica. Quando il conteggio scende a 0, viene pianificata la disattivazione di ServicePackage. Il tempo trascorso il quale verrà disattivato è `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` . 

Si immagini, ad esempio, che si verifichi un decremento a T e che ServicePackage sia pianificato per la disattivazione a 2T + X ( `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` ). Durante questo periodo, se l'hosting ottiene un oggetto `IncrementUsage` perché viene creata una replica, la disattivazione viene annullata.

### <a name="ctrl--c"></a>CTRL + C
Se un ServicePackage passa `DeactivationGraceInterval` / `ExclusiveModeDeactivationGraceInterval` e non ospita ancora una replica, la disattivazione non può essere annullata. I CodePackage ricevono un gestore CTRL + C. A questo punto la pipeline di disattivazione deve terminare per arrestare il processo. 

Durante questo periodo di tempo, se una nuova replica per lo stesso ServicePackage sta tentando di essere posizionata, avrà esito negativo perché il processo non può passare dalla disattivazione all'attivazione.

## <a name="cluster-configurations"></a>Configurazioni dei cluster

Questa sezione elenca le configurazioni con impostazioni predefinite che influiscono sull'attivazione e la disattivazione.

### <a name="servicetype"></a>ServiceType
- **ServiceTypeDisableFailureThreshold**: valore predefinito: 1. Soglia per il numero di errori; una volta raggiunta questa soglia, FailoverManager riceve una notifica per disabilitare il tipo di servizio nel nodo e provare un nodo diverso per la selezione host.
- **ServiceTypeDisableGraceInterval**: valore predefinito: 30 secondi. Intervallo di tempo dopo il quale il tipo di servizio può essere disabilitato.
- **ServiceTypeRegistrationTimeout**: valore predefinito: 300 secondi. Timeout per la registrazione del ServiceType con Service Fabric.

### <a name="activation"></a>Activation
- **ActivationRetryBackoffInterval**: valore predefinito: 10 secondi. Intervallo di backoff per ogni errore di attivazione.
- **ActivationMaxFailureCount**: valore predefinito: 20. Numero massimo per il quale il sistema tenterà di eseguire un'attivazione non riuscita prima di rinunciare. 
- **ActivationRetryBackoffExponentiationBase**: valore predefinito: 1,5.
- **ActivationMaxRetryInterval**: valore predefinito: 3.600 secondi. Intervallo massimo di tentativi backoff per l'attivazione dopo gli errori.
- **CodePackageContinuousExitFailureResetInterval**: valore predefinito: 300 secondi. Intervallo di timeout per la reimpostazione del numero di errori di uscita continui per il CodePackage.

### <a name="download"></a>Scarica
- **DeploymentRetryBackoffInterval**: valore predefinito: 10. Intervallo di backoff per l'errore di distribuzione.
- **DeploymentMaxRetryInterval**: valore predefinito: 3.600 secondi. Intervallo massimo di backoff per la distribuzione dopo gli errori.
- **DeploymentMaxFailureCount**: valore predefinito: 20. Verrà eseguito un nuovo tentativo di distribuzione dell'applicazione in caso di `DeploymentMaxFailureCount` errore della distribuzione dell'applicazione nel nodo.

### <a name="deactivation"></a>Disattivazione
- **DeactivationScanInterval**: valore predefinito: 600 secondi. Tempo minimo concesso al ServicePackage per ospitare una replica se non è mai stata ospitata alcuna replica, ovvero se non viene utilizzata.
- **DeactivationGraceInterval**: valore predefinito: 60 secondi. In un modello di processo *condiviso* , il tempo assegnato a un ServicePackage per ospitare nuovamente un'altra replica dopo che è già stata ospitata una replica.
- **ExclusiveModeDeactivationGraceInterval**: valore predefinito: 1 secondo. In un modello di processo *esclusivo* , il tempo assegnato a un ServicePackage per ospitare nuovamente un'altra replica dopo che è già stata ospitata una replica.

## <a name="next-steps"></a>Passaggi successivi

- [Creare il pacchetto di un'applicazione][a3] e prepararlo per la distribuzione.
- [Distribuire e rimuovere applicazioni][a4] in PowerShell.

<!--Link references--In actual articles, you only need a single period before the slash-->
[a1]: service-fabric-application-model.md
[a2]: service-fabric-hosting-model.md
[a3]: service-fabric-package-apps.md
[a4]: service-fabric-deploy-remove-applications.md

[p1]: /powershell/module/servicefabric/copy-servicefabricservicepackagetonode
