---
title: Manutenzione pianificata - Database di Azure per MySQL - Server flessibile
description: Questo articolo descrive la funzionalità di manutenzione pianificata in Database di Azure per MySQL - Server flessibile.
author: niklarin
ms.author: nlarin
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 424402db1933c0a20ddd25a6e5af11d84d0775a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481158"
---
# <a name="scheduled-maintenance-in-azure-database-for-mysql--flexible-server"></a>Manutenzione pianificata nel Database di Azure per MySQL - Server flessibile

Database di Azure per MySQL: il server flessibile esegue una manutenzione periodica per mantenere il database gestito sicuro, stabile e aggiornato. Durante la manutenzione, il server ottiene nuove funzionalità, aggiornamenti e patch.

> [!IMPORTANT]
> Database di Azure per MySQL: il server flessibile è in anteprima.

## <a name="select-a-maintenance-window"></a>Selezionare una finestra di manutenzione

È possibile pianificare la manutenzione durante un giorno specifico della settimana e un intervallo di tempo all'interno di tale giorno. Oppure è possibile consentire al sistema di scegliere automaticamente un giorno e un'ora dell'intervallo di tempo. In entrambi i modi, il sistema avvisa l'utente cinque giorni prima di eseguire qualsiasi manutenzione. Il sistema consente anche di sapere quando viene avviata la manutenzione e quando viene completata correttamente.

Le notifiche relative alla manutenzione pianificata futura possono essere:

* Inviato tramite posta elettronica a un indirizzo specifico
* Inviato tramite posta elettronica a un Azure Resource Manager ruolo
* Inviato in un SMS ai dispositivi mobili
* un push di notifica a un'app di Azure
* un messaggio vocale

Quando si specificano le preferenze per il programma di manutenzione, è possibile scegliere un giorno della settimana e un intervallo di tempo. Se non lo si fa, il sistema sceglierà un'ora tra le 23:00 e le 07:00 nell'ora dell'area del server. È possibile definire pianificazioni diverse per ogni server flessibile nella sottoscrizione di Azure.

> [!IMPORTANT]
> Normalmente, per un server, trascorrono almeno 30 giorni tra un evento di manutenzione pianificata eseguito correttamente e l'altro.
>
> Tuttavia, nel caso di un aggiornamento critico di emergenza, ad esempio una vulnerabilità grave, la finestra di notifica potrebbe essere inferiore a cinque giorni. È possibile applicare l'aggiornamento critico al server anche se è stata eseguita correttamente una manutenzione pianificata negli ultimi 30 giorni.

È possibile aggiornare le impostazioni di pianificazione in qualsiasi momento. Se è pianificata una manutenzione per il server flessibile e si aggiornano le preferenze di pianificazione, l'implementazione corrente continuerà come pianificato e la modifica delle impostazioni di pianificazione diventerà effettiva al termine della successiva manutenzione pianificata.

È possibile definire una pianificazione gestita dal sistema o una pianificazione personalizzata per ogni server flessibile nella sottoscrizione di Azure.  
* Con la pianificazione personalizzata, è possibile specificare la finestra di manutenzione per il server scegliendo il giorno della settimana e un intervallo di un'ora.  
* Con la pianificazione gestita dal sistema, il sistema sceglierà qualsiasi intervallo di un'ora tra le 23:00 e le 7:00 nell'ora dell'area del server.  

Nell'ambito dell'implementazione delle modifiche, gli aggiornamenti vengono applicati ai server configurati con pianificazione gestita dal sistema, seguiti prima dai server con pianificazione personalizzata dopo un intervallo minimo di 7 giorni all'interno di una determinata area. Se si prevede di ricevere aggiornamenti anticipati sui server dell'ambiente di sviluppo e test, è consigliabile configurare la pianificazione gestita dal sistema per i server usati nell'ambiente di sviluppo e test. In questo modo sarà possibile ricevere prima l'aggiornamento più recente nell'ambiente di sviluppo/test per il test e la valutazione per la convalida. Se si verificano modifiche di comportamento o di rilievo, sarà necessario risolvere i problemi prima dell'implementazione dello stesso aggiornamento nei server di produzione con una pianificazione gestita personalizzata. L'aggiornamento inizia l'implementazione nei server flessibili con pianificazione personalizzata dopo 7 giorni e viene applicato al server nella finestra di manutenzione definita. Al momento non è possibile rinviare l'aggiornamento dopo l'invio della notifica. La pianificazione personalizzata è consigliata solo per gli ambienti di produzione. 

In rari casi, l'evento di manutenzione può essere annullato dal sistema o potrebbe non essere completato correttamente. Se l'aggiornamento non riesce, l'aggiornamento verrà ripristinato e verrà ripristinata la versione precedente dei file binari. In questi scenari di aggiornamento non riuscito, è comunque possibile che si verifichi il riavvio del server durante la finestra di manutenzione. Se l'aggiornamento viene annullato o non riuscito, il sistema creerà una notifica rispettivamente relativa all'evento di manutenzione annullato o non riuscito. Il tentativo successivo di eseguire la manutenzione verrà pianificato in base alle impostazioni di pianificazione correnti e si riceverà una notifica con cinque giorni di anticipo. 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [modificare la pianificazione della manutenzione](how-to-maintenance-portal.md)
* Informazioni su come [ricevere notifiche sulla manutenzione imminente](../../service-health/service-notifications.md) usando integrità dei servizi di Azure
* Informazioni su come [configurare avvisi sugli eventi di manutenzione pianificata imminenti](../../service-health/resource-health-alert-monitor-guide.md)
