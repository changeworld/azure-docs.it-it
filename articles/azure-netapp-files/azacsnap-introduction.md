---
title: Che cos'è applicazione Azure strumento di snapshot coerente per Azure NetApp Files | Microsoft Docs
description: Viene fornita un'introduzione allo strumento di snapshot coerente applicazione Azure che è possibile utilizzare con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: b168167ce4f44d87c396746cca3f271f95f83163
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632823"
---
# <a name="what-is-azure-application-consistent-snapshot-tool-preview"></a>Che cos'è applicazione Azure strumento di snapshot coerente (anteprima)

Applicazione Azure strumento di snapshot coerente (AzAcSnap) è uno strumento da riga di comando che consente di semplificare la protezione dei dati per i database di terze parti (SAP HANA) negli ambienti Linux (ad esempio, SUSE e RHEL).  

## <a name="benefits-of-using-azacsnap"></a>Vantaggi dell'uso di AzAcSnap

AzAcSnap sfrutta le funzionalità di snapshot e replica del volume disponibili in Azure NetApp Files e in istanze Large di Azure.  e in grado di offrire i vantaggi indicati di seguito.

- **Protezione dei dati coerente** con l'applicazione AzAcSnap è una soluzione centralizzata per il backup dei file di database critici. Garantisce la coerenza del database prima di eseguire uno snapshot del volume di archiviazione. Di conseguenza, assicura che lo snapshot del volume di archiviazione possa essere usato per il ripristino del database.
- **Gestione del catalogo di database** Quando si usa AzAcSnap con un database che include un catalogo di backup incorporato, i record all'interno del catalogo vengono mantenuti aggiornati con gli snapshot di archiviazione.  Questa funzionalità consente a un amministratore del database di visualizzare l'attività di backup.
- **Protezione del volume ad hoc** Questa funzionalità è utile per i volumi non di database che non necessitano di disattivazione dell'applicazione prima di creare uno snapshot di archiviazione.  Gli esempi includono SAP HANA volumi di backup del log o SAPTRANS.
- **Clonazione dei volumi di archiviazione** Questa funzionalità offre cloni dei volumi di archiviazione efficienti per lo spazio a scopo di sviluppo e test.
- **Supporto per il ripristino di emergenza** AzAcSnap sfrutta la replica del volume di archiviazione per fornire opzioni per il ripristino di snapshot coerenti con l'applicazione replicata in un sito remoto.

AzAcSnap è un singolo file binario.  Non sono necessari agenti aggiuntivi o plug-in per interagire con il database o con l'archiviazione (Azure NetApp Files tramite Azure Resource Manager e l'istanza di grandi dimensioni di Azure tramite SSH).  AzAcSnap deve essere installato in un sistema dotato di connettività al database e all'archiviazione.  Tuttavia, la flessibilità di installazione e configurazione consente una singola installazione centralizzata o un'installazione completamente distribuita con copie installate in ogni installazione del database.

## <a name="architecture-overview"></a>Panoramica dell'architettura

AzAcSnap può essere installato nello stesso host del database (SAP HANA) oppure può essere installato in un sistema centralizzato.  Tuttavia, deve essere disponibile la connettività di rete ai server di database e al back-end di archiviazione (Azure Resource Manager per Azure NetApp Files o SSH per istanze large di Azure).

AzAcSnap è un'applicazione leggera che in genere viene eseguita da un'utilità di pianificazione esterna.  Nella maggior parte dei sistemi Linux, questa operazione è `cron` , che è l'argomento su cui si focalizzerà la documentazione.  Tuttavia, l'utilità di pianificazione potrebbe essere uno strumento alternativo, purché possa importare il `azacsnap` profilo della shell dell'utente.  L'importazione delle impostazioni di ambiente dell'utente garantisce che i percorsi e le autorizzazioni dei file siano inizializzati correttamente.

## <a name="command-synopsis"></a>Riepilogo comandi

