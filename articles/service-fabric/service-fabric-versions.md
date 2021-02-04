---
title: Aggiornare la versione del cluster di Azure Service Fabric
description: Informazioni sulle versioni del cluster in Azure Service Fabric, incluso un collegamento alle versioni più recenti dal Blog del team di Service Fabric.
ms.topic: troubleshooting
ms.date: 06/15/2020
ms.openlocfilehash: 5abfe83fcb68fcab7df22f1fd266cc695f2b9c80
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99549069"
---
# <a name="upgrade-your-azure-service-fabric-cluster-version"></a>Aggiornare la versione del cluster di Azure Service Fabric

Verificare che il cluster esegua sempre una versione supportata di Azure Service Fabric. Almeno 60 giorni dopo l'annuncio del rilascio di una nuova versione di Service Fabric, termina il supporto per la versione precedente. Gli annunci di nuove versioni sono disponibili nel Blog del [team di Service Fabric](https://azure.microsoft.com/updates/?product=service-fabric).

Per ogni versione del runtime di Service Fabric, è possibile usare le versioni precedenti o specificate dei pacchetti SDK/NuGet. Le versioni più recenti dei pacchetti potrebbero non essere in grado di individuare i cluster precedenti. I cluster precedenti potrebbero avere modifiche di funzionalità o di protocollo non supportate dagli ambienti di pacchetti più recenti.

Per informazioni dettagliate su come fare in modo che il cluster esegua una versione Service Fabric supportata, fare riferimento agli articoli seguenti:

- [Aggiornare un cluster di Azure Service Fabric](service-fabric-cluster-upgrade.md)
- [Aggiornare la versione di Service Fabric eseguita nel cluster di Windows Server autonomo](service-fabric-cluster-upgrade-windows-server.md)

## <a name="unsupported-versions"></a>Versioni non supportate

### <a name="upgrade-alert-for-versions-between-57-and-6363"></a>Avviso di aggiornamento per le versioni comprese tra 5,7 e 6.3.63. *

Per migliorare la sicurezza e la disponibilità, l'infrastruttura di Azure ha apportato una modifica che potrebbe influire sui clienti Service Fabric. Questa modifica ha effetto su tutti i cluster Service Fabric che eseguono le versioni da 5,7 a 6,3.

È disponibile un aggiornamento del runtime di Service Fabric per tutte le versioni di Service Fabric supportate in tutte le aree. Eseguire l'aggiornamento a una delle versioni supportate più recenti del 19 gennaio 2021, per evitare le rotture dei servizi.

Se si dispone di un piano di supporto ed è necessaria assistenza tecnica, contattare i canali di supporto di Azure. Aprire una richiesta di supporto per Azure Service Fabric e menzionare questo contesto nel ticket di supporto.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Se non si esegue l'aggiornamento a una versione supportata

I cluster di Azure Service Fabric eseguiti sulle versioni da 5,7 a 6.3.63. * non saranno disponibili se non è stato eseguito l'aggiornamento entro il 19 gennaio 2021.

#### <a name="required-action"></a>Azione obbligatoria

Eseguire l'aggiornamento a una versione di Service Fabric supportata per evitare tempi di inattività o perdita di funzionalità correlate a questa modifica. Assicurarsi che i cluster eseguano almeno le versioni seguenti per evitare problemi nell'ambiente.

> [!Note]
> **Tutte le versioni rilasciate di 7,2 includono le modifiche necessarie**.
  
  | OS | Runtime Service Fabric corrente nel cluster | Versione CU/patch |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Ubuntu 16 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16.04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16.04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18,04 | 7,2. * | 7,2. * |

### <a name="upgrade-alert-for-versions-later-than-63"></a>Avviso di aggiornamento per le versioni successive alla 6,3

