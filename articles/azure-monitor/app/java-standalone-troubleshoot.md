---
title: Risoluzione dei problemi-monitoraggio di Azure Application Insights Java
description: Risoluzione dei problemi di monitoraggio di Azure Application Insights Java
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855662"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Risoluzione dei problemi di monitoraggio di Azure Application Insights Java

In questo articolo sono stati illustrati alcuni dei problemi comuni che un utente potrebbe affrontare durante la strumentazione dell'applicazione Java usando l'agente Java insieme ai passaggi per risolvere questi problemi.

## <a name="self-diagnostic-log-file"></a>File di log di diagnostica automatica

Per impostazione predefinita, Application Insights Java 3,0 produrrà un file di log denominato `applicationinsights.log` nella stessa directory in cui `applicationinsights-agent-3.0.0.jar` si trova il file.

Questo file di log è la prima posizione in cui verificare la presenza di hint per eventuali problemi che potrebbero verificarsi.

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Aggiornamento da Application Insights Java 2. x SDK

Vedere [eseguire l'aggiornamento da 2. x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-30-preview"></a>Aggiornamento dalla versione di anteprima di 3,0

Se si esegue l'aggiornamento dall'anteprima 3,0, rivedere con attenzione tutte le [Opzioni di configurazione](./java-standalone-config.md) , perché la struttura JSON è stata modificata completamente nella versione GA di 3,0.

Queste modifiche comprendono:

1.  Il nome del file di configurazione è stato modificato da `ApplicationInsights.json` a `applicationinsights.json` .
2.  Il `instrumentationSettings` nodo non è più presente. Tutto il contenuto in `instrumentationSettings` viene spostato al livello radice. 
3.  I nodi di configurazione come `sampling` , `jmxMetrics` `instrumentation` e `heartbeat` vengono spostati da `preview` al livello radice.

## <a name="ssl-certificate-issues"></a>Problemi relativi ai certificati SSL

Se si usa l'archivio chiavi Java predefinito, saranno già presenti tutti i certificati radice dell'autorità di certificazione e non sarà necessario importare altri certificati SSL.

Se si usa un archivio chiavi Java personalizzato, potrebbe essere necessario importare i certificati SSL dell'endpoint Application Insights al suo interno.

### <a name="some-key-terminology"></a>Alcuni termini chiave:
L' *Archivio* chiavi è un repository di certificati, chiavi pubbliche e private. In genere le distribuzioni JDK hanno un eseguibile per gestirle, `keytool` .

L'esempio seguente è un semplice comando per importare un certificato SSL nell'archivio chiavi:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>Passaggi per scaricare e aggiungere il certificato SSL:

1.  Aprire il browser preferito e passare all' `IngestionEndpoint` URL presente nella stringa di connessione usata per instrumentare l'applicazione, come illustrato di seguito.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Stringa di connessione Application Insights":::

2.  Fare clic sull'icona "Visualizza informazioni sito" (blocco) nel browser e fare clic sull'opzione "certificato" come mostrato di seguito.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Acquisizione certificati SSL":::

3.  Passare alla scheda Dettagli e fare clic su copia su file.
4.  Fare clic sul pulsante Avanti e selezionare "base-64 codificato X. 509 (. CER) "e selezionare Avanti.

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="ExportWizard del certificato SSL":::

5.  Specificare il file in cui si desidera salvare il certificato SSL. Infine, fare clic su Avanti e su fine. Verrà visualizzato il messaggio "esportazione completata".
6.  Una volta ottenuto il certificato, è necessario importare il certificato in un archivio chiavi Java. Usare il [comando](#some-key-terminology) precedente per importare i certificati.

> [!WARNING]
> Sarà necessario ripetere questi passaggi per ottenere il nuovo certificato prima della scadenza del certificato corrente. È possibile trovare le informazioni sulla scadenza nella scheda "dettagli" del popup del certificato come illustrato di seguito.

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Dettagli del certificato SSL":::