Il formato generale dei comandi è il seguente: `azacsnap -c [command] --[command] [sub-command] --[flag-name] [flag-value]` .

## <a name="command-options"></a>Opzioni del comando

Le opzioni di comando sono le seguenti: i comandi sono i punti elenco principale e i sottocomandi associati come punti elenco rientrati:

- **`-h`** fornisce la guida estesa della riga di comando con esempi sull'utilizzo di AzAcSnap.
- **`-c configure`** Questo comando fornisce un'interfaccia interattiva Q&un'interfaccia di stile per creare o modificare il `azacsnap` file di configurazione (impostazione predefinita = `azacsnap.json` ).
  - **`--configuration new`** creerà un nuovo file di configurazione.
  - **`--configuration edit`** modifica un file di configurazione esistente.
  - vedere la Guida di [riferimento ai comandi Configure](azacsnap-cmd-ref-configure.md).
- **`-c test`** convalida il file di configurazione e verifica la connettività.
  - **`--test hana`**  Verifica la connessione all'istanza di SAP HANA.
  - **`--test storage`** Verifica la comunicazione con l'interfaccia di archiviazione sottostante creando uno snapshot di archiviazione temporanea in tutti i volumi configurati `data` , quindi rimuoverli.
  - **`--test all`** eseguirà entrambi i `hana` `storage` test e in sequenza.
  - vedere le informazioni di [riferimento sui comandi di test](azacsnap-cmd-ref-test.md).
- **`-c backup`** è il comando principale per eseguire snapshot di archiviazione coerenti con i database per i dati (SAP HANA volumi di dati) & altri volumi (ad esempio, backup di log o condivisi).
  - **`--volume data`** per creare uno snapshot di tutti i volumi nella `dataVolume` strofa del file di configurazione.
  - **`--volume other`** per creare uno snapshot di tutti i volumi nella `otherVolume` strofa del file di configurazione.
  - vedere informazioni di [riferimento sui comandi di backup](azacsnap-cmd-ref-backup.md).
- **`-c details`** fornisce informazioni sugli snapshot o sulla replica.
  - **`--details snapshots`** Fornisce un elenco di dettagli di base sugli snapshot per ogni volume configurato.
  - **`--details replication`** Fornisce informazioni di base sullo stato della replica dal sito di produzione al sito di ripristino di emergenza.
  - vedere [informazioni di riferimento sui comandi](azacsnap-cmd-ref-details.md).
- **`-c delete`** Questo comando Elimina uno snapshot di archiviazione o un set di snapshot. È possibile usare l'ID di backup SAP HANA come disponibile in HANA Studio o il nome dello snapshot di archiviazione. L'ID di backup è collegato solo agli `hana` snapshot creati per i dati e i volumi condivisi. In caso contrario, se viene immesso il nome dello snapshot, vengono cercati tutti gli snapshot che corrispondono al nome di snapshot immesso.
  - vedere l' [eliminazione](azacsnap-cmd-ref-delete.md).
- **`-c restore`** in sono disponibili due metodi per ripristinare uno snapshot in un volume, creando un nuovo volume basato sullo snapshot o eseguendo il rollback di un volume in uno stato di anteprima.
  - **`--restore snaptovol`** Crea un nuovo volume basato sullo snapshot più recente nel volume di destinazione.
  - **`-c restore --restore revertvolume`** Ripristina lo stato precedente del volume di destinazione in base allo snapshot più recente.
  - vedere informazioni di [riferimento sui comandi Restore](azacsnap-cmd-ref-restore.md).
- **`[--configfile <configfilename>]`** Parametro della riga di comando facoltativo per fornire un nome file di configurazione JSON diverso.  Questa operazione è particolarmente utile per la creazione di un file di configurazione separato per SID (ad esempio `--configfile H80.json` ,).

## <a name="next-steps"></a>Passaggi successivi

- [Introduzione allo strumento di snapshot coerente con applicazione Azure](azacsnap-get-started.md)
