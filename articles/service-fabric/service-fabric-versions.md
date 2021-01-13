---
title: Versioni cluster supportate in Azure Service Fabric
description: Informazioni sulle versioni del cluster in Azure Service Fabric, incluso un collegamento alle versioni più recenti dal Blog del team di Service Fabric.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: abf9900e9749a1b21d927e0315076b38848bea10
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132648"
---
# <a name="supported-service-fabric-versions"></a>Versioni di Service Fabric supportate

Verificare che il cluster esegua sempre una versione di Azure Service Fabric supportata. Almeno 60 giorni dopo l'annuncio del rilascio di una nuova versione di Service Fabric, termina il supporto per la versione precedente. Gli annunci di nuove versioni sono disponibili nel Blog del [team di Service Fabric](https://azure.microsoft.com/updates/?product=service-fabric).

Per una versione specifica del runtime di Service Fabric è possibile usare le versioni precedenti o specificate dei pacchetti SDK/NuGet. Le versioni più recenti dei pacchetti non sono supportate ed è possibile che si verifichino problemi con i cluster meno recenti, perché potrebbero avere modifiche di funzionalità o di protocollo non supportate da tali ambienti.

Per informazioni dettagliate su come fare in modo che il cluster esegua una versione di Service Fabric supportata, vedere i documenti seguenti:

- [Aggiornare un cluster di Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Aggiornare la versione di Service Fabric eseguita nel cluster di Windows Server autonomo](service-fabric-cluster-upgrade-windows-server.md)


## <a name="unsupported-versions"></a>Versioni non supportate

### <a name="upgrade-alert-for-versions-between-57-and-below-6363"></a>Avviso di aggiornamento per le versioni comprese tra 5,7 e 6.3.63. *
Per migliorare la sicurezza e la disponibilità, l'infrastruttura di Azure apporterà una modifica che potrebbe influire sui clienti Service Fabric. **Sono interessati tutti i cluster Service Fabric su versioni non supportate da 5,7 a 6,3.** Per risolvere il cambiamento è necessario un aggiornamento del Service Fabric Runtime, che è già disponibile per tutte le versioni di Service Fabric supportate in tutte le aree.

Si consiglia di effettuare un'azione per eseguire l'aggiornamento alle versioni più recenti supportate del **19 gennaio, 2021** per evitare le rotture dei servizi se si dispone di un piano di supporto ed è necessario assistenza tecnica, contattare Microsoft tramite i canali di supporto di Azure aprendo una richiesta di supporto per Azure Service Fabric e citando questo contesto nel ticket di supporto.

#### <a name="impact-if-not-upgraded-to-supported-versions"></a>Effetto se non aggiornato alle versioni supportate

**Cluster di Azure Service Fabric eseguiti in versioni non supportate da 5,7 a 6.3.63. \* non sarà in grado di diventare disponibile e non sarà disponibile** se non è stato eseguito l'aggiornamento a una delle seguenti versioni supportate entro il 19 gennaio 2021.

#### <a name="required-action"></a>Azione richiesta
Eseguire l'aggiornamento a Service Fabric versioni supportate elencate di seguito per evitare tempi di inattività o perdita di funzionalità correlate a questa modifica. Assicurarsi che i cluster eseguano almeno queste versioni per evitare problemi nell'ambiente.

  ###### <a name="supported-service-fabric-runtime-versions"></a>Versioni di Runtime Service Fabric supportate
   Se il servizio non è disponibile nelle versioni di Service Fabric supportate elencate di seguito, eseguire l'aggiornamento a una di queste versioni che contengono già le modifiche necessarie per evitare tempi di inattività per il cluster.  
  
  | OS | Runtime Service Fabric corrente nel cluster | Versione CU/patch  | 
  | --- | --- |--- | 
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7.2.445.9590 |
  | Ubuntu 16 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16.04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16.04 | 7,2. * | 7.2.447.1 |
  | Linux Ubuntu 18,04 | 7,2. * | 7.2.447.1804 |
 
