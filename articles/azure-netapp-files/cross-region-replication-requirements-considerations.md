---
title: Requisiti e considerazioni per l'uso della replica Azure NetApp Files volume tra aree | Microsoft Docs
description: Vengono descritti i requisiti e le considerazioni per l'utilizzo della funzionalità di replica tra aree del volume di Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: b-juche
ms.openlocfilehash: ed03e20f7a1a24d1a38e023b958959fdc6fdc326
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579340"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Requisiti e considerazioni per l'uso della replica tra aree 

Tenere presente i requisiti e le considerazioni seguenti sull'uso della funzionalità di [replica tra aree del volume](cross-region-replication-create-peering.md) di Azure NetApp Files:  

## <a name="requirements-and-considerations"></a>Problemi e considerazioni 

* La funzionalità di replica tra aree è attualmente disponibile in anteprima pubblica. È necessario inviare una richiesta di attesa per l'accesso alla funzionalità tramite la pagina Azure NetApp Files l'invio dell'oggetto di attesa per la [replica tra aree](https://aka.ms/anfcrrpreviewsignup). Attendere un messaggio di posta elettronica di conferma ufficiale dal team di Azure NetApp Files prima di usare la funzionalità di replica tra aree.
* Azure NetApp Files replica è disponibile solo in alcune coppie di aree fisse. Vedere [coppie di aree supportate](cross-region-replication-introduction.md#supported-region-pairs). 
* I volumi SMB sono supportati insieme ai volumi NFS. Per la replica dei volumi SMB è necessaria una connessione Active Directory negli account NetApp di origine e di destinazione. La connessione AD di destinazione deve avere accesso ai server DNS o aggiungere controller di dominio raggiungibili dalla subnet delegata nell'area di destinazione. Per ulteriori informazioni, vedere [requisiti per le connessioni Active Directory](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* L'account di destinazione deve trovarsi in un'area diversa dall'area del volume di origine. È anche possibile selezionare un account NetApp esistente in un'area diversa.  
* Il volume di destinazione della replica è di sola lettura fino a quando non viene eseguito il [failover nell'area di destinazione](cross-region-replication-manage-disaster-recovery.md#fail-over-to-destination-volume) per abilitare il volume di destinazione per la lettura e la scrittura. 
* Azure NetApp Files replica non supporta attualmente più sottoscrizioni. tutte le repliche devono essere eseguite in una singola sottoscrizione.
* È possibile configurare un massimo di cinque volumi per la replica all'interno di una singola sottoscrizione per area. È possibile aprire un ticket di supporto per richiedere un aumento della quota predefinita di cinque volumi di destinazione della replica (per sottoscrizione in un'area). 
* Ci possono essere un ritardo fino a cinque minuti affinché l'interfaccia rifletta uno snapshot appena aggiunto nel volume di origine.  
* Le topologie di propagazione e di ventilazione in/out non sono supportate.
* La configurazione della replica del volume per i volumi di origine creati da uno snapshot non è supportata in questo momento.
* Dopo aver configurato la replica tra aree, il processo di replica crea *snapshot SnapMirror* per fornire riferimenti tra il volume di origine e il volume di destinazione. Gli snapshot SnapMirror vengono automaticamente ciclati quando ne viene creato uno nuovo per ogni trasferimento incrementale. Non è possibile eliminare gli snapshot SnapMirror fino a quando non viene eliminata la relazione di replica e il volume. 
* È possibile eliminare gli snapshot manuali nel volume di origine di una relazione di replica quando la relazione di replica è attiva o interruppe e anche dopo l'eliminazione della relazione di replica. Non è possibile eliminare gli snapshot manuali per il volume di destinazione fino a quando la relazione di replica non viene interruppe.
* Non è possibile ripristinare uno snapshot creato prima della creazione del volume di destinazione della replica.

## <a name="next-steps"></a>Passaggi successivi
* [Creare la replica di un volume](cross-region-replication-create-peering.md)
* [Visualizzare lo stato integrità della relazione di replica](cross-region-replication-display-health-status.md)
* [Gestire il ripristino di emergenza](cross-region-replication-manage-disaster-recovery.md)
* [Metriche di replica del volume](azure-netapp-files-metrics.md#replication)
* [Eliminare repliche di volumi o volumi](cross-region-replication-delete.md)
* [Risolvere i problemi relativi alla replica tra più aree](troubleshoot-cross-region-replication.md)


