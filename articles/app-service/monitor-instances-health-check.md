---
title: Monitorare l'integrità delle istanze del servizio app
description: Informazioni su come monitorare l'integrità delle istanze del servizio app usando il controllo integrità.
keywords: servizio app di Azure, app Web, controllo integrità, traffico instradato, istanze integre, percorso, monitoraggio,
author: msangapu-msft
ms.topic: article
ms.date: 12/03/2020
ms.author: msangapu
ms.openlocfilehash: 60a210c6c336c1b820015304e8ab53bc894c17bf
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98792492"
---
# <a name="monitor-app-service-instances-using-health-check"></a>Monitorare le istanze del servizio app usando il controllo integrità

![Errore controllo integrità][2]

Questo articolo usa il controllo di integrità nel portale di Azure per monitorare le istanze del servizio app. Il controllo dell'integrità aumenta la disponibilità dell'applicazione rimuovendo le istanze non integre. Il [piano di servizio app](/overview-hosting-plans) deve essere ridimensionato a due o più istanze per usare il controllo integrità. Il percorso di controllo integrità deve controllare i componenti critici dell'applicazione. Se, ad esempio, l'applicazione dipende da un database e da un sistema di messaggistica, l'endpoint di controllo integrità deve connettersi a tali componenti. Se l'applicazione non è in grado di connettersi a un componente critico, il percorso deve restituire un codice di risposta a 500 per indicare che l'app non è integra.

## <a name="what-app-service-does-with-health-checks"></a>Funzionamento del servizio app con i controlli di integrità

- Quando si specifica un percorso nell'app, il controllo integrità esegue il ping del percorso in tutte le istanze dell'app del servizio app a intervalli di 1 minuto.
- Se un'istanza non risponde con un codice di stato compreso tra 200-299 (inclusi) dopo due o più richieste oppure non riesce a rispondere al ping, il sistema determina che non è integro e lo rimuove.
- Dopo la rimozione, il controllo integrità continua a eseguire il ping dell'istanza di non integro. Se continua a rispondere senza esito positivo, il servizio app riavvia la VM sottostante per ripristinare lo stato integro dell'istanza.
- Se un'istanza rimane non integra per un'ora, verrà sostituita con una nuova istanza.
- Inoltre, durante il ridimensionamento orizzontale, il servizio app esegue il ping del percorso di controllo integrità per assicurarsi che le nuove istanze siano pronte.

> [!NOTE]
> Il controllo integrità non segue 302 reindirizzamenti. Al massimo un'istanza verrà sostituita all'ora, con un massimo di tre istanze al giorno per ogni piano di servizio app.
>

## <a name="enable-health-check"></a>Abilita controllo integrità

![Navigazione del controllo integrità nel portale di Azure][3]

- Per abilitare il controllo dell'integrità, passare alla portale di Azure e selezionare l'app del servizio app.
- In **monitoraggio** selezionare **controllo integrità**.
- Selezionare **Abilita** e specificare un percorso URL valido nell'applicazione, ad esempio `/health` o `/api/health` .
- Fare clic su **Salva**.

> [!CAUTION]
> Le modifiche alla configurazione del controllo integrità riavviano l'app. Per ridurre al minimo l'effetto sulle app di produzione, è consigliabile [configurare gli slot di staging](deploy-staging-slots.md) e scambiarli nell'ambiente di produzione.
>

### <a name="configuration"></a>Configurazione

Oltre a configurare le opzioni di controllo di integrità, è anche possibile configurare le [impostazioni dell'app](configure-common.md)seguenti:

| Nome impostazione app | Valori consentiti | Descrizione |
|-|-|-|
|`WEBSITE_HEALTHCHECK_MAXPINGFAILURES` | 2 - 10 | Numero massimo di errori del ping. Se, ad esempio, è impostato su `2` , le istanze verranno rimosse dopo i `2` ping non riusciti. Inoltre, quando si esegue la scalabilità verticale o orizzontale, il servizio app esegue il ping del percorso di controllo integrità per assicurarsi che le nuove istanze siano pronte. |
|`WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` | 0 - 100 | Per evitare che vengano sovraccaricate le istanze di integro, non verranno escluse più della metà delle istanze. Se, ad esempio, un piano di servizio app viene ridimensionato a quattro istanze e tre non sono integre, al massimo due verranno escluse. Le altre due istanze (uno integro e uno non integro) continueranno a ricevere le richieste. Nello scenario peggiore in cui tutte le istanze non sono integre, nessuna verrà esclusa. Per eseguire l'override di questo comportamento, impostare l'impostazione dell'app su un valore compreso tra `0` e `100` . Un valore più alto indica che verranno rimosse più istanze non integre (il valore predefinito è 50). |

#### <a name="authentication-and-security"></a>Autenticazione e sicurezza

Il controllo dell'integrità si integra con le funzionalità di autenticazione e autorizzazione del servizio app. Se queste funzionalità di sicurezza sono abilitate, non sono necessarie impostazioni aggiuntive. Tuttavia, se si usa il proprio sistema di autenticazione, il percorso di controllo integrità deve consentire l'accesso anonimo. Se il sito è abilitato solo per HTTP **s**, la richiesta di controllo integrità verrà inviata tramite http **s**.

I team di sviluppo aziendale di grandi dimensioni spesso devono rispettare i requisiti di sicurezza per le API esposte. Per proteggere l'endpoint di controllo di integrità, è necessario innanzitutto usare funzionalità come [restrizioni IP](app-service-ip-restrictions.md#set-an-ip-address-based-rule), [certificati client](app-service-ip-restrictions.md#set-an-ip-address-based-rule)o una rete virtuale per limitare l'accesso alle applicazioni. È possibile proteggere l'endpoint di controllo dell'integrità richiedendo la `User-Agent` corrispondenza tra le corrispondenze della richiesta in ingresso `ReadyForRequest/1.0` . Non è possibile effettuare lo spoofing del User-Agent perché la richiesta è già stata protetta dalle funzionalità di sicurezza precedenti.

## <a name="monitoring"></a>Monitoraggio

Dopo aver fornito il percorso di controllo integrità dell'applicazione, è possibile monitorare l'integrità del sito usando monitoraggio di Azure. Nel pannello **controllo integrità** nel portale fare clic sulle **metriche** nella barra degli strumenti superiore. Verrà aperto un nuovo pannello in cui è possibile visualizzare lo stato di integrità cronologico del sito e creare una nuova regola di avviso. Per ulteriori informazioni sul monitoraggio dei siti, [vedere la Guida di monitoraggio di Azure](web-sites-monitor.md).

## <a name="next-steps"></a>Passaggi successivi
- [Creare un avviso di log attività per monitorare tutte le operazioni del motore di scalabilità automatica della sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Creare un avviso di log attività per monitorare tutte le operazioni di scalabilità automatica in riduzione e in aumento non riuscite per la sottoscrizione](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

[1]: ./media/app-service-monitor-instances-health-check/health-check-success-diagram.png
[2]: ./media/app-service-monitor-instances-health-check/health-check-failure-diagram.png
[3]: ./media/app-service-monitor-instances-health-check/azure-portal-navigation-health-check.png