Per migliorare la sicurezza e la disponibilità, l'infrastruttura di Azure ha apportato una modifica che potrebbe influire sui clienti Service Fabric. Questa modifica influirà su tutti i cluster Service Fabric che usano la [modalità di rete aperta per i contenitori](./service-fabric-networking-modes.md#set-up-open-networking-mode) ed eseguono le versioni da 6,3 a 7,0 o versioni supportate non compatibili successive a 7,0. È disponibile un aggiornamento del runtime di Service Fabric per tutte le versioni di Service Fabric supportate in tutte le aree.

#### <a name="if-you-dont-upgrade-to-a-supported-version"></a>Se non si esegue l'aggiornamento a una versione supportata

I cluster di Azure Service Fabric eseguiti in versioni non modificate successive a 6,3 subiranno una perdita di funzionalità o di interruzione del servizio se non sono stati aggiornati a una versione supportata dal 19 gennaio 2021.
  
  - **Per i cluster che eseguono una versione di Service Fabric maggiore di 6,3 senza usare la funzionalità di rete aperta**, il cluster rimarrà attivo.

 - **Per i cluster che eseguono una versione di Service Fabric superiore a 6,3 e usano la [funzionalità di rete aperta per i contenitori](https://docs.microsoft.com/azure/service-fabric/service-fabric-networking-modes#set-up-open-networking-mode)** , il cluster potrebbe non essere più disponibile e cesserà di funzionare, causando interruzioni del servizio per i carichi di lavoro.
 
 -   **Per i cluster che eseguono [versioni di Windows tra 7.0.457 e 7.0.466 (entrambe le versioni incluse)](https://docs.microsoft.com/azure/service-fabric/service-fabric-versions#supported-version-names) e il sistema operativo Windows è abilitata la funzionalità contenitori di Windows. Nota: le versioni di Linux 7.0.457, 7.0.464 e 7.0.465 non sono interessate**.
    - **Effetto**: il cluster smette di funzionare, causando interruzioni del servizio per i carichi di lavoro.
    
#### <a name="required-action"></a>Azione obbligatoria

Per evitare tempi di inattività o perdita di funzionalità, assicurarsi che i cluster eseguano una delle versioni seguenti.

Le versioni di Service Fabric nella tabella contengono le modifiche necessarie per evitare la perdita di funzionalità. Assicurarsi di usare una di queste versioni.  

> [!Note]
> **I cluster di Service fabric di Azure in esecuzione nella versione 6,5 devono eseguire più aggiornamenti contemporaneamente prima di modificare infrastucuture per evitare la perdita di funzionalità per il cluster**. 
>   -   1. Eseguire l'aggiornamento a 7.0.466. **I cluster che eseguono il sistema operativo Windows con la funzionalità contenitori di Windows abilitata non possono trovarsi in questa versione intermedia. È necessario eseguire il passaggio successivo (II), ad esempio  Aggiornamento in modo che sia più sicuro e conforme a versione per evitare le rotture del servizio**
>   -   2. Eseguire l'aggiornamento alle versioni più recenti dei reclami nella versione 7,0 * (7.0.478) o in una delle versioni più recenti elencate di seguito.


> [!Note]
> **Tutte le versioni di rilascio di 7,2 includono le modifiche necessarie**.

 | OS | Runtime Service Fabric corrente nel cluster | Versione CU/patch |
  | --- | --- |--- |
  | Windows | 7,0. * | 7.0.478.9590 |
  | Windows | 7,1. * | 7.1.503.9590 |
  | Windows | 7,2. * | 7,2. * |
  | Linux Ubuntu 16.04 | 7,0. * | 7.0.472.1  |
  | Linux Ubuntu 16.04 | 7,1. * | 7.1.455.1  |
  | Linux Ubuntu 18,04 | 7,1. * | 7.1.455.1804 |
  | Linux Ubuntu 16.04 | 7,2. * | 7,2. * |
  | Linux Ubuntu 18,04 | 7,2. * | 7,2. * |

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
| 7.1.510.* | 7.0.466.* |Minore o uguale alla versione 4,1 |31 marzo 2021 |
| 7.2.413.* | 7.0.470.* |Minore o uguale alla versione 4,2 |Versione corrente, quindi nessuna data di fine |
| 7.2.432.* | 7.0.470.* |Minore o uguale alla versione 4,2 |Versione corrente, quindi nessuna data di fine |
| 7.2.433.* | 7.0.470.* |Minore o uguale alla versione 4,2 |Versione corrente, quindi nessuna data di fine |
| 7.2.445.* | 7.0.470.* |Minore o uguale alla versione 4,2 |Versione corrente, quindi nessuna data di fine |
| 7.2.452.* | 7.0.470.* |Minore o uguale alla versione 4,2 |Versione corrente, quindi nessuna data di fine |

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
| 5,3 RTO | 5.3.121.9494 | Non applicabile|
| 5,3 CU1 | 5.3.204.9494 | Non applicabile|
| 5,3 CU2 | 5.3.301.9590 | Non applicabile|
| 5,3 CU3 | 5.3.311.9590 | Non applicabile|
| 5,4 CU2 | 5.4.164.9494 | Non applicabile|
| 5,5 CU1 | 5.5.216.0    | Non applicabile|
| 5,5 CU2 | 5.5.219.0 | Non applicabile|
| 5,5 CU3 | 5.5.227.0 | Non applicabile|
| 5,5 CU4 | 5.5.232.0 | Non applicabile|
| 5,6 RTO | 5.6.204.9494 | Non applicabile|
| 5,6 CU2 | 5.6.210.9494 | Non applicabile|
| 5,6 CU3 | 5.6.220.9494 | Non applicabile|
| 5,7 RTO | 5.7.198.9494 | Non applicabile|
| 5,7 CU4 | 5.7.221.9494 | Non applicabile|
| 6,0 RTO | 6.0.211.9494 | 6.0.120.1 |
| 6,0 CU1 | 6.0.219.9494 | 6.0.127.1 |
| 6,0 CU2 | 6.0.232.9494 | 6.0.133.1 |
| 6,1 CU1 | 6.1.456.9494 | 6.1.183.1 |
| 6,1 CU2 | 6.1.467.9494 | 6.1.185.1 |
| 6,1 CU3 | 6.1.472.9494 | Non applicabile|
| 6,1 CU4 | 6.1.480.9494 | 6.1.187.1 |
| 6,2 RTO | 6.2.269.9494 | 6.2.184.1 |
| 6,2 CU1 | 6.2.274.9494 | 6.2.191.1 |
| 6,2 CU2 | 6.2.283.9494 | 6.2.194.1 |
| 6,2 CU3 | 6.2.301.9494 | 6.2.199.1 |
| 6,3 RTO | 6.3.162.9494 | 6.3.119.1 |
| 6,3 CU1 | 6.3.176.9494 | 6.3.124.1 |
| 6,3 CU1 | 6.3.187.9494 | 6.3.129.1 |
| 6,4 RTO | 6.4.617.9590 | 6.4.625.1 |
| 6,4 CU2 | 6.4.622.9590 | Non applicabile|
| 6,4 CU3 | 6.4.637.9590 | 6.4.634.1 |
| 6,4 CU4 | 6.4.644.9590 | 6.4.639.1 |
| 6,4 CU5 | 6.4.654.9590 | 6.4.649.1 |
| 6,4 CU6 | 6.4.658.9590 | Non applicabile|
| 6,4 CU7 | 6.4.664.9590 | 6.4.661.1 |
| 6,4 CU8 | 6.4.670.9590 | Non applicabile|
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
| 7,1 CU10 DALLA | 7.1.510.9590 | N/D |
| 7,2 RTO | 7.2.413.9590 | N/D |
| 7,2 CU2 | 7.2.432.9590 | 7.2.431.1 |
| 7,2 CU3 | 7.2.433.9590 | Non applicabile|
| 7,2 CU4 | 7.2.445.9590 | 7.2.447.1 |
| 7,2 CU5 | 7.2.452.9590 | 7.2.454.1 |
