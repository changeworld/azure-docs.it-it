---
title: Gestire le notifiche sui dispositivi
description: Le notifiche forniscono informazioni sulle attività di rete che potrebbero richiedere attenzione, oltre a indicazioni per la gestione di questa attività.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/12/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: cac8d609272be1d9f34b7e0d6404e0a0ea524df7
ms.sourcegitcommit: b85ce02785edc13d7fb8eba29ea8027e614c52a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99509032"
---
# <a name="work-with-device-notifications"></a>Gestire le notifiche sui dispositivi

Le notifiche forniscono informazioni sulle attività di rete che potrebbero richiedere attenzione, oltre a indicazioni per la gestione di questa attività. Ad esempio, è possibile ricevere una notifica relativa a:

- Un dispositivo inattivo. Se il dispositivo non fa più parte della rete, è possibile rimuoverlo. Se il dispositivo è inattivo, ad esempio perché è erroneamente disconnesso dalla rete, riconnettere il dispositivo ed eliminare la notifica.

- Un indirizzo IP è stato rilevato in un dispositivo attualmente identificato da un solo indirizzo MAC. Rispondere autorizzando l'indirizzo IP del dispositivo.

La risposta alle notifiche consente di migliorare le informazioni fornite nella mappa del dispositivo, nell'inventario dei dispositivi e nelle query e nei report di data mining. Fornisce inoltre informazioni dettagliate sulle modifiche di rete legittime e sui potenziali problemi di configurazione della rete.

Per accedere alle notifiche:

- Selezionare **impostazioni di sistema** , quindi selezionare **miglioramento dei dati**.

## <a name="notifications-vs-alerts"></a>Notifiche e avvisi

Oltre a ricevere notifiche sull'attività di rete, è possibile ricevere *avvisi*. Le notifiche forniscono informazioni sulle modifiche alla rete o sulle proprietà dei dispositivi non risolte che non presentano una minaccia. Gli avvisi forniscono informazioni sulle deviazioni della rete e sulle modifiche che potrebbero presentare una minaccia per la rete.

Per visualizzare le notifiche:

1. Selezionare **mappa del dispositivo** dal riquadro del menu a sinistra della console.

2. Selezionare l'icona **notifiche** . Il numero rosso sopra l'icona indica il numero di notifiche.

   :::image type="content" source="media/how-to-enrich-asset-information/notifications-alert-screenshot.png" alt-text="Icona di notifica.":::

   Nella finestra **notifiche** vengono visualizzate tutte le notifiche rilevate dal sensore.

   :::image type="content" source="media/how-to-enrich-asset-information/notification-screen.png" alt-text="Notifiche.":::

## <a name="filter-the-notifications-window"></a>Filtrare la finestra notifiche

Usare i filtri di ricerca per visualizzare le notifiche di interesse.

| Filtra per | Descrizione |
|--|--|
| Filtra per tipo | Visualizzare le notifiche che coprono un'area di interesse specifica. Ad esempio, visualizzare solo le notifiche per i dispositivi inattivi. |
| Filtra per intervallo di date | Visualizza le notifiche che coprono un intervallo di tempo specifico. Ad esempio, visualizzare le notifiche inviate solo nell'ultima settimana. |
| Cerca informazioni specifiche | Cercare notifiche specifiche. |

## <a name="notification-events-and-responses"></a>Risposte ed eventi di notifica

Nella tabella seguente vengono descritti i tipi di evento di notifica che è possibile ricevere, insieme alle opzioni per gestirli. È possibile aggiornare le informazioni sul dispositivo con un valore consigliato o ignorare la notifica. Quando si ignora una notifica, le informazioni sul dispositivo non vengono aggiornate con le informazioni consigliate. Se il traffico viene nuovamente rilevato, la notifica verrà inviata nuovamente.