### <a name="upgrade-alert-for-versions-greater-than-63"></a>Avviso di aggiornamento per versioni superiori a 6,3 
Per migliorare la sicurezza e la disponibilità, l'infrastruttura di Azure apporterà una modifica che potrebbe influire sui clienti Service Fabric. **Tutti i cluster Service fabric che usano [la funzionalità di rete aperta per i contenitori](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode), vengono eseguiti su versioni non supportate maggiori di 6,3 e inferiori a 7,0 e le versioni supportate non compatibili a partire da 7,0 in poi sono interessate**. Per risolvere il cambiamento è necessario un aggiornamento del Service Fabric Runtime, che è già disponibile per tutte le versioni di Service Fabric supportate in tutte le aree.

 #### <a name="impact-if-not-upgraded-to-supported-versions"></a>Effetto se non aggiornato alle versioni supportate
  I cluster di Service Fabric di Azure che **usano [la funzionalità di rete aperta per i](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode) contenitori per i contenitori e per le esecuzioni con versioni successive a 6,3** che non includono modifiche comportano la perdita di funzionalità o di interruzione del servizio se non vengono aggiornati a una delle seguenti versioni supportate dal **19 gennaio 2021**.
 
  - **Per i cluster che eseguono una versione di Service Fabric maggiore di 6,3 senza la funzionalità di rete aperta**, il cluster rimarrà attivo, ma la funzionalità di rete aperta per i cluster di contenitori smette di funzionare, causando interruzioni del servizio per i carichi di lavoro.

 - **Per i cluster che eseguono una versione di Service Fabric maggiore di 6,3 e usano la [funzionalità di rete aperta per i contenitori](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode)** , il cluster rimarrà attivo, ma smette di funzionare, causando interruzioni del servizio per i carichi di lavoro.
  
#### <a name="required-action"></a>Azione richiesta
Eseguire l'aggiornamento a Service Fabric versioni supportate elencate di seguito per evitare tempi di inattività o perdita di funzionalità correlate a questa modifica. Assicurarsi che i cluster eseguano almeno queste versioni per evitare problemi nell'ambiente. 
 
 ###### <a name="supported-service-fabric-runtime-versions"></a>Versioni di Runtime Service Fabric supportate
 Se non si dispone delle seguenti versioni supportate di Service Fabric, eseguire l'aggiornamento a una di queste versioni che contengono già le modifiche necessarie per evitare la perdita di funzionalità.  
 
  | OS | Runtime Service Fabric corrente nel cluster | Versione CU/patch  | 
  | --- | --- |--- | 
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7.2.445.9590 |
  | Linux Ubuntu 16.04 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16.04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16.04 | 7,2. * | 7.2.447.1 |
  | Linux Ubuntu 18,04 | 7,2. * | 7.2.447.1804 |

## <a name="supported-versions"></a>Versioni supportate
Nella tabella seguente sono elencate le versioni di Service Fabric e le date di fine del supporto.

| Runtime di Service Fabric nel cluster | Possibilità di aggiornamento diretto dalla versione del cluster |SDK compatibile o versione del pacchetto NuGet | Fine del supporto |
| --- | --- |--- | --- |
| Tutte le versioni del cluster prima di 5.3.121 | 5.1.158.* |Versione 2.3 o precedente |20 gennaio 2017 |
| 5.3.* | 5.1.158.* |Versione 2.3 o precedente |24 febbraio 2017 |
| 5.4.* | 5.1.158.* |Versione 2.4 o precedente |10 maggio 2017       |
| 5.5.* | 5.4.164.* |Versione 2.5 o precedente |10 agosto 2017    |
| 5.6.* | 5.4.164.* |Versione 2.6 o precedente |13 ottobre 2017   |
| 5.7.* | 5.4.164.* |Versione 2.7 o precedente |15 dicembre 2017  |
| 6.0.* | 5.6.205.* |Versione 2.8 o precedente |30 marzo 2018     |
| 6.1.* | 5.7.221.* |Versione 3.0 o precedente |15 luglio 2018      |
| 6.2.* | 6.0.232.* |Versione 3.1 o precedente |26 ottobre 2018   |
| 6.3.* | 6.1.480.* |Versione 3.2 o precedente |31 marzo 2019  |
| 6.4.* | 6.2.301.* |Versione 3.3 o precedente |15 settembre 2019 |
| 6,5. * | 6.4.617.* |Minore o uguale alla versione 3,4 |1 agosto 2020 |
| 7.0.466.* | 6.4.664.* |Minore o uguale alla versione 4,0|31 gennaio 2021  |
| 7.0.466.* | 6,5. * |Minore o uguale alla versione 4,0|31 gennaio 2021 |
| 7.0.470.* | 7.0.466.* |Minore o uguale alla versione 4,0 |31 gennaio 2021  |
| 7.0.472.* | 7.0.466.* |Minore o uguale alla versione 4,0 |31 gennaio 2021  |
| 7.0.478.* | 7.0.466.* |Minore o uguale alla versione 4,0 |31 gennaio 2021  |
| 7.1.409.* | 7.0.466.* |Minore o uguale alla versione 4,1 |31 marzo 2021 |
| 7.1.417.* | 7.0.466.* |Minore o uguale alla versione 4,1 |31 marzo 2021 |
| 7.1.428.* | 7.0.466.* |Minore o uguale alla versione 4,1 |31 marzo 2021 |
| 7.1.456.* | 7.0.466.* |Minore o uguale alla versione 4,1 |31 marzo 2021 |
| 7.1.458.* | 7.0.466.* |Minore o uguale alla versione 4,1 |31 marzo 2021 |
| 7.1.459.* | 7.0.466.* |Minore o uguale alla versione 4,1 |31 marzo 2021 |
| 7.1.503.* | 7.0.466.* |Minore o uguale alla versione 4,1 |31 marzo 2021 |
| 7.2.413.* | 7.0.470.* |Minore o uguale alla versione 4,2 |Versione corrente, quindi nessuna data di fine |
| 7.2.432.* | 7.0.470.* |Minore o uguale alla versione 4,2 |Versione corrente, quindi nessuna data di fine |
| 7.2.433.* | 7.0.470.* |Minore o uguale alla versione 4,2 |Versione corrente, quindi nessuna data di fine |
| 7.2.445.* | 7.0.470.* |Minore o uguale alla versione 4,2 |Versione corrente, quindi nessuna data di fine |

