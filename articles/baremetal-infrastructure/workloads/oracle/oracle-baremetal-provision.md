---
title: Effettuare il provisioning di BareMetal per Oracle
description: Informazioni sul provisioning dell'infrastruttura BareMetal per Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 45ca2bf8bfc61c3820b4b14daa998e2e82e972fa
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559095"
---
# <a name="provision-baremetal-for-oracle"></a>Effettuare il provisioning di BareMetal per Oracle

Questo articolo illustra come effettuare il provisioning delle istanze dell'infrastruttura BareMetal per i carichi di lavoro Oracle. 

Il primo passaggio per effettuare il provisioning delle istanze BareMetal consiste nell'usare l'account del servizio di supporto microsoft. Saranno utili in base alle esigenze specifiche del carico di lavoro e all'architettura che si sta distribuendo, ad esempio istanza singola, RAC a un nodo o RAC. Per altre informazioni su queste topologie, vedere [Architettura dell'infrastruttura BareMetal per Oracle.](oracle-baremetal-architecture.md)

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure attiva
> * Contratto di supporto tecnico Microsoft Premier
> * Licenze per Red Hat Enterprise Linux 7.6
> * Contratto di supporto Oracle 
> * Licenze e componenti di installazione software per Oracle
> * ExpressRoute connesso **in locale** ad Azure (facoltativamente, configurare ExpressRoute Copertura globale per la connettività diretta dall'ambiente locale al Oracle Database)   
> * Rete virtuale
> * Creazione di gateway
> * Macchine virtuali (VM) nella rete virtuale (jump box)

## <a name="information-to-provide-microsoft-operations"></a>Informazioni da fornire a Microsoft Operations

È necessario fornire le informazioni seguenti all'amministratore del servizio di configurazione:

1. Spazio indirizzi della rete virtuale. Questo intervallo deve essere /24 subnet; ad esempio 10.11.0.0/24.
2. Intervallo P2P. Questo intervallo deve essere una subnet /29. ad esempio 10.12.0.0/29.
3. Pool di indirizzi IP del server. L'intervallo consigliato è /24; ad esempio 10.13.0.0/24.
4. Indirizzo IP del server. Selezionare un indirizzo IP dal pool di indirizzi IP del server.

    > [!Note] 
    > I primi 30 indirizzi IP sono riservati per la configurazione dell'infrastruttura Microsoft. In questo esempio, il primo indirizzo IP disponibile per un pannello sarà quindi 10.13.0.30.

5. L'area di Azure richiesta; ad esempio Stati Uniti occidentali 2.
6. È necessario lo SKU dell'infrastruttura BareMetal. ad esempio S192 (due computer).

## <a name="storage-requirements"></a>Requisiti di archiviazione

Collaborare con il rappresentante CSA per le esigenze di archiviazione durante la richiesta di provisioning, incluse le esigenze di archiviazione previste in base alla crescita futura. L'archiviazione aggiunta è in incrementi di 1 TB.

Per i volumi, viene seguito lo [standard OFA (Optimal Flexible Architecture) di](https://docs.oracle.com/en/database/oracle/oracle-database/19/ladbi/about-the-optimal-flexible-architecture-standard.html#GUID-6619CDB7-9667-426E-8471-5A996707D093)Oracle con il livello Basic e la configurazione Enterprise. Se si hanno requisiti di archiviazione personalizzati diversi dal "dimensionamento delle t-shirt" standard, effettuare la richiesta personalizzata tramite csa.

| Configurazione di base (POC/Test) | Descrizione | Piccolo | Medio | Grande |
| --- | --- | --- | --- | --- |
| /u01 | File binari Oracle | 500 GB | 500 GB | 500 GB |
| /u02 | Uso intensivo della lettura/amministrazione | 500 GB | 1 TB | 5 TB |
| /u03 | Scrittura con utilizzo intensivo/log | 500 GB | 1 TB | 5 TB |
| /u09 | Backup | 5 TB | 10 TB | 15 TB |

| Configurazione organizzazione | Descrizione | Piccolo | Medio | Grande | Molto grande |
| --- | --- | --- | --- | --- | --- |
| /u01 | File binari Oracle | 500 GB | 500 GB | 500 GB | 500 GB |
| /u02 | Amministrativi | 100 GB | 100 GB | 100 GB | 100 GB |
| Da /u10 a /u59 | Utilizzo intensivo delle operazioni di lettura | 500 GB | 5 TB | 10 TB | 20 TB |
| Da /u60 a /u89 | Utilizzo intensivo delle operazioni di scrittura | 500 GB | 5 TB | 10 TB | 20 TB |
| Da /u90 a /u91 | Log di ripeti | 500 GB | 500 GB | 1 TB | 1 TB |
| /u95 | Archiviazione | 10 TB | 10 TB | 20 TB | 20 TB |
| /u98 | Backup | 25 TB | 25 TB | 50 TB | 50 TB |

## <a name="next-step"></a>Passaggio successivo

Altre informazioni sull'infrastruttura BareMetal per Oracle.

> [!div class="nextstepaction"]
> [Che cos'è l'infrastruttura BareMetal in Azure?](../../concepts-baremetal-infrastructure-overview.md)
