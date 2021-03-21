---
title: Gestire una risorsa Datadog-soluzioni partner di Azure
description: Questo articolo descrive la gestione di una risorsa Datadog nel portale di Azure. Come configurare Single Sign-On, eliminare un'organizzazione Confluent e ottenere supporto.
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/19/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 1a76f79f31d1f4518c069afb7fccbad5bd22d4e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745295"
---
# <a name="manage-the-datadog-resource"></a>Gestire la risorsa Datadog

Questo articolo illustra come gestire le impostazioni per l'integrazione di Azure con Datadog.

## <a name="resource-overview"></a>Panoramica della risorsa

Per visualizzare i dettagli della risorsa Datadog, selezionare **Panoramica** nel riquadro sinistro.

:::image type="content" source="media/manage/resource-overview.png" alt-text="Panoramica delle risorse di Datadog" border="true" lightbox="media/manage/resource-overview.png":::

I dettagli includono:

- Nome del gruppo di risorse
- Località/area geografica
- Subscription
- Tag
- Collegamento Single Sign-on all'organizzazione Datadog
- Offerta/piano Datadog
- Termine fatturazione

Sono inoltre disponibili collegamenti a Dashboard, log e mappe host Datadog.

La schermata panoramica fornisce un riepilogo delle risorse che inviano log e metriche a Datadog.

- Tipo di risorsa: tipo di risorsa di Azure.
- Risorse totali: numero di tutte le risorse per il tipo di risorsa.
- Risorse che inviano log: numero di risorse che inviano log a Datadog tramite l'integrazione.
- Risorse che inviano metriche: numero di risorse che inviano metriche a Datadog tramite l'integrazione.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>Riconfigurare le regole per le metriche e i log

Per modificare le regole di configurazione per le metriche e i log, selezionare **metriche e log** nel riquadro sinistro.

:::image type="content" source="media/manage/reconfigure-metrics-and-logs.png" alt-text="Modificare la configurazione dei log e delle metriche per la risorsa Datadog." border="true":::

