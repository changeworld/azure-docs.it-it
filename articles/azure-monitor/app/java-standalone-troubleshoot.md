---
title: Risoluzione dei problemi di monitoraggio di Azure Application Insights per Java
description: Informazioni su come risolvere i problemi relativi all'agente Java per monitoraggio di Azure Application Insights
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 286354ecf508dec7b9ba7633bf3b5c7ddc6bfd91
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737058"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>Guida alla risoluzione dei problemi: monitoraggio di Azure Application Insights per Java

In questo articolo vengono illustrati alcuni dei problemi comuni che potrebbero verificarsi durante la strumentazione di un'applicazione Java usando l'agente Java per Application Insights. Vengono inoltre illustrati i passaggi per risolvere questi problemi. Application Insights è una funzionalità del servizio della piattaforma di monitoraggio di Azure.

## <a name="check-the-self-diagnostic-log-file"></a>Controllare il file di log di diagnostica automatica

Per impostazione predefinita, l'agente Java 3,0 per Application Insights produce un file di log denominato `applicationinsights.log` nella stessa directory che include il `applicationinsights-agent-3.0.2.jar` file.

Questo file di log è la prima posizione in cui verificare la presenza di hint per eventuali problemi che potrebbero verificarsi.

## <a name="jvm-fails-to-start"></a>Impossibile avviare JVM

Se JVM non viene avviato con "errore durante l'apertura del file zip o del manifesto JAR mancante", provare a eseguire nuovamente il download del file jar dell'agente perché potrebbe essere stato danneggiato durante il trasferimento di file.

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Aggiornamento da Application Insights Java 2. x SDK

Se si sta già usando il Application Insights Java 2. x SDK nell'applicazione, è possibile continuarne l'uso. L'agente Java 3,0 lo rileverà. Per altre informazioni, vedere [upgrade from the Java 2. x SDK](./java-standalone-upgrade-from-2x.md).

## <a name="upgrade-from-application-insights-java-30-preview"></a>Aggiornamento da Application Insights Java 3,0 Preview

Se si sta eseguendo l'aggiornamento dall'agente di anteprima Java 3,0, rivedere con attenzione tutte le [Opzioni di configurazione](./java-standalone-config.md) . La struttura JSON è stata modificata completamente nella versione di disponibilità generale di 3,0 (GA).

Queste modifiche comprendono:

-  Il nome del file di configurazione è stato modificato da `ApplicationInsights.json` a `applicationinsights.json` .
-  Il `instrumentationSettings` nodo non è più presente. Tutto il contenuto in `instrumentationSettings` viene spostato al livello radice. 
-  I nodi di configurazione come `sampling` , `jmxMetrics` , `instrumentation` e `heartbeat` vengono spostati da `preview` al livello radice.

## <a name="some-logging-is-not-auto-collected"></a>Alcune registrazioni non vengono raccolte automaticamente

La registrazione viene acquisita solo se prima soddisfa la soglia configurata dei framework di registrazione e il secondo soddisfa anche la soglia di Application Insights configurata.

Il modo migliore per determinare se una particolare istruzione di registrazione soddisfa la soglia configurata dei framework di registrazione consiste nel verificare che venga visualizzata nel normale registro applicazioni, ad esempio file o console.

Si noti inoltre che se un'eccezione viene passata al logger, il messaggio di log (e l'eccezione) verrà visualizzato nel portale di Azure sotto la `exceptions` tabella anziché nella `traces` tabella.

