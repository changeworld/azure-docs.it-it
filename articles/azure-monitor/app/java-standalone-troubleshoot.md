---
title: Risoluzione dei problemi di monitoraggio di Azure Application Insights per Java
description: Informazioni su come risolvere i problemi relativi all'agente Java per monitoraggio di Azure Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 1ccfd583b58d129268af2a94e3072200e58308cd
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2020
ms.locfileid: "97347831"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Guida alla risoluzione dei problemi: monitoraggio di Azure Application Insights per Java

In questo articolo vengono illustrati alcuni dei problemi comuni che potrebbero verificarsi durante la strumentazione di un'applicazione Java usando l'agente Java per Application Insights. Vengono inoltre illustrati i passaggi per risolvere questi problemi. Application Insights è una funzionalità del servizio della piattaforma di monitoraggio di Azure.

## <a name="check-the-self-diagnostic-log-file"></a>Controllare il file di log di diagnostica automatica

Per impostazione predefinita, l'agente Java 3,0 per Application Insights produce un file di log denominato `applicationinsights.log` nella stessa directory che include il `applicationinsights-agent-3.0.0.jar` file.

Questo file di log è la prima posizione in cui verificare la presenza di hint per eventuali problemi che potrebbero verificarsi.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Aggiornamento da Application Insights Java 2. x SDK

Se si sta già usando il Application Insights Java 2. x SDK nell'applicazione, è possibile continuarne l'uso. L'agente Java 3,0 lo rileverà. Per altre informazioni, vedere [upgrade from the Java 2. x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Aggiornamento da Application Insights Java 3,0 Preview

Se si sta eseguendo l'aggiornamento dall'agente di anteprima Java 3,0, rivedere con attenzione tutte le [Opzioni di configurazione](./java-standalone-config.md) . La struttura JSON è stata modificata completamente nella versione di disponibilità generale di 3,0 (GA).

Queste modifiche comprendono:

-  Il nome del file di configurazione è stato modificato da `ApplicationInsights.json` a `applicationinsights.json` .
-  Il `instrumentationSettings` nodo non è più presente. Tutto il contenuto in `instrumentationSettings` viene spostato al livello radice. 
-  I nodi di configurazione come `sampling` , `jmxMetrics` , `instrumentation` e `heartbeat` vengono spostati da `preview` al livello radice.

## <a name="import-ssl-certificates"></a>Importa certificati SSL

Se si usa l'archivio chiavi Java predefinito, avrà già tutti i certificati radice dell'autorità di certificazione. Non è necessario importare altri certificati SSL.

Se si usa un archivio chiavi Java personalizzato, potrebbe essere necessario importare i certificati SSL dell'endpoint Application Insights al suo interno.

### <a name="key-terminology"></a>Terminologia chiave
Un *Archivio* chiavi è un repository di certificati, chiavi pubbliche e chiavi private. In genere, le distribuzioni Java Development Kit hanno un eseguibile per gestirle: `keytool` .

L'esempio seguente è un semplice comando per importare un certificato SSL nell'archivio chiavi:

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>Passaggi per il download e l'aggiunta di un certificato SSL

1.  Aprire il browser preferito e accedere all' `IngestionEndpoint` URL presente nella stringa di connessione usata per instrumentare l'applicazione.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Screenshot che mostra una stringa di connessione Application Insights.":::

2.  Selezionare l'icona **Visualizza informazioni sul sito** (blocco) nel browser, quindi selezionare l'opzione **certificato** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Screenshot dell'opzione certificato nelle informazioni sul sito.":::

3.  Passare alla scheda **Dettagli** e selezionare **copia su file**.
4.  Selezionare il pulsante **Avanti** e selezionare **base-64 codificato X. 509 (. Formato CER)** , quindi selezionare di nuovo **Avanti** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Screenshot dell'esportazione guidata certificati con un formato selezionato.":::

5.  Specificare il file in cui si desidera salvare il certificato SSL. Quindi selezionare   >  **fine** successivo. Verrà visualizzato il messaggio "esportazione completata".
6.  Dopo aver eseguito il certificato, è possibile importare il certificato in un archivio chiavi Java. Usare il [comando precedente](#key-terminology) per importare i certificati.

> [!WARNING]
> È necessario ripetere questi passaggi per ottenere il nuovo certificato prima della scadenza del certificato corrente. È possibile trovare le informazioni sulla scadenza nella scheda **Dettagli** della finestra di dialogo **certificato** .
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Screenshot che mostra i dettagli del certificato SSL.":::