## <a name="supported-operating-systems"></a>Sistemi operativi supportati

Nella tabella seguente sono elencati i sistemi operativi supportati per le versioni di Service Fabric supportate.

| Sistema operativo | Versione Service Fabric supportata più recente |
| --- | --- |
| Windows Server 2012 R2 | Tutte le versioni |
| Windows Server 2016 | Tutte le versioni |
| Windows Server 1709 | 6.0 |
| Windows Server 1803 | 6.4 |
| Windows Server 1809 | 6.4.654.9590 |
| Windows Server 2019 | 6.4.654.9590 |
| Linux Ubuntu 16.04 | 6.0 |
| Linux Ubuntu 18,04 | 7.1 |

## <a name="supported-version-names"></a>Nomi di versione supportati

Nella tabella seguente sono elencati i nomi delle versioni di Service Fabric e i numeri di versione corrispondenti.

| Nome della versione | Numero di versione di Windows | Numero di versione di Linux |
| --- | --- | --- |
| 5,3 RTO | 5.3.121.9494 | N/D |
| 5,3 CU1 | 5.3.204.9494 | N/D |
| 5,3 CU2 | 5.3.301.9590 | N/D |
| 5,3 CU3 | 5.3.311.9590 | N/D |
| 5,4 CU2 | 5.4.164.9494 | N/D |
| 5,5 CU1 | 5.5.216.0    | N/D |
| 5,5 CU2 | 5.5.219.0    | N/D |
| 5,5 CU3 | 5.5.227.0    | N/D |
| 5,5 CU4 | 5.5.232.0 | N/D |
| 5,6 RTO | 5.6.204.9494 | N/D |
| 5,6 CU2 | 5.6.210.9494 | N/D |
| 5,6 CU3 | 5.6.220.9494 | N/D |
| 5,7 RTO | 5.7.198.9494 | N/D |
| 5,7 CU4 | 5.7.221.9494 | N/D |
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | N/D |
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 | 
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | N/D |
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | N/D |
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | N/D |
| 6,5 RTO | 6.5.639.9590 | 6.5.435.1 |
| 6,5 CU1 | 6.5.641.9590 | 6.5.454.1 |
| 6,5 CU2 | 6.5.658.9590 | 6.5.460.1 |
| 6,5 CU3 | 6.5.664.9590 | 6.5.466.1 |
| 6,5 CU5 | 6.5.676.9590 | 6.5.467.1 |
| 7,0 RTO | 7.0.457.9590 | 7.0.457.1 |
| 7,0 CU2 | 7.0.464.9590 | 7.0.464.1 |
| 7,0 CU3 | 7.0.466.9590 | 7.0.465.1 |
| 7,0 CU4 | 7.0.470.9590 | 7.0.469.1 |
| 7,0 CU6 | 7.0.472.9590 | 7.0.471.1 |
| 7,0 CU9 | 7.0.478.9590 | 7.0.472.1 |
| 7,1 RTO | 7.1.409.9590 | 7.1.410.1 |
| 7,1 CU1 | 7.1.417.9590 | 7.1.418.1 |
| 7,1 CU2 | 7.1.428.9590 | 7.1.428.1 |
| 7,1 CU3 | 7.1.456.9590 | 7.1.452.1 |
| 7,1 CU5 | 7.1.458.9590 | 7.1.454.1 |
| 7,1 CU6 | 7.1.459.9590 | 7.1.455.1 |
| 7,1 CU8 | 7.1.503.9590 | 7.1.508.1 |
| 7,2 RTO | 7.2.413.9590 | N/D |
| 7,2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7,2 CU3 | 7.2.433.9590 | N/D |
| 7,2 CU4 | 7.2.445.9590 | 7.2.447.1 |