Per altri dettagli, vedere la [configurazione della registrazione raccolta automaticamente](./java-standalone-config.md#auto-collected-logging) .

## <a name="import-ssl-certificates"></a>Importa certificati SSL

Questa sezione illustra come risolvere e possibilmente correggere le eccezioni correlate ai certificati SSL quando si usa l'agente Java.

Per risolvere il problema sono disponibili due percorsi diversi:
* Se si usa un archivio chiavi Java predefinito
* Se si usa un archivio chiavi Java personalizzato

Se non si è certi del percorso da seguire, verificare se si dispone di una JVM ARG `-Djavax.net.ssl.trustStore=...` .
Se _non_ si dispone di tale JVM ARG, probabilmente si usa l'archivio chiavi Java predefinito.
Se si _dispone di_ tale JVM ARG, è probabile che si stia usando un archivio chiavi personalizzato e che la JVM ARG indichi l'archivio chiavi personalizzato.

### <a name="if-using-the-default-java-keystore"></a>Se si usa l'archivio chiavi Java predefinito:

In genere, l'archivio chiavi Java predefinito avrà già tutti i certificati radice dell'autorità di certificazione. Tuttavia potrebbero esserci alcune eccezioni, ad esempio il certificato dell'endpoint di inserimento potrebbe essere firmato da un certificato radice diverso. Quindi, per risolvere il problema, è consigliabile attenersi ai tre passaggi seguenti:

1.  Controllare se il certificato radice usato per firmare l'endpoint Application Insights è già presente nell'archivio chiavi predefinito. Per impostazione predefinita, i certificati CA attendibili vengono archiviati in `$JAVA_HOME/jre/lib/security/cacerts` . Per elencare i certificati in un archivio chiavi Java, usare il comando seguente:
    > `keytool -list -v -keystore $PATH_TO_KEYSTORE_FILE`
 
    È possibile reindirizzare l'output a un file temporaneo come questo (sarà facile da cercare in un secondo momento)
    > `keytool -list -v -keystore $JAVA_HOME/jre/lib/security/cacerts > temp.txt`

2. Una volta ottenuto l'elenco dei certificati, attenersi alla [procedura seguente](#steps-to-download-ssl-certificate) per scaricare il certificato radice usato per firmare l'endpoint Application Insights.

    Dopo aver scaricato il certificato, generare un hash SHA-1 sul certificato usando il comando seguente:
    > `keytool -printcert -v -file "your_downloaded_root_certificate.cer"`
 
    Copiare il valore SHA-1 e verificare se questo valore è presente nel file "temp.txt" salvato in precedenza.  Se non è possibile trovare il valore SHA-1 nel file Temp, significa che il certificato radice scaricato non è presente nell'archivio chiavi Java predefinito.


3. Importare il certificato radice nell'archivio chiavi Java predefinito usando il comando seguente:
    >   `keytool -import -file "the cert file" -alias "some meaningful name" -keystore "path to cacerts file"`
 
    In questo caso sarà
 
    > `keytool -import -file "your downloaded root cert file" -alias "some meaningful name" $JAVA_HOME/jre/lib/security/cacerts`


### <a name="if-using-a-custom-java-keystore"></a>Se si usa un archivio chiavi Java personalizzato:

Se si usa un archivio chiavi Java personalizzato, potrebbe essere necessario importare al suo interno i certificati SSL radice Application Insights endpoint (s).
Per risolvere il problema, è consigliabile eseguire le due operazioni seguenti:
1. Seguire questa [procedura](#steps-to-download-ssl-certificate) per scaricare il certificato radice dall'endpoint Application Insights.
2. Usare il comando seguente per importare il certificato SSL radice nell'archivio chiavi Java personalizzato:
    > `keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name.cer" -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-ssl-certificate"></a>Passaggi per scaricare il certificato SSL

1.  Aprire il browser preferito e accedere all' `IngestionEndpoint` URL presente nella stringa di connessione usata per instrumentare l'applicazione.

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png" alt-text="Screenshot che mostra una stringa di connessione Application Insights." lightbox="media/java-ipa/troubleshooting/ingestion-endpoint-snippet.png":::

2.  Selezionare l'icona **Visualizza informazioni sul sito** (blocco) nel browser, quindi selezionare l'opzione **certificato** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="Screenshot dell'opzione certificato nelle informazioni sul sito." lightbox="media/java-ipa/troubleshooting/certificate-icon-capture.png":::

3.  Anziché scaricare il certificato "foglia", è necessario scaricare il certificato "root", come illustrato di seguito. Successivamente, è necessario fare clic su "percorso certificato"-> selezionare il certificato radice-> fare clic su "Visualizza certificato". Verrà visualizzato un nuovo menu certificato ed è possibile scaricare il certificato dal menu nuovo.

    :::image type="content" source="media/java-ipa/troubleshooting/root-certificate-selection.png" alt-text="Screenshot della selezione del certificato radice." lightbox="media/java-ipa/troubleshooting/root-certificate-selection.png":::

4.  Passare alla scheda **Dettagli** e selezionare **copia su file**.
5.  Selezionare il pulsante **Avanti** e selezionare **base-64 codificato X. 509 (. Formato CER)** , quindi selezionare di nuovo **Avanti** .

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="Screenshot dell'esportazione guidata certificati con un formato selezionato." lightbox="media/java-ipa/troubleshooting/certificate-export-wizard.png":::

6.  Specificare il file in cui si desidera salvare il certificato SSL. Quindi selezionare   >  **fine** successivo. Verrà visualizzato il messaggio "esportazione completata".

> [!WARNING]
> È necessario ripetere questi passaggi per ottenere il nuovo certificato prima della scadenza del certificato corrente. È possibile trovare le informazioni sulla scadenza nella scheda **Dettagli** della finestra di dialogo **certificato** .
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="Screenshot che mostra i dettagli del certificato SSL." lightbox="media/java-ipa/troubleshooting/certificate-details.png":::