| Tipi di evento di notifica | Descrizione | Risposte |
|--|--|--|
| Nuovo IP rilevato | Al dispositivo è associato un nuovo indirizzo IP. Potrebbero essere rilevati cinque scenari: <br /><br /> Un indirizzo IP aggiuntivo è stato associato a un dispositivo. Questo dispositivo è associato anche a un indirizzo MAC esistente.<br /><br /> È stato rilevato un nuovo indirizzo IP per un dispositivo che usa un indirizzo MAC esistente. Attualmente il dispositivo non comunica utilizzando un indirizzo IP.<br /> <br /> È stato rilevato un nuovo indirizzo IP per un dispositivo che usa un nome NetBIOS. <br /><br /> È stato rilevato un indirizzo IP come interfaccia di gestione per un dispositivo associato a un indirizzo MAC. <br /><br /> È stato rilevato un nuovo indirizzo IP per un dispositivo che usa un indirizzo IP virtuale. | **Imposta IP aggiuntivo sul dispositivo** (Unisci dispositivi) <br /> <br />**Sostituisci IP esistente** <br /> <br /> **Elimina**<br /> Rimuovere la notifica. |
| Dispositivi inattivi | Il traffico non è stato rilevato in un dispositivo per più di 60 giorni. | **Elimina** <br /> Se il dispositivo non fa parte della rete, rimuoverlo. <br /><br />**Elimina** <br /> Rimuovere la notifica se il dispositivo fa parte della rete. Se il dispositivo è inattivo, ad esempio perché è erroneamente disconnesso dalla rete, chiudere la notifica e riconnettere il dispositivo. |
| Nuovi dispositivi OT | Una subnet include un dispositivo OT non definito in una subnet ICS. <br /><br /> Ogni subnet che contiene almeno un dispositivo OT può essere definita come subnet ICS. Ciò consente di distinguere tra dispositivi OT e IT sulla mappa. | **Imposta come subnet ICS** <br /> <br /> **Elimina** <br />Rimuovere la notifica se il dispositivo non fa parte della subnet. |
| Nessuna subnet configurata | Non sono attualmente configurate subnet nella rete. <br /><br /> Configurare le subnet per una migliore rappresentazione nella mappa e la possibilità di distinguere tra dispositivi OT e IT. | **Aprire la configurazione subnet** e configurare le subnet. <br /><br />**Elimina** <br /> Rimuovere la notifica. |
| Modifiche del sistema operativo | Uno o più nuovi sistemi operativi sono stati associati al dispositivo. | Selezionare il nome del nuovo sistema operativo che si vuole associare al dispositivo.<br /><br /> **Elimina** <br /> Rimuovere la notifica. |
| Nuove subnet | Sono state individuate nuove subnet. | **Learn**<br />Aggiungere automaticamente la subnet.<br />**Apri configurazione subnet**<br />Aggiungere tutte le informazioni sulla subnet mancanti.<br />**Elimina**<br />Rimuovere la notifica. |
| Modifiche al tipo di dispositivo | Al dispositivo è stato associato un nuovo tipo di dispositivo. | **Imposta come {...}**<br />Associare il nuovo tipo al dispositivo.<br />**Elimina**<br />Rimuovere la notifica. |

## <a name="respond-to-many-notifications-simultaneously"></a>Rispondere a molte notifiche simultaneamente

Potrebbe essere necessario gestire più notifiche simultaneamente. Ad esempio:

- Se è stato eseguito un aggiornamento del sistema operativo a un set di server di rete di grandi dimensioni, è possibile indicare al sensore di apprendere le nuove versioni server per tutti i server aggiornati. 

- Se un gruppo di dispositivi in una determinata riga è stato eliminato e non è più attivo, è possibile indicare al sensore di rimuovere questi dispositivi dalla console di.

È possibile indicare al sensore di applicare le informazioni rilevate di recente a più dispositivi o ignorarlo.   

Per visualizzare le notifiche e gestire le notifiche:

1. Usare l'opzione **Filtra per tipo, intervallo di date** o **Seleziona tutto** . Deselezionare le notifiche come richiesto.

2. Indica al sensore di applicare le informazioni rilevate di recente ai dispositivi selezionati selezionando **Learn**. In alternativa, indicare al sensore di ignorare le informazioni rilevate di recente selezionando **Ignora**. Viene visualizzato il numero di notifiche che è possibile apprendere e ignorare contemporaneamente, insieme al numero di notifiche che è necessario gestire singolarmente.

Non è possibile gestire contemporaneamente i **nuovi indirizzi IP** e **Nessuna Subnet** configurata. Richiedono una conferma manuale.

## <a name="improve-device-os-classification-data-enhancement"></a>Migliorare la classificazione del sistema operativo del dispositivo: miglioramento dei dati 

Il sensore individua continuamente i nuovi dispositivi OT. Consente inoltre di individuare le modifiche apportate ai dispositivi individuati in precedenza, inclusi i tipi di sistema operativo.

In determinate circostanze, i conflitti possono essere rilevati nei sistemi operativi individuati. Questo problema può verificarsi perché è presente una versione del sistema operativo che fa riferimento a sistemi desktop o server. Se si verifica questa situazione, si riceverà una notifica con le classificazioni del sistema operativo facoltative.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="Migliorare i dati.":::

Esaminare le raccomandazioni per migliorare la classificazione del sistema operativo. Queste informazioni vengono visualizzate nei report inventario dispositivi, data mining e altro. Consente inoltre di migliorare l'accuratezza di avvisi, minacce e analisi dei rischi.

Quando si accetta una raccomandazione, le informazioni sul tipo di sistema operativo verranno aggiornate nel sensore.

## <a name="see-also"></a>Vedi anche

[Visualizzare gli avvisi](how-to-view-alerts.md)
