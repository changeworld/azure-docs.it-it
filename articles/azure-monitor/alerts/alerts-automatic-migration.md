---
title: Informazioni sul funzionamento del processo di migrazione automatica per gli avvisi classici di monitoraggio di Azure
description: Informazioni sul funzionamento del processo di migrazione automatica.
ms.topic: conceptual
ms.date: 02/14/2021
ms.openlocfilehash: 92aaffcea7a7c96cd77aade318520b093eed3e14
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045463"
---
# <a name="understand-the-automatic-migration-process-for-your-classic-alert-rules"></a>Informazioni sul processo di migrazione automatica per le regole di avviso classiche

Come [annunciato in precedenza](monitoring-classic-retirement.md), gli avvisi classici di monitoraggio di Azure sono ritirati per gli utenti del cloud pubblico, anche se sono ancora in uso limitato fino al **31 maggio 2021**. Gli avvisi classici per il cloud di Azure per enti pubblici e Azure Cina 21Vianet si ritireranno il **29 febbraio 2024**.

[Uno strumento di migrazione](alerts-using-migration-tool.md) è disponibile nel portale di Azure per i clienti per attivare la migrazione autonomamente. Questo articolo illustra il processo di migrazione automatica nel cloud pubblico, che verrà avviato dopo il 31 maggio 2021. Vengono inoltre illustrati i problemi e le soluzioni che possono verificarsi.

## <a name="important-things-to-note"></a>Aspetti importanti da notare

Il processo di migrazione converte le regole di avviso classiche in nuove regole di avviso equivalenti e crea gruppi di azioni. In preparazione, tenere presenti i punti seguenti:

- I formati di payload delle notifiche per le nuove regole di avviso sono diversi dai payload delle regole di avviso classiche perché supportano più funzionalità. Se si dispone di una regola di avviso classica con app per la logica, manuali operativi o webhook, è possibile che smettano di funzionare come previsto dopo la migrazione, a causa delle differenze nel payload. [Informazioni su come prepararsi per la migrazione](alerts-prepare-migration.md).

- Non è possibile eseguire la migrazione di alcune regole di avviso classiche utilizzando lo strumento. [Informazioni sulle regole di cui non è possibile eseguire la migrazione e sulle operazioni da eseguire con esse](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts).

## <a name="what-will-happen-during-the-automatic-migration-process-in-public-cloud"></a>Cosa accade durante il processo di migrazione automatica nel cloud pubblico?

- A partire dal 31 maggio 2021, non sarà possibile creare nuove regole di avviso classiche e la migrazione di avvisi classici verrà attivata in batch.
- Eventuali regole di avviso classiche che monitorano le risorse di destinazione eliminate o su [metriche non più supportate](alerts-understand-migration.md#classic-alert-rules-on-deprecated-metrics) sono considerate non valide.
- Le regole di avviso classiche non valide verranno rimosse a volte dopo il 31 maggio 2021.
- Una volta avviata la migrazione per la sottoscrizione, l'operazione dovrebbe essere completata entro un'ora. I clienti possono monitorare lo stato della migrazione [nello strumento di migrazione in monitoraggio di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Monitoring/MigrationBladeViewModel).
- I proprietari della sottoscrizione riceveranno un messaggio di posta elettronica in caso di esito positivo o negativo della migrazione.

    > [!NOTE]
    > Se non si desidera attendere l'avvio del processo di migrazione automatica, è comunque possibile attivare la migrazione volontariamente utilizzando lo strumento di migrazione.

## <a name="what-if-the-automatic-migration-fails"></a>Cosa accade se la migrazione automatica ha esito negativo?

Quando il processo di migrazione automatica ha esito negativo, i proprietari delle sottoscrizioni riceveranno un messaggio di posta elettronica per notificare il problema. È possibile usare lo strumento di migrazione in monitoraggio di Azure per visualizzare i dettagli completi del problema. Per informazioni sui problemi che potrebbero verificarsi durante la migrazione, vedere la [Guida alla risoluzione dei](alerts-understand-migration.md#common-problems-and-remedies) problemi.

  > [!NOTE]
  > Nel caso in cui sia necessaria un'azione da clienti, ad esempio la disabilitazione temporanea di un blocco di risorsa o la modifica di un'assegnazione di criteri, i clienti dovranno risolvere tali problemi. Se i problemi non vengono risolti da allora, non è possibile garantire una corretta migrazione degli avvisi classici.

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la migrazione](alerts-prepare-migration.md)
- [Informazioni sul funzionamento dello strumento di migrazione](alerts-understand-migration.md)