Per altre informazioni, vedere [configurare metriche e log](create.md#configure-metrics-and-logs).

## <a name="view-monitored-resources"></a>Visualizzare le risorse monitorate

Per visualizzare l'elenco delle risorse che emettono log in Datadog, selezionare **risorse monitorate** nel riquadro sinistro.

:::image type="content" source="media/manage/view-monitored-resources.png" alt-text="Visualizzare le risorse monitorate da Datadog" border="true":::

È possibile filtrare l'elenco di risorse per tipo di risorsa, nome del gruppo di risorse, posizione e se la risorsa sta inviando log e metriche.

I **log di colonna in Datadog** indicano se la risorsa sta inviando log a Datadog. Se la risorsa non invia log, questo campo indica perché i log non vengono inviati a Datadog. I motivi possibili sono:

- La risorsa non supporta l'invio di log. È possibile configurare solo i tipi di risorse con le categorie del log di monitoraggio per inviare i log a Datadog.
- È stato raggiunto il limite di cinque impostazioni di diagnostica. Ogni risorsa di Azure può avere un massimo di cinque impostazioni di diagnostica. Per altre informazioni, vedere [impostazioni di diagnostica](../../azure-monitor/platform/diagnostic-settings.md).
- Errore. La risorsa è configurata in modo da inviare i log a Datadog, ma è bloccata da un errore.
- Log non configurati. Solo le risorse di Azure con i tag di risorse appropriati sono configurate per l'invio di log a Datadog.
- Area non supportata. La risorsa di Azure si trova in un'area che attualmente non supporta l'invio di log a Datadog.
- Agente Datadog non configurato. Le macchine virtuali in cui non è installato l'agente Datadog non emettono log in Datadog.

## <a name="api-keys"></a>API Keys

Per visualizzare l'elenco delle chiavi API per la risorsa Datadog, selezionare le **chiavi** nel riquadro sinistro. Vengono visualizzate informazioni sulle chiavi.

:::image type="content" source="media/manage/keys.png" alt-text="Chiavi API per l'organizzazione Datadog." border="true":::

Il portale di Azure fornisce una visualizzazione di sola lettura delle chiavi API. Per gestire le chiavi, selezionare il collegamento portale Datadog. Dopo avere apportato le modifiche nel portale di Datadog, aggiornare la visualizzazione portale di Azure.

L'integrazione di Azure Datadog consente di installare l'agente Datadog in una macchina virtuale o in un servizio app. Se non si seleziona una chiave predefinita, l'installazione dell'agente Datadog non riesce.

## <a name="monitor-virtual-machines-using-the-datadog-agent"></a>Monitorare le macchine virtuali usando l'agente Datadog

È possibile installare gli agenti Datadog nelle macchine virtuali come estensione. Passare a **Virtual Machine Agent** sotto le configurazioni dell'organizzazione Datadog nel riquadro sinistro. Questa schermata mostra l'elenco di tutte le macchine virtuali nella sottoscrizione.

Per ogni macchina virtuale vengono visualizzati i dati seguenti:

- Nome risorsa: nome macchina virtuale
- Stato delle risorse: indica se la macchina virtuale è arrestata o in esecuzione. L'agente Datadog può essere installato solo in macchine virtuali in cui è in esecuzione. Se la macchina virtuale viene arrestata, l'installazione dell'agente Datadog verrà disabilitata.
- Versione agente: numero di versione dell'agente Datadog.
- Stato dell'agente: indica se l'agente Datadog è in esecuzione nella macchina virtuale.
- Integrazioni abilitate: metriche chiave raccolte dall'agente Datadog.
- Metodo di installazione: lo strumento specifico usato per installare l'agente Datadog. Ad esempio chef o script.
- Invio dei log: indica se l'agente Datadog Invia log a Datadog.

Selezionare la macchina virtuale in cui installare l'agente Datadog. Selezionare **Installa agente**.

Il portale richiede di confermare che si vuole installare l'agente con la chiave predefinita. Selezionare **OK** per avviare l'installazione. Azure Mostra lo stato di **installazione** fino a quando l'agente non viene installato e sottoposta a provisioning.

Dopo l'installazione dell'agente Datadog, lo stato cambia in installato.

Per verificare che l'agente Datadog sia stato installato, selezionare la macchina virtuale e passare alla finestra estensioni.

È possibile disinstallare gli agenti Datadog in una macchina virtuale passando a **Virtual Machine Agent**. Selezionare la macchina virtuale e **disinstallare l'agente**.

## <a name="monitor-app-services-using-the-datadog-agent-as-an-extension"></a>Monitorare i servizi app usando l'agente Datadog come estensione

È possibile installare gli agenti Datadog nei servizi app come estensione. Passare all' **estensione del servizio app** nel riquadro sinistro. Questa schermata mostra l'elenco di tutti i servizi app nella sottoscrizione.

Per ogni servizio app vengono visualizzati gli elementi dati seguenti:

- Nome risorsa: nome macchina virtuale.
- Stato delle risorse: indica se il servizio app è stato interrotto o in esecuzione. L'agente Datadog può essere installato solo nei servizi app che eseguono. Se il servizio app viene arrestato, l'installazione dell'agente Datadog è disabilitata.
- Piano di servizio app: il piano specifico configurato per il servizio app.
- Versione agente: numero di versione dell'agente Datadog.

Per installare l'agente Datadog, selezionare il servizio app e **installare l'estensione**. La versione più recente dell'agente Datadog è installata nel servizio app come estensione.

Il portale conferma che si vuole installare l'agente Datadog. Inoltre, le impostazioni dell'applicazione per il servizio app specifico vengono aggiornate con la chiave predefinita. Il servizio app viene riavviato al termine dell'installazione dell'agente Datadog.

Selezionare **OK** per avviare il processo di installazione per l'agente Datadog. Il portale Mostra lo stato di **installazione** fino a quando l'agente non è installato. Dopo l'installazione dell'agente Datadog, lo stato cambia in installato.

Per disinstallare gli agenti Datadog nel servizio app, passare all' **estensione del servizio app**. Selezionare il servizio app e l' **estensione di disinstallazione**

## <a name="reconfigure-single-sign-on"></a>Riconfigurare Single Sign-On

Se si desidera riconfigurare Single Sign-On, selezionare **Single Sign-on** nel riquadro sinistro.

Per stabilire Single Sign-On tramite Azure Active Directory, selezionare **abilita Single Sign-on tramite Azure Active Directory**.

Il portale recupera l'applicazione Datadog appropriata da Azure Active Directory. L'app deriva dal nome dell'app aziendale selezionato quando si configura l'integrazione. Selezionare il nome dell'app Datadog come mostrato di seguito:
 
:::image type="content" source="media/manage/reconfigure-single-sign-on.png" alt-text="Riconfigurare Single Sign-On applicazione." border="true":::
 
## <a name="disable-or-enable-integration"></a>Disabilitare o abilitare l'integrazione

È possibile interrompere l'invio di log e metriche da Azure a Datadog. Continueranno a essere addebitati altri servizi Datadog che non sono correlati alle metriche e ai log di monitoraggio.

Per disabilitare l'integrazione di Azure con Datadog, vedere **Panoramica**. Selezionare **Disabilita** e **OK**.
 
:::image type="content" source="media/manage/disable.png" alt-text="Disabilitare la risorsa Datadog." border="true":::

Per abilitare l'integrazione di Azure con Datadog, vedere **Panoramica**. Selezionare **Abilita** e **OK**. Selezionando **Enable** viene recuperata qualsiasi configurazione precedente per le metriche e i log. La configurazione determina quali risorse di Azure emettono metriche e log in Datadog. Dopo aver completato il passaggio, le metriche e i log vengono inviati a Datadog.
 
:::image type="content" source="media/manage/enable.png" alt-text="Abilitare la risorsa Datadog." border="true":::

## <a name="delete-datadog-resource"></a>Elimina risorsa Datadog

Passare a **Panoramica** nel riquadro sinistro e selezionare **Elimina**. Confermare che si vuole eliminare la risorsa Datadog. Selezionare **Elimina**.

:::image type="content" source="media/manage/delete.png" alt-text="Elimina risorsa Datadog" border="true":::

Se viene eseguito il mapping di una sola risorsa Datadog a un'organizzazione Datadog, i log e le metriche non vengono più inviati a Datadog. Tutti gli arresti per la fatturazione per Datadog tramite Azure Marketplace.

Se viene eseguito il mapping di più di una risorsa Datadog all'organizzazione Datadog, l'eliminazione della risorsa Datadog interrompe solo l'invio di log e metriche per la risorsa Datadog. Poiché l'organizzazione Datadog è collegata ad altre risorse di Azure, la fatturazione continua tramite Azure Marketplace.

## <a name="getting-support"></a>Richiesta di assistenza

Per contattare il supporto tecnico sull'integrazione Datadog di Azure, selezionare **nuovo richiesta di supporto** nel riquadro sinistro. Selezionare il collegamento al portale di Datadog.

:::image type="content" source="media/manage/support-request.png" alt-text="Crea una nuova richiesta di supporto Datadog" border="true":::

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sulla risoluzione dei problemi, vedere [risoluzione dei problemi relativi alle soluzioni Datadog](troubleshoot.md).
