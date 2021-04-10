---
title: Connettersi ai sistemi SAP
description: Accedere e gestire le risorse SAP automatizzando i flussi di lavoro con le app per la logica di Azure
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 03/24/2021
tags: connectors
ms.openlocfilehash: b2f7ae79cf43e257c3e13d2da7fec5c83dd3fab0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937278"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Connettersi a sistemi SAP con App per la logica di Azure

Questo articolo illustra come è possibile accedere alle risorse SAP dalle app per la logica usando il [connettore SAP](/connectors/sap/).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone ancora di una sottoscrizione di Azure, [registrarsi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* App per la logica da cui si vuole accedere alle risorse SAP. Se non si ha familiarità con le app per la logica, vedere [Panoramica del servizio app](../logic-apps/logic-apps-overview.md) per la logica e [Guida introduttiva per la creazione della prima app per la logica nella portale di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md).

    * Se è stata usata una versione precedente del connettore SAP che è stata deprecata, è necessario [eseguire la migrazione al connettore corrente per](#migrate-to-current-connector) potersi connettere al server SAP.

    * Se si esegue l'app per la logica in Azure multi-tenant, vedere i [prerequisiti multi-tenant](#multi-tenant-azure-prerequisites).

    * Se l'app per la logica è in esecuzione in un ambiente del servizio di integrazione a livello Premium [(ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), vedere [prerequisiti di ISE](#ise-prerequisites).

* Un [server applicazioni SAP](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) o un [Server dei messaggi SAP](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) a cui si vuole accedere dalle app per la logica. Per informazioni sui server SAP e sulle azioni SAP che è possibile usare con il connettore, vedere [compatibilità SAP](#sap-compatibility).

    * Per consentire l'uso di RFC, è necessario configurare il server SAP. Per ulteriori informazioni, vedere la nota SAP seguente: [460089-profili di autorizzazione minimi per i programmi RFC esterni](https://launchpad.support.sap.com/#/notes/460089). 

* Contenuto del messaggio da inviare al server SAP, ad esempio un file IDoc di esempio. Il contenuto deve essere in formato XML e includere lo spazio dei nomi dell'azione SAP che si vuole usare. È possibile [inviare IDOCs con uno schema di file flat eseguendone il wrapping in una busta XML](#send-flat-file-idocs).

* Se si vuole usare l'oggetto **quando viene ricevuto un messaggio dal trigger SAP** , è necessario eseguire anche le operazioni seguenti:

    * Configurare le autorizzazioni di sicurezza del gateway SAP con questa impostazione: `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

    * Configurare la registrazione di sicurezza del gateway SAP per individuare l'elenco di controllo di accesso (ACL). Per ulteriori informazioni, vedere l' [argomento della Guida di SAP per la configurazione della registrazione del gateway](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm). In caso contrario, verrà visualizzato questo errore: `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    > [!NOTE]
    > Questo trigger usa lo stesso percorso URI per rinnovare e annullare la sottoscrizione da una sottoscrizione di webhook. L'operazione di rinnovo utilizza il `PATCH` Metodo http, mentre l'operazione di annullamento della sottoscrizione utilizza il `DELETE` Metodo http. Questo comportamento potrebbe far apparire un'operazione di rinnovo come operazione di annullamento della sottoscrizione nella cronologia del trigger, ma l'operazione è ancora un rinnovo perché il trigger usa `PATCH` come metodo HTTP, non `DELETE` .

### <a name="sap-compatibility"></a>Compatibilità con SAP

Il connettore SAP è compatibile con i tipi di sistemi SAP seguenti:

* Sistemi SAP basati su HANA locali e basati sul cloud, ad esempio S/4 HANA.

* Sistemi SAP locali classici, ad esempio R/3 e ECC.

Il connettore SAP supporta i seguenti tipi di messaggi e di integrazione dei dati da sistemi basati su SAP NetWeaver:

* Documento intermedio (IDoc)

* Interfaccia di programmazione dell'applicazione business (Bova)

* RFC (Remote Function Call) e RFC transazionale (tRFC)

Il connettore SAP usa la [libreria di SAP .NET Connector (NCO)](https://support.sap.com/en/product/connectors/msnet.html). Con il connettore è possibile usare le azioni e i trigger SAP seguenti:

* **Inviare un messaggio a SAP** per [inviare IDOCs su tRFC](#send-idoc-action) azione, che è possibile usare per:

    * [Chiamare le funzioni di Bruni su RFC](#call-bapi-action)

    * Chiamare RFC/tRFC nei sistemi SAP

    * Creare o chiudere sessioni con stato

    * Eseguire il commit o il rollback delle transazioni di Bova

    * Confermare un identificatore di transazione

    * Inviare IDocs, ottenere lo stato di un IDoc dal numero e ottenere un elenco di IDocs per una transazione

    * Leggi una tabella SAP

* **Quando viene ricevuto un messaggio dal trigger SAP** , che può essere usato per:

    * Ricevere IDocs su tRFC

    * Chiamare le funzioni di Bruni su tRFC

    * Chiamare RFC/tRFC nei sistemi SAP

* Azione **genera schemi** , che è possibile usare per generare schemi per gli artefatti SAP per IDOC, Bruno o RFC.

Per usare queste azioni SAP, è necessario innanzitutto autenticare la connessione con un nome utente e una password. Il connettore SAP supporta inoltre le [comunicazioni di rete sicure (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true). È possibile utilizzare SNC per SAP NetWeaver Single Sign-On (SSO) o per ulteriori funzionalità di sicurezza da prodotti esterni. Se si usa SNC, vedere i [prerequisiti di SNC](#snc-prerequisites).

### <a name="migrate-to-current-connector"></a>Esegui la migrazione al connettore corrente

Il server applicazioni SAP e i connettori del server di messaggistica SAP precedenti sono stati deprecati il 29 febbraio 2020. Per eseguire la migrazione al connettore SAP corrente, seguire questa procedura:

1. Aggiornare il [gateway dati locale](https://www.microsoft.com/download/details.aspx?id=53127) alla versione corrente. Per altre informazioni, vedere [installare un gateway dati locale per app per la logica di Azure](../logic-apps/logic-apps-gateway-install.md).

1. Nell'app per la logica che usa il connettore SAP deprecato, eliminare l'azione **Invia a SAP** .

1. Aggiungere l'azione **Invia messaggio a SAP** dal connettore SAP corrente.

1. Riconnettersi al sistema SAP nella nuova azione.

1. Salvare l'app per la logica.

### <a name="multi-tenant-azure-prerequisites"></a>Prerequisiti di Azure multi-tenant

Questi prerequisiti si applicano se l'app per la logica è in esecuzione in Azure multi-tenant. Il connettore SAP gestito non viene eseguito in modo nativo in [ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

> [!TIP]
> Se si usa ISE a livello Premium, è possibile usare il connettore SAP ISE invece del connettore SAP gestito. Per ulteriori informazioni, vedere [prerequisiti di ISE](#ise-prerequisites).

Il connettore SAP gestito si integra con i sistemi SAP tramite il [gateway dati locale](../logic-apps/logic-apps-gateway-connection.md). Negli scenari di invio dei messaggi, ad esempio, quando un messaggio viene inviato da un'app per la logica a un sistema SAP, il gateway dati funge da client RFC e inoltra le richieste ricevute dall'app per la logica a SAP. Analogamente, in scenari di ricezione dei messaggi, il gateway dati funge da Server RFC che riceve le richieste da SAP e le inoltra all'app per la logica.

* [Scaricare e installare il gateway dati locale](../logic-apps/logic-apps-gateway-install.md) in un computer host o una macchina virtuale che si trova nella stessa rete virtuale del sistema SAP a cui ci si connette.

* [Creare una risorsa del gateway di Azure](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) per il gateway dati locale nella portale di Azure. Questo gateway consente di accedere in modo sicuro ai dati e alle risorse locali. Assicurarsi di usare una versione supportata del gateway.

    * Se si verifica un problema con il gateway, provare a eseguire [l'aggiornamento alla versione più recente](https://aka.ms/on-premises-data-gateway-installer), che può includere aggiornamenti per risolvere il problema.

* [Scaricare e installare la libreria client SAP più recente](#sap-client-library-prerequisites) nello stesso computer locale del gateway dati locale.

### <a name="ise-prerequisites"></a>Prerequisiti di ISE

Questi prerequisiti si applicano se l'app per la logica viene eseguita in un ISE di livello Premium. Tuttavia, non si applicano alle app per la logica eseguite in un ISE a livello di sviluppatore. Un ISE fornisce l'accesso alle risorse protette da una rete virtuale di Azure e offre altri connettori nativi ISE che consentono alle app per la logica di accedere direttamente alle risorse locali senza usare il gateway dati locale.

1. Se non si ha già un account di archiviazione di Azure con un contenitore BLOB, creare un contenitore usando il [portale di Azure](../storage/blobs/storage-quickstart-blobs-portal.md) o [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

1. [Scaricare e installare la libreria client SAP più recente](#sap-client-library-prerequisites) nel computer locale. Sono presenti i file di assembly seguenti:

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. Creare un file con estensione zip che includa questi file di assembly. Caricare il pacchetto nel contenitore BLOB in archiviazione di Azure.

1. In portale di Azure o Azure Storage Explorer passare al percorso del contenitore in cui è stato caricato il file zip.

1. Copiare l'URL del percorso del contenitore. Assicurarsi di includere il token di firma di accesso condiviso (SAS), in modo che il token SAS sia autorizzato. In caso contrario, la distribuzione per il connettore SAP ISE non riesce.

1. Installare e distribuire il connettore SAP in ISE. Per altre informazioni, vedere [aggiungere connettori ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

   1. Nella [portale di Azure](https://portal.azure.com)trovare e aprire ISE.

   1. Nel menu ISE selezionare **connettori gestiti** &gt; **Aggiungi**. Dall'elenco Connectors (connettori) individuare e selezionare **SAP**.

   1. Nel riquadro **Aggiungi un nuovo connettore gestito** , nella casella **pacchetto SAP** , incollare l'URL del file con estensione zip che contiene gli assembly SAP. Anche in questo caso, assicurarsi di includere il token SAS.
 
  1. Selezionare **Crea** per terminare la creazione del connettore ISE.

1. Se l'istanza di SAP e ISE si trovano in reti virtuali diverse, è necessario anche eseguire il [peering di tali reti](../virtual-network/tutorial-connect-virtual-networks-portal.md) in modo che siano connesse.

### <a name="sap-client-library-prerequisites"></a>Prerequisiti della libreria client SAP

Questi sono i prerequisiti per la libreria client SAP che si sta usando con il connettore.

* Assicurarsi di installare la versione più recente, il [connettore SAP (NCo 3,0) per Microsoft .NET 3.0.22.0 compilato con .NET Framework 4,0-Windows 64-bit (x64)](https://support.sap.com/en/product/connectors/msnet.html). Le versioni precedenti di SAP NCo potrebbero riscontrare problemi quando più di un messaggio di IDoc viene inviato nello stesso momento. Questa condizione blocca tutti i messaggi successivi inviati alla destinazione SAP, causando il timeout dei messaggi.

* È necessario che sia installata la versione a 64 bit della libreria client SAP, perché il gateway dati viene eseguito solo su sistemi a 64 bit. L'installazione della versione a 32 bit non supportata genera un errore di "immagine non valida".

* Copiare i file di assembly dalla cartella di installazione predefinita in un altro percorso, in base allo scenario indicato di seguito.

    * Per le app per la logica eseguite in ISE, seguire invece i [prerequisiti di ISE](#ise-prerequisites) .

    * Per le app per la logica eseguite in Azure multi-tenant e con il gateway dati locale, copiare i file di assembly nella cartella di installazione del gateway dati. 

        
        * Se la connessione SAP ha esito negativo con il messaggio di errore, **verificare le informazioni sull'account e/o le autorizzazioni e riprovare**, assicurarsi di aver copiato i file di assembly nella cartella di installazione del gateway dati.
        
        * Risolvere altri problemi utilizzando il [Visualizzatore log associazione assembly .NET](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer). Questo strumento consente di verificare che i file di assembly si trovino nella posizione corretta. 
        
        * Facoltativamente, selezionare l'opzione di **registrazione global assembly cache** quando si installa la libreria client SAP.

Si notino le relazioni seguenti tra la libreria client SAP, il .NET Framework, il Runtime .NET e il gateway:

* Sia l'adapter Microsoft SAP sia il servizio host del gateway usano .NET Framework 4.7.2.

* SAP NCo per .NET Framework 4,0 funziona con i processi che usano il Runtime .NET da 4,0 a 4,8.

* SAP NCo per .NET Framework 2,0 funziona con processi che usano il Runtime .NET 2,0 a 3,5, ma non funzionano più con il gateway più recente.

### <a name="snc-prerequisites"></a>Prerequisiti di SNC

Se si usa un gateway dati locale con SNC opzionale, che è supportato solo in Azure multi-tenant, è necessario configurare queste impostazioni aggiuntive.

Se si usa SNC con SSO, assicurarsi che il servizio gateway dati sia in esecuzione come un utente mappato all'utente SAP. Per modificare l'account predefinito, selezionare **Cambia account** e immettere le credenziali utente.

![Screenshot delle impostazioni del gateway dati locale nel portale di Azure che mostra la pagina delle impostazioni del servizio con il pulsante per modificare l'account del servizio gateway selezionato.](./media/logic-apps-using-sap-connector/gateway-account.png)

Se si Abilita SNC tramite un prodotto di sicurezza esterno, copiare i file o la libreria SNC nello stesso computer in cui è installato il gateway dati. Alcuni esempi di prodotti SNC includono [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM. Per altre informazioni sull'abilitazione di SNC per il gateway dati, vedere [abilitare le comunicazioni di rete sicure](#enable-secure-network-communications).

## <a name="send-idoc-messages-to-sap-server"></a>Inviare messaggi IDoc al server SAP

Seguire questi esempi per creare un'app per la logica che invia un messaggio IDoc a un server SAP e restituisce una risposta:

1. [Creare un'app per la logica attivata da una richiesta HTTP.](#create-http-request-trigger)

1. [Creare un'azione nel flusso di lavoro per inviare un messaggio a SAP.](#create-sap-action-to-send-message)

1. [Creare un'azione di risposta HTTP nel flusso di lavoro.](#create-http-response-action)

1. [Creare un modello di richiesta-risposta per la chiamata di funzione remota (RFC), se si usa una RFC per ricevere risposte da SAP ABAP.](#create-rfc-request-response)

1. [Testare l'app per la logica.](#test-logic-app)

### <a name="create-http-request-trigger"></a>Crea trigger di richiesta HTTP

> [!NOTE]
> Quando un'app per la logica riceve IDocs da SAP, il [trigger di richiesta](../connectors/connectors-native-reqres.md) ora supporta il formato XML semplice di SAP. Per ricevere IDocs come XML semplice, usare il trigger **quando un messaggio viene ricevuto da SAP**. Impostare il parametro **IDOC Format** su **SapPlainXml**.

Per prima cosa, creare un'app per la logica con un endpoint in Azure per inviare richieste *http post* all'app per la logica. Quando l'app per la logica riceve queste richieste HTTP, il [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) viene attivato ed esegue il passaggio successivo nel flusso di lavoro.

1. Nella [portale di Azure](https://portal.azure.com)creare un'app per la logica vuota, che apre la **finestra di progettazione delle app** per la logica.

1. Nella casella di ricerca immettere `http request` come filtro. Dall'elenco **trigger** selezionare **quando viene ricevuta una richiesta http**.

   ![Screenshot della finestra di progettazione di app per la logica, che mostra un nuovo trigger di richiesta HTTP aggiunto all'app per la logica.](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Salvare l'app per la logica in modo che sia possibile generare un URL dell'endpoint per l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**. L'URL dell'endpoint viene ora visualizzato nel trigger. 

   ![Screenshot della finestra di progettazione di app per la logica, che mostra il trigger di richiesta HTTP con l'URL POST generato copiato.](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>Crea azione SAP per inviare un messaggio

Creare quindi un'azione per inviare il messaggio IDoc a SAP quando viene attivato il [trigger della richiesta http](#create-http-request-trigger) .

1. Nella finestra di progettazione di app per la logica, sotto il trigger, selezionare **nuovo passaggio**.

   ![Screenshot della finestra di progettazione di app per la logica, che mostra l'app per la logica modificata per aggiungere un nuovo passaggio.](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Nella casella di ricerca immettere `sap` come filtro. Dall'elenco delle **azioni** selezionare **Invia messaggio a SAP**.
  
   ![Screenshot della finestra di progettazione di app per la logica, che mostra la selezione dell'azione "Invia messaggio a SAP".](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   In alternativa, è possibile selezionare la scheda **Enterprise** e selezionare l'azione SAP.

   ![Screenshot della finestra di progettazione di app per la logica, che mostra la selezione dell'azione "Invia messaggio a SAP" nella scheda Enterprise.](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Se la connessione esiste già, continuare con il passaggio successivo. Se viene richiesto di creare una nuova connessione, fornire le informazioni seguenti per connettersi al server SAP locale.

   1. Specificare un nome per la connessione.

   1. Se si usa il gateway dati, seguire questa procedura:
   
      1. Nella sezione **gateway dati** , in **sottoscrizione**, selezionare innanzitutto la sottoscrizione di Azure per la risorsa del gateway dati creata nella portale di Azure per l'installazione del gateway dati.
   
      1. In **gateway connessione** selezionare la risorsa del gateway dati in Azure.

   1. Continuare a fornire le informazioni di connessione. Per la proprietà **tipo di accesso** , eseguire il passaggio a seconda che la proprietà sia impostata su **server applicazioni** o **gruppo**:
   
      * Per il **server applicazioni**, queste proprietà, che in genere appaiono facoltative, sono obbligatorie:

        ![Creare una connessione al server applicazioni SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Per il **gruppo**, queste proprietà, che in genere appaiono facoltative, sono obbligatorie:

        ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Per impostazione predefinita, la tipizzazione forte viene utilizzata per verificare la presenza di valori non validi eseguendo la convalida XML rispetto allo schema. Questo comportamento può essere utile per rilevare i problemi precedenti. L'opzione di **tipizzazione sicura** è disponibile per la compatibilità con le versioni precedenti e controlla solo la lunghezza della stringa. Altre informazioni sull' [opzione di tipizzazione sicura](#safe-typing).

   1. Al termine, selezionare **Crea**.

      App per la logica configura e testa la connessione per assicurarsi che la connessione funzioni correttamente.

    > [!NOTE]

    > Se viene visualizzato l'errore seguente, si verifica un problema con l'installazione della libreria client di SAP NCo: 
    >
    > **Test della connessione non riuscito. Errore ' Impossibile elaborare la richiesta. Dettagli errore:' Impossibile caricare il file o l'assembly ' sapnco, Version = 3.0.0.42, Culture = neutral, PublicKeyToken 50436dca5c7f7d23' o una delle relative dipendenze. Il sistema non è in grado di trovare il file specificato .' .'**
    >
    > Assicurarsi di [installare la versione richiesta della libreria client di SAP NCO e soddisfare tutti gli altri prerequisiti](#sap-client-library-prerequisites).

1. Ora individuare e selezionare un'azione dal server SAP.

   1. Nella casella **azione SAP** selezionare l'icona della cartella. Dall'elenco file, individuare e selezionare il messaggio SAP che si desidera usare. Per spostarsi nell'elenco, usare le frecce.

      Questo esempio Mostra come selezionare un IDoc con il tipo **Orders** .

      ![Trovare e selezionare l'azione IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Se non è possibile trovare l'azione desiderata, è possibile immettere manualmente un percorso, ad esempio:

      ![Specificare manualmente il percorso all'azione IDoc](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Specificare il valore per l' **azione SAP** tramite l'editor di espressioni. In questo modo, è possibile utilizzare la stessa azione per diversi tipi di messaggio.

      Per altre informazioni sulle operazioni IDoc, vedere [schemi di messaggio per le operazioni di IDOC](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Fare clic all'interno della casella **Messaggio di input** in modo che venga visualizzato l'elenco di contenuti dinamici. In tale elenco, sotto **Alla ricezione di una richiesta HTTP** selezionare il campo **Corpo**.

      Questo passaggio include il contenuto del corpo del trigger di richiesta HTTP e invia l'output al server SAP.

      ![Selezionare la proprietà "Body" dal trigger](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Al termine, l'azione SAP ha un aspetto simile all'esempio seguente:

      ![Completare l'azione SAP](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

#### <a name="send-flat-file-idocs"></a>Invia IDocs file flat

È possibile utilizzare IDocs con uno schema di file flat se si esegue il wrapping in una busta XML. Per inviare un file flat IDoc, usare le istruzioni generiche per [creare un'azione SAP per inviare il messaggio IDOC](#create-sap-action-to-send-message) con le modifiche seguenti.

1. Per l'azione **Invia messaggio a SAP** , usare l'URI azione SAP `http://microsoft.lobservices.sap/2007/03/Idoc/SendIdoc` .

1. Formattare il messaggio di input con una busta XML. Per un esempio, vedere il messaggio di esempio seguente:

```xml
<?xml version="1.0" encoding="utf-8"?>
<SendIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
  <idocData>EDI_DC    300                      ORDERS052SAPMSS    LIMSFTABCSWI                                                                                           ED  93AORDERSOLP     VLTRFC    KUMSFTABCSWI                                                                                           13561                       231054476                                                                           20190523085430ORDERSORDERS05          US
E2EDK01005300                1     E2EDK010050     1       USD                                                                        Z4O14506907554
E2EDK03   300                2     E2EDK03   0     2   02220190523
E2EDKA1   300                3     E2EDKA1   0     2   RE                  MSFTASWI
E2EDKA1   300                4     E2EDKA1   0     2   US                  MSFTASWI
E2EDKA1   300                5     E2EDKA1   0     2   WE                  MSFTASWILIC
E2EDKA1   300                6     E2EDKA1   0     2   Z1 KKKKKKK                           ABC YYYYYYYYYYY ZZ                                                                                                                          BBBBBBBBBBBBBBBB 11                                                                                      ttttttttttt                                 6666              US                                                                                                999 999 99 99                                                                                                                SSSSSSS SSS SSSSSS                                                                                                                                SSSSSSS SSS SSSSSS
E2EDKA1   300                7     E2EDKA1   0     2   Z2 KKKKKKK                           BBBBBBBBBBBBBBBB DDDDDDDD ZZ                                                                                                                EEEEEEEEEEE 86                                                                                           rrrrrrrr                                    8888              US                                                                                                999 999 99 99                                                                                                                NNNNNN NNNNNN                                                                                                                                     NNNNNN NNNNNN
E2EDK02   300                8     E2EDK02   0     2   901Z
E2EDK02   300                9     E2EDK02   0     2   90399680096ZZS2002
E2EDK02   300                10    E2EDK02   0     2   902S
E2EDKT1   300                11    E2EDKT1   0     2   Z1EME
E2EDKT2   300                12    E2EDKT2   0     3   xxx@xxx-xx.xx
E2EDKT1   300                13    E2EDKT1   0     2   Z2EME
E2EDKT2   300                14    E2EDKT2   0     3   x.xxxxxx@xxxxxxxx-xxxxxxxxxx.xx
E2EDP01001300                15    E2EDP010010     2   10         1              EA                          999.9
E2EDP19   300                16    E2EDP19   0     3   00AAAA-11111</idocData>
</SendIdoc>
```



### <a name="create-http-response-action"></a>Crea azione di risposta HTTP

Ora aggiungere un'azione di risposta al flusso di lavoro dell'app logica e includere l'output dall'azione SAP. In questo modo, l'app per la logica restituisce i risultati dal server SAP per il richiedente originale.

1. Nella finestra di progettazione di app per la logica, sotto l'azione SAP, selezionare **nuovo passaggio**.

1. Nella casella di ricerca immettere `response` come filtro. Nell'elenco **azioni** selezionare **risposta**.

1. Fare clic all'interno della casella **Corpo** casella in modo che venga visualizzato l'elenco di contenuto dinamico. Da tale elenco, in **Invia messaggio a SAP** selezionare il campo **corpo** .

   ![Completare l'operazione SAP](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Salvare l'app per la logica.

#### <a name="create-rfc-request-response"></a>Crea richiesta-risposta RFC

> [!NOTE]
> Il trigger SAP riceve IDocs su tRFC, che non ha un parametro di risposta da progettazione. 

È necessario creare un modello di richiesta e risposta se è necessario ricevere risposte usando una chiamata di funzione remota (RFC) per le app per la logica di SAP ABAP. Per ricevere IDocs nell'app per la logica, è necessario effettuare la prima azione di una [richiesta http](../connectors/connectors-native-reqres.md#add-a-response-action) con un codice di stato `200 OK` e senza contenuto. Questo passaggio consigliato completa immediatamente il trasferimento asincrono di SAP LUW su tRFC, che lascia nuovamente disponibile la conversazione SAP CPIC. È quindi possibile aggiungere altre azioni nell'app per la logica per elaborare i IDoc ricevuti senza bloccare ulteriori trasferimenti.

Per implementare un modello di richiesta e risposta, è necessario innanzitutto individuare lo schema RFC utilizzando il [ `generate schema` comando](#generate-schemas-for-artifacts-in-sap). Lo schema generato ha due nodi radice possibili: 

1. Il nodo della richiesta, ovvero la chiamata ricevuta da SAP.

1. Il nodo della risposta, ovvero la risposta a SAP.

Nell'esempio seguente viene generato un modello di richiesta e risposta dal `STFC_CONNECTION` modulo RFC. Il codice XML della richiesta viene analizzato per estrarre un valore del nodo nel quale le richieste SAP `<ECHOTEXT>` . La risposta inserisce il timestamp corrente come valore dinamico. Si riceve una risposta simile quando si invia una `STFC_CONNECTION` RFC da un'app per la logica a SAP.

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

### <a name="test-logic-app"></a>Test dell'app per la logica

1. Se l'app per la logica non è già abilitata, scegliere **Panoramica** dal menu dell'app per la logica. Sulla barra degli strumenti selezionare **Abilita**.

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Esegui**. Questo passaggio avvia manualmente l'app per la logica.

1. Attivare l'app per la logica inviando una richiesta HTTP POST all'URL nel trigger della richiesta HTTP.
Includere il contenuto del messaggio con la richiesta. Per inviare la richiesta, è possibile usare uno strumento, ad esempio [Postman](https://www.getpostman.com/apps).

   In questo articolo, la richiesta invia un file IDoc, che deve essere in formato XML e includere lo spazio dei nomi per l'azione SAP che si sta usando, ad esempio:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Dopo aver inviato la richiesta HTTP, attendere la risposta dall'app per la logica.

   > [!NOTE]
   > L'app per la logica può andare in timeout se tutti i passaggi necessari per la risposta non terminano entro il [limite di timeout della richiesta](./logic-apps-limits-and-config.md). Se si verifica questa condizione, le richieste potrebbero venire bloccate. Per diagnosticare i problemi, vedere le informazioni su come [controllare e monitorare le app per la logica](../logic-apps/monitor-logic-apps.md).

A questo punto è stata creata un'app per la logica in grado di comunicare con il server SAP. Dopo aver impostato una connessione a SAP per l'app per la logica, è possibile esplorare altre azioni SAP disponibili, ad esempio BAPI e RFC.

## <a name="receive-message-from-sap"></a>Ricevi messaggio da SAP

Questo esempio usa un'app per la logica che si attiva quando l'app riceve un messaggio da un sistema SAP.

### <a name="add-an-sap-trigger"></a>Aggiungere un trigger SAP

1. Nella portale di Azure creare un'app per la logica vuota, che apre la finestra di progettazione delle app per la logica.

1. Nella casella di ricerca immettere `sap` come filtro. Dall'elenco **trigger** selezionare **quando un messaggio viene ricevuto da SAP**.

   ![Aggiungere trigger SAP](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   In alternativa, è possibile selezionare la scheda **Enterprise** , quindi selezionare il trigger:

   ![Aggiungi trigger SAP dalla scheda Enterprise](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Se la connessione esiste già, continuare con il passaggio successivo per poter configurare l'azione SAP. Tuttavia, se vengono richiesti i dettagli della connessione, fornire le informazioni in modo che sia possibile creare una connessione al server SAP locale adesso.

   1. Specificare un nome per la connessione.

   1. Se si usa il gateway dati, seguire questa procedura:

      1. Nella sezione **gateway dati** , in **sottoscrizione**, selezionare innanzitutto la sottoscrizione di Azure per la risorsa del gateway dati creata nella portale di Azure per l'installazione del gateway dati.

      1. In **gateway connessione** selezionare la risorsa del gateway dati in Azure.

   1. Continuare a fornire informazioni sulla connessione. Per la proprietà **tipo di accesso** , eseguire il passaggio a seconda che la proprietà sia impostata su **server applicazioni** o **gruppo**:

      * Per il **server applicazioni**, queste proprietà, che in genere appaiono facoltative, sono obbligatorie:

        ![Creare una connessione al server applicazioni SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Per il **gruppo**, queste proprietà, che in genere appaiono facoltative, sono obbligatorie:

        ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Per impostazione predefinita, la tipizzazione forte viene utilizzata per verificare la presenza di valori non validi eseguendo la convalida XML rispetto allo schema. Questo comportamento può essere utile per rilevare i problemi precedenti. L'opzione di **tipizzazione sicura** è disponibile per la compatibilità con le versioni precedenti e controlla solo la lunghezza della stringa. Altre informazioni sull' [opzione di tipizzazione sicura](#safe-typing).

   1. Al termine, selezionare **Crea**.

      App per la logica configura e testa la connessione per assicurarsi che la connessione funzioni correttamente.

1. Fornire i [parametri richiesti](#parameters) in base alla configurazione del sistema SAP. 

   È possibile [filtrare i messaggi ricevuti dal server SAP specificando un elenco di azioni SAP](#filter-with-sap-actions).

   È possibile selezionare un'azione SAP dalla selezione file:

   ![Aggiungere un'azione SAP all'app per la logica](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   In alternativa, è possibile specificare manualmente un'azione:

   ![Immettere manualmente l'azione SAP che si vuole usare](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Di seguito un esempio che illustra come viene visualizzata l'azione quando si configura il trigger per ricevere più messaggi.

   ![Esempio di trigger che riceve più messaggi](media/logic-apps-using-sap-connector/example-trigger.png)

   Per altre informazioni sull'azione SAP, vedere [schemi di messaggio per operazioni IDOC](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Salvare l'app per la logica in modo da poter iniziare a ricevere messaggi dal sistema SAP. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

App per la logica è ora pronta per ricevere messaggi dal sistema SAP.

> [!NOTE]
> Il trigger SAP non è un trigger di polling, ma è invece un trigger basato su webhook. Se si usa il gateway dati, il trigger viene chiamato dal gateway dati solo quando esiste un messaggio, pertanto non è necessario eseguire il polling.

Se si riceve un errore di **500 gateway non valido** con un messaggio simile al **servizio ' sapgw00' sconosciuto**, sostituire il nome del servizio gateway nella connessione API e attivare la configurazione con il numero di porta. Nell'errore di esempio seguente, `sapgw00` deve essere sostituito con un numero di porta reale, ad esempio `3300` . 

```json
"body": {
   "error": {
      "code": 500,
      "source": "EXAMPLE-FLOW-NAME.eastus.environments.microsoftazurelogicapps.net",
      "clientRequestId": "00000000-0000-0000-0000-000000000000",
      "message": "BadGateway",
      "innerError": {
         "error": {
            "code": "UnhandledException",
            "message": "\nERROR service 'sapgw00' unknown\nTIME Wed Nov 11 19:37:50 2020\nRELEASE 721\nCOMPONENT NI (network interface)\nVERSION 40\nRC -3\nMODULE ninti.c\nLINE 933\nDETAIL NiPGetServByName: 'sapgw00' not found\nSYSTEM CALL getaddrinfo\nCOUNTER 1\n\nRETURN CODE: 20"
         }
      }
```

#### <a name="parameters"></a>Parametri

Insieme a semplici input di stringa e numero, il connettore SAP accetta i parametri di tabella seguenti ( `Type=ITAB` input):

* Parametri di direzione della tabella, sia di input che di output, per le versioni SAP precedenti.

* Modifica dei parametri, che sostituiscono i parametri della direzione della tabella per le versioni più recenti di SAP.

* Parametri della tabella gerarchica

#### <a name="filter-with-sap-actions"></a>Filtrare con azioni SAP

Facoltativamente, è possibile filtrare i messaggi ricevuti dall'app per la logica dal server SAP fornendo un elenco, o una matrice, con una o più azioni SAP. Per impostazione predefinita, questa matrice è vuota, il che significa che l'app per la logica riceve tutti i messaggi dal server SAP senza filtrare. 

Quando si configura il filtro della matrice, il trigger riceve solo i messaggi dai tipi di azione SAP specificati e rifiuta tutti gli altri messaggi dal server SAP. Tuttavia, questo filtro non influisce sul fatto che la tipizzazione del payload ricevuto sia debole o forte.

Qualsiasi filtro azioni SAP si verifica a livello dell'adattatore SAP per il gateway dati locale. Per altre informazioni, vedere [come inviare test IDOCs alle app per la logica da SAP](#test-sending-idocs-from-sap).

Se non è possibile inviare pacchetti IDoc da SAP al trigger dell'app per la logica, vedere il messaggio di rifiuto della chiamata a Transactional RFC (tRFC) nella finestra di dialogo SAP tRFC (codice T SM58). Nell'interfaccia SAP è possibile che vengano visualizzati i messaggi di errore seguenti, che vengono ritagliati a causa dei limiti della sottostringa nel campo di **testo dello stato** .

* `The RequestContext on the IReplyChannel was closed without a reply being`: Gli errori imprevisti si verificano quando il gestore catch-all per il canale termina il canale a causa di un errore e ricompila il canale per elaborare altri messaggi.

  * Per confermare che l'app per la logica ha ricevuto il IDoc, [aggiungere un'azione di risposta](../connectors/connectors-native-reqres.md#add-a-response-action) che restituisce un `200 OK` codice di stato. IDoc viene trasportato tramite tRFC, che non consente un payload di risposta.

  * Se invece è necessario rifiutare il IDoc, rispondere con un codice di stato HTTP diverso da in `200 OK` modo che l'adapter SAP restituisca un'eccezione a SAP per conto dell'utente. 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`: Gli errori previsti si verificano con altri errori, ad esempio l'impossibilità di generare un payload XML IDoc perché i segmenti non vengono rilasciati da SAP, quindi i metadati del tipo di segmento necessari per la conversione risultano mancanti. 

  * Per fare in modo che questi segmenti vengano rilasciati da SAP, contattare il tecnico ABAP per il sistema SAP.
### <a name="asynchronous-request-reply-for-triggers"></a>Richiesta-risposta asincrona per i trigger

Il connettore SAP supporta il [modello Request/Reply asincrono](/azure/architecture/patterns/async-request-reply) di Azure per i trigger di app per la logica. È possibile usare questo modello per creare richieste riuscite che altrimenti avrebbero avuto esito negativo con il modello request/reply sincrono predefinito. 

> [!TIP]
> Nelle app per la logica con più azioni di risposta, tutte le azioni di risposta devono usare lo stesso modello Request/Reply. Ad esempio, se l'app per la logica usa un controllo switch con più azioni di risposta possibili, è necessario configurare tutte le azioni di risposta in modo che usino lo stesso modello Request/Reply, sincrono o asincrono. 

L'abilitazione della risposta asincrona per l'azione di risposta consente all'app per la logica di rispondere con una `202 Accepted` risposta quando una richiesta è stata accettata per l'elaborazione. La risposta contiene un'intestazione location che è possibile usare per recuperare lo stato finale della richiesta.

Per configurare un modello Request/Reply asincrono per l'app per la logica usando il connettore SAP:

1. Aprire l'app per la logica nella **finestra di progettazione di app** per la logica.

1. Verificare che il connettore SAP sia il trigger per l'app per la logica.

1. Aprire l'azione di **risposta** dell'app per la logica. Nella barra del titolo dell'azione selezionare il menu (**...**) &gt; **Impostazioni**.

1. Nelle **Impostazioni** per l'azione di risposta, attivare l'interruttore in **risposta asincrona**. Selezionare fine.

1. Salvare le modifiche apportate all'app per la logica.

## <a name="find-extended-error-logs"></a>Trovare log degli errori estesi

Per i messaggi di errore completi, controllare i log estesi dell'adapter SAP. È anche possibile [abilitare un file di log esteso per il connettore SAP](#extended-sap-logging-in-on-premises-data-gateway).

Per le versioni del gateway dati locale a partire dal 2020 giugno e versioni successive, è possibile [abilitare i log del gateway nelle impostazioni dell'app](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app). 

* Il livello di registrazione predefinito è **avviso**.

* Se si Abilita la  **registrazione aggiuntiva** nelle impostazioni di **diagnostica** dell'app del gateway dati locale, il livello di registrazione viene aumentato a **informativo**.

* Per aumentare il livello di registrazione a **verbose**, aggiornare la seguente impostazione nel file di configurazione. In genere, il file di configurazione si trova in `C:\Program Files\On-premises data gateway\Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` .

```json
<setting name="SapTraceLevel" serializeAs="String">
   <value>Verbose</value>
</setting>
```

Per le versioni del gateway dati locale a partire dall'aprile 2020 e versioni precedenti, i log sono disabilitati per impostazione predefinita.

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>Registrazione SAP estesa nel gateway dati locale

Se si usa un [gateway dati locale per le app per la logica](../logic-apps/logic-apps-gateway-install.md), è possibile configurare un file di log esteso per il connettore SAP. È possibile usare il gateway dati locale per reindirizzare gli eventi di Event Tracing for Windows (ETW) in file di log rotanti inclusi nei file zip di registrazione del gateway. 

È possibile [esportare tutti i log di configurazione e servizio del gateway](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app) in un file con estensione zip in dalle impostazioni dell'app gateway.

> [!NOTE]
> La registrazione estesa potrebbe influire sulle prestazioni delle app per la logica quando è sempre abilitata. È consigliabile disattivare i file di log estesi dopo aver completato l'analisi e la risoluzione di un problema.

#### <a name="capture-etw-events"></a>Acquisisci eventi ETW

Facoltativamente, gli utenti avanzati possono acquisire direttamente gli eventi ETW. È quindi possibile [utilizzare i dati in diagnostica di Azure nell'hub eventi](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md) o [raccogliere i dati nei log di monitoraggio di Azure](../azure-monitor/agents/diagnostics-extension-logs.md). Per ulteriori informazioni, vedere le [procedure consigliate per la raccolta e l'archiviazione di dati](/azure/architecture/best-practices/monitoring#collecting-and-storing-data). È possibile usare [PerfView](https://github.com/Microsoft/perfview/blob/master/README.md) per lavorare con i file ETL risultanti oppure è possibile scrivere un programma personalizzato. Questa procedura dettagliata USA PerfView:

1. Nel menu PerfView selezionare **Collect** &gt; **Collect** per acquisire gli eventi.

1. Nel campo **provider aggiuntivo** immettere `*Microsoft-LobAdapter` per specificare il provider SAP per l'acquisizione di eventi di adapter SAP. Se non si specificano queste informazioni, la traccia include solo eventi ETW generali.

1. Mantieni le altre impostazioni predefinite. Se lo si desidera, è possibile modificare il nome o il percorso del file nel campo **file di dati** .

1. Selezionare **Avvia raccolta** per avviare la traccia.

1. Dopo aver riprodotto il problema o raccolto un numero sufficiente di dati di analisi, selezionare **Arresta raccolta**.

Per condividere i dati con un'altra parte, ad esempio i tecnici del supporto di Azure, comprimere il file ETL.

Per visualizzare il contenuto della traccia:

1. In PerfView selezionare **file** &gt; **Apri** e selezionare il file ETL appena generato.

1. Nella barra laterale PerfView, la sezione **eventi** nel file ETL.

1. In **filtro** filtrare `Microsoft-LobAdapter` per per visualizzare solo gli eventi rilevanti e i processi del gateway.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Per attivare l'app per la logica, inviare un messaggio dal sistema SAP.

1. Scegliere **Panoramica** dal menu dell'app per la logica. Esaminare la **cronologia delle esecuzioni** per le nuove esecuzioni per l'app per la logica.

1. Aprire l'esecuzione più recente, che mostra il messaggio inviato dal sistema SAP nella sezione output di trigger.

### <a name="test-sending-idocs-from-sap"></a>Testare l'invio di IDocs da SAP

Per inviare IDocs da SAP all'app per la logica, è necessaria la configurazione minima seguente:

> [!IMPORTANT]
> Usare questi passaggi solo quando si testa la configurazione di SAP con l'app per la logica. Gli ambienti di produzione richiedono una configurazione aggiuntiva.

1. [Configurare una destinazione RFC in SAP](#create-rfc-destination)

1. [Creare una connessione ABAP alla destinazione RFC](#create-abap-connection)

1. [Creare una porta di ricezione](#create-receiver-port)

1. [Creare una porta mittente](#create-sender-port)

1. [Creazione di un partner di sistema logico](#create-logical-system-partner)

1. [Creare un profilo partner](#create-partner-profiles)

1. [Testare l'invio dei messaggi](#test-sending-messages)

#### <a name="create-rfc-destination"></a>Crea destinazione RFC

1. Per aprire la **configurazione delle impostazioni delle connessioni RFC** , nell'interfaccia SAP usare il codice di transazione **Sm59** (codice T) con il prefisso **/n** .

1. Selezionare **connessioni TCP/IP**  >  **Crea**.

1. Creare una nuova destinazione RFC con le impostazioni seguenti:
    
    * Per la **destinazione RFC** immettere un nome.
    
    * Nella scheda **Impostazioni tecniche** , in **tipo di attivazione** selezionare **programma server registrato**. Per l' **ID del programma** immettere un valore. In SAP il trigger dell'app per la logica verrà registrato con questo identificatore.

    > [!IMPORTANT]
    > L' **ID del programma** SAP fa distinzione tra maiuscole e minuscole. Assicurarsi di usare in modo coerente lo stesso formato di case per l' **ID del programma** quando si configura l'app per la logica e il server SAP. In caso contrario, è possibile che vengano visualizzati gli errori seguenti in tRFC monitor (T-Code SM58) quando si tenta di inviare un IDoc a SAP:
    >
    > * **Impossibile trovare la funzione IDOC_INBOUND_ASYNCHRONOUS**
    > * **Il client RFC non ABAP (tipo di partner) non è supportato**
    >
    > Per ulteriori informazioni su SAP, vedere le note seguenti (login required) <https://launchpad.support.sap.com/#/notes/2399329> e <https://launchpad.support.sap.com/#/notes/353597> .
    
    * Nella scheda **Unicode** , per **tipo di comunicazione con sistema di destinazione**, selezionare **Unicode**.

1. Salvare le modifiche.

1. Registrare il nuovo **ID programma** con app per la logica di Azure.

1. Per testare la connessione, nell'interfaccia SAP, nella nuova **destinazione RFC**, selezionare test di **connessione**.

#### <a name="create-abap-connection"></a>Crea connessione ABAP

1. Per aprire la **configurazione delle impostazioni delle connessioni RFC** , nell'interfaccia SAP usare il codice **sm59** _ Transaction (codice T) con il prefisso _ */n**.

1. Selezionare **ABAP Connections**  >  **create**.

1. Per **destinazione RFC** immettere l'identificatore per [il sistema SAP di test](#create-rfc-destination).

1. Salvare le modifiche.

1. Per testare la connessione, selezionare **test di connessione**.

#### <a name="create-receiver-port"></a>Crea porta ricevitore

1. Per aprire le **porte nelle impostazioni di elaborazione di IDOC** , nell'interfaccia SAP usare il codice di transazione **We21** (codice T) con il prefisso **/n** .

1. Select **ports**  >  **Transactional RFC**  >  **create**.

1. Nella casella Impostazioni visualizzata selezionare **nome porta personale**. Per la porta di test immettere un **nome**. Salvare le modifiche.

1. In impostazioni per la nuova porta del ricevitore, per **destinazione RFC**, immettere l'identificatore per [la destinazione RFC di test](#create-rfc-destination).

1. Salvare le modifiche.

#### <a name="create-sender-port"></a>Crea porta mittente

1.  Per aprire le **porte nelle impostazioni di elaborazione di IDOC** , nell'interfaccia SAP usare il codice di transazione **We21** (codice T) con il prefisso **/n** .

1. Select **ports**  >  **Transactional RFC**  >  **create**.

1. Nella casella Impostazioni visualizzata selezionare **nome porta personale**. Per la porta di test, immettere un **nome** che inizia con **SAP**. Tutti i nomi delle porte del mittente devono iniziare con le lettere **SAP**, ad esempio **SAPTEST**. Salvare le modifiche.

1. Nelle impostazioni per la porta del nuovo mittente, per la **destinazione RFC**, immettere l'identificatore per [la connessione ABAP](#create-abap-connection).

1. Salvare le modifiche.

#### <a name="create-logical-system-partner"></a>Creazione di un partner di sistema logico

1. Per aprire la **visualizzazione delle modifiche "sistemi logici":** impostazioni di panoramica, nell'interfaccia SAP usare il codice di transazione **BD54** (T-Code).

1. Accettare il messaggio di avviso visualizzato: **Attenzione: la tabella è tra client**

1. Sopra l'elenco che mostra i sistemi logici esistenti, selezionare **nuove voci**.

1. Per il nuovo sistema logico, immettere un identificatore **Log.System** e una descrizione del **nome** breve. Salvare le modifiche.

1. Quando viene visualizzata la richiesta **per Workbench** , creare una nuova richiesta fornendo una descrizione o, se è già stata creata una richiesta, ignorare questo passaggio.

1. Dopo aver creato la richiesta Workbench, collegare la richiesta alla richiesta di aggiornamento della tabella. Per confermare che la tabella è stata aggiornata, salvare le modifiche.

#### <a name="create-partner-profiles"></a>Creazione dei profili dei partner

Per gli ambienti di produzione, è necessario creare due profili partner. Il primo profilo è per il mittente, ovvero l'organizzazione e il sistema SAP. Il secondo profilo è per il ricevitore, ovvero l'app per la logica.

1. Per aprire le impostazioni dei **profili partner** , nell'interfaccia SAP usare il codice di transazione **We20** (codice T) con il prefisso **/n** .

1. In **profili partner** selezionare **tipo di partner LS**  >  **Crea**.

1. Creare un nuovo profilo partner con le seguenti impostazioni:

    * Per **partner no**, immettere [l'identificatore del partner di sistema logico](#create-logical-system-partner).

    * Per **Partin. Digitare**, immettere **ls**.

    * Per **Agent**, immettere l'identificatore per l'account utente SAP da usare quando si registrano gli identificatori di programma per app per la logica di Azure o altri sistemi non SAP.

1. Salvare le modifiche. Se [il partner di sistema logico non è stato creato](#create-logical-system-partner), si riceve l'errore, si **immette un numero di partner valido**.

1. Nelle impostazioni del profilo partner, in **parmtrs in uscita**, selezionare **Crea parametro** in uscita.

1. Creare un nuovo parametro in uscita con le impostazioni seguenti:

    * Immettere il **tipo di messaggio**, ad esempio **crema**.

    * Immettere l' [identificatore della porta del destinatario](#create-receiver-port).

    * Immettere le dimensioni IDoc per il **pacchetto. Dimensioni**. In alternativa, per [inviare IDOCs uno alla volta da SAP](#receive-idoc-packets-from-sap), selezionare **pass IDOC immediatamente**.

1. Salvare le modifiche.

#### <a name="test-sending-messages"></a>Testare l'invio dei messaggi

1. Per aprire lo **strumento di test per le impostazioni di elaborazione IDOC** , nell'interfaccia SAP usare il codice di transazione **We19** (codice T) con il prefisso **/n** .

1. In **modello per test** selezionare **via messaggio** e immettere il tipo di messaggio, ad esempio **crema**. Selezionare **Crea**.

1. Confermare il **tipo di IDOC?** messaggio selezionando **continua**.

1. Selezionare il nodo **EDIDC** . Immettere i valori appropriati per le porte di ricezione e mittente. Selezionare **Continua**.

1. Selezionare **elaborazione in uscita standard**.

1. Per avviare l'elaborazione del IDoc in uscita, selezionare **continue (continua**). Al termine dell'elaborazione, viene visualizzato il **IDOC inviato al sistema SAP o al messaggio del programma esterno** .

1.  Per verificare la presenza di errori di elaborazione, usare il codice di transazione **SM58** (codice T) con il prefisso **/n** .

## <a name="receive-idoc-packets-from-sap"></a>Ricevere pacchetti IDoc da SAP

È possibile configurare SAP per l' [invio di IDOCs in pacchetti](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html), ovvero batch o gruppi di IDOCs. Per ricevere pacchetti IDoc, il connettore SAP e in particolare il trigger non necessita di una configurazione aggiuntiva. Tuttavia, per elaborare ogni elemento in un pacchetto IDoc dopo che il trigger ha ricevuto il pacchetto, sono necessari alcuni passaggi aggiuntivi per suddividere il pacchetto in un singolo IDocs.

Di seguito è riportato un esempio che illustra come estrarre singoli IDocs da un pacchetto usando la [ `xpath()` funzione](./workflow-definition-language-functions-reference.md#xpath):

1. Prima di iniziare, è necessaria un'app per la logica con un trigger SAP. Se non è già presente nell'app per la logica, seguire i passaggi precedenti in questo argomento per [configurare un'app per la logica con un trigger SAP](#receive-message-from-sap).

    > [!IMPORTANT]
    > L' **ID del programma** SAP fa distinzione tra maiuscole e minuscole. Assicurarsi di usare in modo coerente lo stesso formato di case per l' **ID del programma** quando si configura l'app per la logica e il server SAP. In caso contrario, è possibile che vengano visualizzati gli errori seguenti in tRFC monitor (T-Code SM58) quando si tenta di inviare un IDoc a SAP:
    >
    > * **Impossibile trovare la funzione IDOC_INBOUND_ASYNCHRONOUS**
    > * **Il client RFC non ABAP (tipo di partner) non è supportato**
    >
    > Per ulteriori informazioni su SAP, vedere le note seguenti (login required) <https://launchpad.support.sap.com/#/notes/2399329> e <https://launchpad.support.sap.com/#/notes/353597> .

   Ad esempio:

   ![Aggiungere il trigger SAP all'app per la logica](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. [Aggiungere un'azione di risposta all'app per la logica](../connectors/connectors-native-reqres.md#add-a-response-action) per rispondere immediatamente con lo stato della richiesta SAP. È consigliabile aggiungere questa azione subito dopo il trigger, per liberare il canale di comunicazione con il server SAP. Scegliere uno dei seguenti codici di stato ( `statusCode` ) da usare nell'azione di risposta:

    * **202 accettato**, ovvero la richiesta è stata accettata per l'elaborazione, ma l'elaborazione non è ancora stata completata.

    * **204 nessun contenuto**, che indica che il server ha completato correttamente la richiesta e che non sono presenti contenuti aggiuntivi da inviare nel corpo del payload della risposta. 

    * **200 OK**. Questo codice di stato contiene sempre un payload, anche se il server genera un corpo di payload di lunghezza zero. 

1. Ottenere lo spazio dei nomi radice dal IDoc XML che l'app per la logica riceve da SAP. Per estrarre questo spazio dei nomi dal documento XML, aggiungere un passaggio per la creazione di una variabile di stringa locale e archiviare tale spazio dei nomi utilizzando un' `xpath()` espressione:

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Ottenere lo spazio dei nomi radice da IDoc](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Per estrarre un singolo IDoc, aggiungere un passaggio per la creazione di una variabile di matrice e archiviare la raccolta IDoc usando un'altra `xpath()` espressione:

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Ottenere una matrice di elementi](./media/logic-apps-using-sap-connector/get-array.png)

   La variabile di matrice rende disponibili ogni IDoc per l'app per la logica da elaborare singolarmente tramite l'enumerazione della raccolta. In questo esempio, l'app per la logica trasferisce ogni IDoc a un server SFTP usando un ciclo:

   ![Invia IDoc al server SFTP](./media/logic-apps-using-sap-connector/loop-batch.png)

   Ogni IDoc deve includere lo spazio dei nomi radice, che è il motivo per cui il contenuto del file viene incapsulato all'interno di un `<Receive></Receive` elemento insieme allo spazio dei nomi radice prima di inviare il IDOC all'app downstream o al server SFTP in questo caso.

È possibile usare il modello di avvio rapido per questo modello selezionando questo modello nella finestra di progettazione di app per la logica quando si crea una nuova app per la logica.

![Selezionare il modello di app per la logica batch](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Generare gli schemi per gli artefatti in SAP

In questo esempio viene usata un'app per la logica che è possibile attivare con una richiesta HTTP. Per generare gli schemi per i IDoc e i bruni specificati, l'azione SAP **genera lo schema** invia una richiesta a un sistema SAP.

Questa azione SAP restituisce un [XML Schema](#sample-xml-schemas), non il contenuto o i dati del documento XML stesso. Gli schemi restituiti nella risposta vengono caricati in un account di integrazione usando il connettore Azure Resource Manager. Gli schemi contengono le parti seguenti:

* Struttura del messaggio di richiesta. Usare queste informazioni per creare l'elenco di Bova `get` .

* Struttura del messaggio di risposta. Usare queste informazioni per analizzare la risposta. 

Per inviare il messaggio di richiesta, usare l'azione SAP generico **Invia messaggio a SAP** o le azioni di chiamata di destinazione di **Bova** .

### <a name="sample-xml-schemas"></a>Schemi XML di esempio

Per informazioni su come generare un XML Schema da usare per la creazione di un documento di esempio, vedere gli esempi seguenti. Questi esempi illustrano come usare molti tipi di payload, tra cui:

* [Richieste RFC](#xml-samples-for-rfc-requests)

* [Richieste di Bova](#xml-samples-for-bapi-requests)

* [Richieste IDoc](#xml-samples-for-idoc-requests)

* Tipi di dati XML Schema semplici o complessi

* Parametri della tabella

* Comportamenti XML facoltativi

È possibile iniziare la XML Schema con un prologo XML facoltativo. Il connettore SAP funziona con o senza il prologo XML.

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>Esempi XML per richieste RFC

L'esempio seguente è una chiamata RFC di base. Il nome RFC è `STFC_CONNECTION` . Questa richiesta usa lo spazio dei nomi predefinito `xmlns=` , ma è possibile assegnare e usare alias degli spazi dei nomi, ad esempio `xmmlns:exampleAlias=` . Il valore dello spazio dei nomi è lo spazio dei nomi per tutte le RFC in SAP per i servizi Microsoft. Nella richiesta è presente un parametro di input semplice `<REQUTEXT>` .

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

Nell'esempio seguente viene riportata una chiamata RFC con un parametro table. Questa chiamata di esempio e il relativo gruppo di RFC di test sono disponibili come parte di tutti i sistemi SAP. Il nome del parametro della tabella è `TCPICDAT` . Il tipo di riga della tabella è `ABAPTEXT` e questo elemento si ripete per ogni riga della tabella. Questo esempio contiene una singola riga denominata `LINE` . Le richieste con un parametro table possono contenere un numero qualsiasi di campi, dove il numero è un numero intero positivo (*n*). 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

Nell'esempio seguente viene riportata una chiamata RFC con un parametro table con un campo anonimo. Un campo anonimo è quando al campo non è assegnato alcun nome. I tipi complessi vengono dichiarati in uno spazio dei nomi separato, in cui la dichiarazione imposta un nuovo valore predefinito per il nodo corrente e tutti i relativi elementi figlio. Nell'esempio viene usato il codice esadecimale `x002F` come carattere di escape per il simbolo */* , perché questo simbolo è riservato nel nome del campo SAP.

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

Nell'esempio seguente sono inclusi i prefissi per gli spazi dei nomi. È possibile dichiarare tutti i prefissi contemporaneamente oppure è possibile dichiarare un numero qualsiasi di prefissi come attributi di un nodo. L'alias dello spazio dei nomi RFC `ns0` viene usato come radice e parametri per il tipo di base.

> [!NOTE]
> i tipi complessi vengono dichiarati in uno spazio dei nomi diverso per i tipi RFC con l'alias `ns3` invece dello spazio dei nomi RFC normale con l'alias `ns0` .

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>Esempi XML per richieste BAPI

Gli esempi XML seguenti sono richieste di esempio per [chiamare il metodo Bova](#call-bapi-action).

> [!NOTE]
> SAP rende gli oggetti business disponibili per i sistemi esterni, descrivendoli in risposta a RFC `RPY_BOR_TREE_INIT` , che app per la logica emette senza filtro di input. App per la logica ispeziona la tabella di output `BOR_TREE` . Il `SHORT_TEXT` campo viene usato per i nomi degli oggetti business. Gli oggetti business non restituiti da SAP nella tabella di output non sono accessibili alle app per la logica.
> 
> Se si usano oggetti business personalizzati, è necessario assicurarsi di pubblicare e rilasciare questi oggetti business in SAP. In caso contrario, SAP non elenca gli oggetti business personalizzati nella tabella di output `BOR_TREE` . Non è possibile accedere agli oggetti business personalizzati nelle app per la logica fino a quando non si espongono gli oggetti business da SAP. 

Nell'esempio seguente viene ottenuto un elenco di banche utilizzando il metodo Bova `GETLIST` . Questo esempio contiene l'oggetto business per una banca `BUS1011` . 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

Nell'esempio seguente viene creato un oggetto Bank usando il `CREATE` metodo. In questo esempio viene utilizzato lo stesso oggetto business dell'esempio precedente, `BUS1011` . Quando si usa il `CREATE` metodo per creare una banca, assicurarsi di eseguire il commit delle modifiche perché per impostazione predefinita non viene eseguito il commit di questo metodo.

> [!TIP]
> Assicurarsi che il documento XML segua tutte le regole di convalida configurate nel sistema SAP. Ad esempio, in questo documento di esempio, la chiave della banca ( `<BANK_KEY>` ) deve essere un numero di routing bancario, noto anche come numero ABA, negli Stati Uniti.

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

Nell'esempio seguente vengono recuperati i dettagli di una banca usando il numero di routing bancario, il valore per `<BANK_KEY>` . 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>Esempi XML per richieste IDoc

Per generare un XML Schema SAP IDoc normale, usare l'applicazione di **Accesso SAP** e il codice T `WE-60` . Accedere alla documentazione SAP tramite l'interfaccia utente grafica e generare schemi XML in formato XSD per i tipi e le estensioni di IDoc. Per una spiegazione dei formati e dei payload SAP generici e delle finestre di dialogo predefinite, vedere la [documentazione di SAP](https://help.sap.com/viewer/index).

In questo esempio vengono dichiarati il nodo radice e gli spazi dei nomi. L'URI nel codice di esempio, `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` , dichiara la configurazione seguente:

* `/IDoc` è la nota radice per tutti IDocs
* `/3` versione dei tipi di record per le definizioni dei segmenti comuni
* `/ORDERS05` è il tipo IDoc
* `//` è un segmento vuoto, perché non esiste alcuna estensione IDoc
* `/700` è la versione di SAP
* `/Send` è l'azione per inviare le informazioni a SAP

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

È possibile ripetere il `idocData` nodo per inviare un batch di IDOCs in una singola chiamata. Nell'esempio seguente è presente un record di controllo, `EDI_DC40` e più record di dati.

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

L'esempio seguente è un record di controllo IDoc di esempio, che usa il prefisso `EDI_DC` . È necessario aggiornare i valori in modo che corrispondano al tipo di installazione e IDoc di SAP. Ad esempio, il codice client di IDoc potrebbe non essere `800` . Contattare il team SAP per assicurarsi di usare i valori corretti per l'installazione di SAP.

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

L'esempio seguente è un record di dati di esempio con segmenti semplici. Questo esempio usa il formato di data SAP. I documenti con tipizzazione forte possono usare formati di data XML nativi, ad esempio `2020-12-31 23:59:59` .

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

Nell'esempio seguente viene riportato un record di dati con segmenti raggruppati. Il record include un nodo padre del gruppo, `E2EDKT1002GRP` , e più nodi figlio, tra cui `E2EDKT1002` e `E2EDKT2001` . 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

Il metodo consigliato consiste nel creare un identificatore IDoc per l'uso con tRFC. È possibile impostare questo identificatore di transazione, `tid` , usando l' [operazione Send IDOC](/connectors/sap/#send-idoc) nell'API del connettore SAP.

L'esempio seguente è un metodo alternativo per impostare l'identificatore della transazione o `tid` . In questo esempio, l'ultimo nodo del segmento record di dati e il nodo dati IDoc sono chiusi. Il GUID, `guid` , viene quindi usato come identificatore tRFC per rilevare i duplicati. 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

### <a name="add-an-http-request-trigger"></a>Aggiungere un trigger di richiesta HTTP

1. Nella portale di Azure creare un'app per la logica vuota, che apre la finestra di progettazione delle app per la logica.

1. Nella casella di ricerca immettere `http request` come filtro. Dall'elenco **trigger** selezionare **quando viene ricevuta una richiesta http**.

   ![Aggiungere un trigger della richiesta HTTP](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Salvare l'app per la logica per poter generare un URL endpoint per l'app per la logica.
Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   L'URL endpoint verrà visualizzato nel trigger, ad esempio:

   ![Generare l'URL per l'endpoint](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>Aggiungere un'azione SAP per generare schemi

1. Nella finestra di progettazione di app per la logica, sotto il trigger, selezionare **nuovo passaggio**.

   ![Aggiungere un nuovo passaggio all'app per la logica](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. Nella casella di ricerca immettere `sap` come filtro. Nell'elenco **azioni** selezionare **genera schemi**.
  
   ![Aggiungere l'azione "genera schemi" all'app per la logica](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   In alternativa, è possibile selezionare la scheda **Enterprise** e selezionare l'azione SAP.

   ![Selezionare l'azione di invio a SAP dalla scheda Enterprise](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Se la connessione esiste già, continuare con il passaggio successivo per poter configurare l'azione SAP. Tuttavia, se vengono richiesti i dettagli della connessione, fornire le informazioni in modo che sia possibile creare una connessione al server SAP locale adesso.

   1. Specificare un nome per la connessione.

   1. Nella sezione **gateway dati** , in **sottoscrizione**, selezionare innanzitutto la sottoscrizione di Azure per la risorsa del gateway dati creata nella portale di Azure per l'installazione del gateway dati. 
   
   1. In **gateway connessione** selezionare la risorsa del gateway dati in Azure.

   1. Continuare a fornire informazioni sulla connessione. Per la proprietà **tipo di accesso** , eseguire il passaggio a seconda che la proprietà sia impostata su **server applicazioni** o **gruppo**:
   
      * Per il **server applicazioni**, queste proprietà, che in genere appaiono facoltative, sono obbligatorie:

        ![Creare una connessione al server applicazioni SAP](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * Per il **gruppo**, queste proprietà, che in genere appaiono facoltative, sono obbligatorie:

        ![Creare una connessione al server di messaggistica SAP](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Per impostazione predefinita, la tipizzazione forte viene utilizzata per verificare la presenza di valori non validi eseguendo la convalida XML rispetto allo schema. Questo comportamento può essere utile per rilevare i problemi precedenti. L'opzione di **tipizzazione sicura** è disponibile per la compatibilità con le versioni precedenti e controlla solo la lunghezza della stringa. Altre informazioni sull' [opzione di tipizzazione sicura](#safe-typing).

   1. Al termine, selezionare **Crea**.

      App per la logica configura e testa la connessione per assicurarsi che la connessione funzioni correttamente.

1. Specificare il percorso dell'artefatto per cui si desidera generare lo schema.

   È possibile selezionare l'azione SAP dalla selezione file:

   ![Selezionare l'azione SAP](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   In alternativa, è possibile immettere manualmente l'azione:

   ![Immettere manualmente l'azione SAP](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Per generare gli schemi per più artefatti, specificare i dettagli dell'azione SAP per ogni artefatto, ad esempio:

   ![Selezionare Aggiungi nuovo elemento](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Mostrare due elementi](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Per ulteriori informazioni sull'azione SAP, vedere la pagina relativa agli [schemi di messaggio per le operazioni IDOC](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Esegui** per attivare un'esecuzione per l'app per la logica.

1. Aprire l'esecuzione e controllare gli output per l'azione **genera schemi** .

   Gli output mostrano gli schemi generati per l'elenco specificato di messaggi.

### <a name="upload-schemas-to-an-integration-account"></a>Caricare gli schemi in un account di integrazione

Facoltativamente, è possibile scaricare o archiviare gli schemi generati nei repository, quali blob, risorsa di archiviazione o account di integrazione. Gli account di integrazione offrono un'esperienza all'avanguardia con altre azioni XML, quindi l'esempio illustra come caricare gli schemi in un account di integrazione per la stessa app per la logica usando il connettore Azure Resource Manager.

1. Nella finestra di progettazione di app per la logica, sotto il trigger, selezionare **nuovo passaggio**.

1. Nella casella di ricerca immettere `Resource Manager` come filtro. Selezionare **Crea o aggiorna una risorsa**.

   ![Selezionare l'azione Azure Resource Manager](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Immettere i dettagli per l'azione, tra cui la sottoscrizione di Azure, il gruppo di risorse di Azure e l'account di integrazione. Per aggiungere token SAP ai campi, fare clic all'interno delle caselle per tali campi e selezionare dall'elenco di contenuto dinamico visualizzato.

   1. Aprire l'elenco **Aggiungi nuovo parametro** e selezionare il **percorso** e i campi delle **proprietà** .

   1. Specificare i dettagli per questi nuovi campi, come illustrato in questo esempio.

      ![Immettere i dettagli per l'azione Azure Resource Manager](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   L'azione SAP **Generare schemi** genera schemi come una raccolta, in modo che la finestra di progettazione aggiunga automaticamente un ciclo **For each** all'azione. Di seguito è riportato un esempio che illustra come viene visualizzata questa azione:

   ![Azione di Azure Resource Manager con ciclo "for each"](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > Gli schemi usano il formato con codifica base64. Per caricare gli schemi in un account di integrazione, devono essere decodificati usando la funzione `base64ToString()`. Di seguito è riportato un esempio che mostra il codice per l'elemento `"properties"`:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Salvare l'app per la logica. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

### <a name="test-your-logic-app"></a>Testare l'app per la logica

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Esegui** per attivare manualmente l'app per la logica.

1. Una volta completata l'esecuzione, passare all'account di integrazione e verificare che gli schemi generati esistano.

## <a name="enable-secure-network-communications"></a>Abilitare le comunicazioni di rete sicure

Prima di iniziare, verificare di aver soddisfatto i [prerequisiti](#prerequisites)elencati in precedenza, che si applicano solo quando si usa il gateway dati e le app per la logica vengono eseguite in Azure multi-tenant:

* Verificare che il gateway dati locale sia installato in un computer che si trova nella stessa rete del sistema SAP.

* Per SSO, il gateway dati è in esecuzione come utente mappato a un utente SAP.

* La libreria SNC che fornisce le funzioni di sicurezza aggiuntive viene installata nello stesso computer del gateway dati. Alcuni esempi includono [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos e NTLM.

   Per abilitare SNC per le richieste da e verso il sistema SAP, selezionare la casella di controllo **USA SNC** nella connessione SAP e specificare le proprietà seguenti:

   ![Configurare SAP SNC nella connessione](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Proprietà | Descrizione |
   |----------| ------------|
   | **Percorso della libreria SNC** | Nome o percorso della libreria SNC relativo al percorso di installazione di NCo o al percorso assoluto. Gli esempi sono `sapsnc.dll` o `.\security\sapsnc.dll` o `c:\security\sapsnc.dll` . |
   | **SSO SSO** | Quando ci si connette tramite SNC, l'identità SNC viene in genere usata per l'autenticazione del chiamante. Un'altra opzione consiste nell'eseguire l'override di in modo che sia possibile usare le informazioni relative all'utente e alla password per l'autenticazione del chiamante, ma la riga è ancora crittografata. |
   | **Nome della SNC** | Nella maggior parte dei casi, questa proprietà può essere omessa. La soluzione SNC installata in genere conosce il proprio nome SNC. Solo per le soluzioni che supportano più identità, potrebbe essere necessario specificare l'identità da usare per questa particolare destinazione o server. |
   | **Nome del partner SNC** | Nome del back-end SNC. |
   | **Qualità della protezione di SNC** | Qualità del servizio da utilizzare per la comunicazione SNC di questa particolare destinazione o server. Il valore predefinito è definito dal sistema back-end. Il valore massimo è definito dal prodotto di sicurezza usato per SNC. |
   |||

   > [!NOTE]
   > Non impostare le variabili di ambiente SNC_LIB e SNC_LIB_64 nel computer in cui sono presenti il gateway dati e la libreria SNC. Se impostate, hanno la precedenza sul valore della libreria SNC passato attraverso il connettore.

## <a name="safe-typing"></a>Tipizzazione sicura

Per impostazione predefinita, quando si crea la connessione SAP, viene usata la tipizzazione forte per verificare la presenza di valori non validi eseguendo la convalida XML rispetto allo schema. Questo comportamento può essere utile per rilevare i problemi precedenti. L'opzione di **tipizzazione sicura** è disponibile per la compatibilità con le versioni precedenti e controlla solo la lunghezza della stringa. Se si sceglie la **tipizzazione sicura**, il tipo di file dat e il tipo di Tim in SAP vengono considerati stringhe anziché come equivalenti XML, `xs:date` e `xs:time` , dove `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . La tipizzazione sicura influiscono sul comportamento per la generazione di tutti gli schemi, il messaggio di invio sia per il payload "inviato" sia per la risposta "ricevuto" e il trigger. 

Quando si usa la tipizzazione forte (la tipizzazione **sicura** non è abilitata), lo schema esegue il mapping dei tipi dat e Tim ai tipi XML più semplici:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Quando si inviano messaggi utilizzando la tipizzazione forte, la risposta dat e Tim è conforme al formato di tipo XML corrispondente:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Quando è abilitata la **tipizzazione sicura** , lo schema esegue il mapping dei tipi dat e Tim ai campi stringa XML con solo restrizioni di lunghezza, ad esempio:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Quando i messaggi vengono inviati con la **tipizzazione sicura** abilitata, la risposta dat e Tim è simile a questo esempio:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Scenari avanzati

### <a name="change-language-headers"></a>Modificare le intestazioni della lingua

Quando ci si connette a SAP da app per la logica, la lingua predefinita per la connessione è l'inglese. È possibile impostare la lingua per la connessione utilizzando [l'intestazione `Accept-Language` http standard](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) con le richieste in ingresso.

> [!TIP]
> La maggior parte dei Web browser aggiunge un' `Accept-Language` intestazione in base alle impostazioni dell'utente. Il Web browser applica questa intestazione quando si crea una nuova connessione SAP nella finestra di progettazione delle app per la logica. Se non si vuole creare connessioni SAP nella lingua preferita del browser Web, aggiornare le impostazioni del browser Web per usare la lingua preferita oppure creare la connessione SAP usando Azure Resource Manager invece della finestra di progettazione di app per la logica. 

Ad esempio, è possibile inviare una richiesta con l' `Accept-Language` intestazione all'app per la logica usando il trigger della **richiesta http** . Tutte le azioni nell'app per la logica ricevono l'intestazione. Quindi, SAP usa le lingue specificate nei messaggi di sistema, ad esempio i messaggi di errore di Bova.

I parametri di connessione SAP per un'app per la logica non hanno una proprietà Language. Se quindi si usa l' `Accept-Language` intestazione, è possibile che venga ricevuto l'errore seguente: **verificare le informazioni sull'account e/o le autorizzazioni e riprovare.** In questo caso, controllare invece i log degli errori del componente SAP. L'errore si verifica in effetti nel componente SAP che usa l'intestazione, pertanto è possibile che venga ricevuto uno di questi messaggi di errore:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>Conferma transazione in modo esplicito

Quando si inviano transazioni a SAP da app per la logica, questo scambio si verifica in due passaggi, come descritto nel documento SAP relativo ai [programmi server RFC transazionali](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true). Per impostazione predefinita, l'azione **Invia a SAP** gestisce sia i passaggi per il trasferimento della funzione che la conferma della transazione in un'unica chiamata. Il connettore SAP offre la possibilità di separare questi passaggi. È possibile inviare un IDoc e, anziché confermare automaticamente la transazione, è possibile usare l'azione di **conferma dell'ID di transazione** esplicita.

Questa funzionalità di separazione della conferma dell'ID transazione è utile quando non si desidera duplicare le transazioni in SAP, ad esempio negli scenari in cui potrebbero verificarsi errori a causa di cause quali problemi di rete. Confermando separatamente l'ID della transazione, la transazione viene completata una sola volta nel sistema SAP.

Di seguito è riportato un esempio che illustra questo modello:

1. Creare un'app per la logica vuota e aggiungere un trigger HTTP.

1. Dal connettore SAP aggiungere l'azione **Invia IDOC** . Fornire i dettagli per il IDoc inviato al sistema SAP.

1. Per confermare in modo esplicito l'ID transazione in un passaggio separato, nel campo **conferma TID** selezionare **No**. Per il campo facoltativo **ID transazione GUID** è possibile specificare manualmente il valore o fare in modo che il connettore generi automaticamente e restituisca il GUID nella risposta dall'azione Invia IDOC.

   ![Invia Proprietà azione IDOC](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. Per confermare in modo esplicito l'ID transazione, aggiungere l'azione **conferma ID transazione** , assicurandosi di [evitare di inviare IDOCs duplicati a SAP](#avoid-sending-duplicate-idocs). Fare clic all'interno della casella **ID transazione** in modo che venga visualizzato l'elenco contenuto dinamico. Da tale elenco selezionare il valore **ID transazione** restituito dall'azione **Invia IDOC** .

   ![Azione conferma ID transazione](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   Dopo l'esecuzione di questo passaggio, la transazione corrente viene contrassegnata come completata a entrambe le estremità, sul lato del connettore SAP e sul lato del sistema SAP.

#### <a name="avoid-sending-duplicate-idocs"></a>Evitare di inviare IDocs duplicati

Se si verifica un problema con IDocs duplicati inviati a SAP dall'app per la logica, seguire questa procedura per creare una variabile stringa da usare come identificatore della transazione IDoc. La creazione di questo identificatore di transazione contribuisce a impedire trasmissioni di rete duplicate quando si verificano problemi quali interruzioni temporanee, problemi di rete o riconoscimenti persi.

> [!NOTE]
> I sistemi SAP dimenticano un identificatore di transazione dopo un periodo di tempo specificato o 24 ore per impostazione predefinita. Di conseguenza, SAP non riesce mai a confermare un identificatore di transazione se l'ID o il GUID è sconosciuto.
> Se la conferma per un identificatore di transazione ha esito negativo, questo errore indica che communcation con il sistema SAP non è riuscito prima che SAP fosse in grado di confermare la conferma.

1. Nella finestra di progettazione di app per la logica aggiungere la **variabile di inizializzazione** dell'azione all'app per la logica. 

1. Nell'editor per l'azione **Inizializza variabile** configurare le impostazioni seguenti. Salvare quindi le modifiche.

    1. Per **nome** immettere un nome per la variabile. Ad esempio: `IDOCtransferID`.

    1. Per **tipo** selezionare **stringa** come tipo di variabile.

    1. Per **valore**, selezionare la casella di testo **Immettere valore iniziale** per aprire il menu contenuto dinamico. Selezionare la scheda **espressioni** . Nell'elenco di funzioni immettere la funzione `guid()` . Selezionare quindi **OK** per salvare le modifiche. Il campo del **valore** è ora impostato sulla `guid()` funzione che genera un GUID.

1. Dopo l'azione **Inizializza variabile** , aggiungere l'azione **Send IDOC**.

1. Nell'editor per l'azione **Invia IDOC** configurare le impostazioni seguenti. Salvare quindi le modifiche.

    1. Per **tipo di IDOC** selezionare il tipo di messaggio e per **messaggio di input IDOC** specificare il messaggio.

    1. Per la **versione di rilascio di SAP** selezionare i valori della configurazione di SAP.

    1. Per la **versione dei tipi di record** selezionare i valori della configurazione di SAP.

    1. Per **Confirm TID** selezionare **No**.

    1. Selezionare **Aggiungi nuovo elenco parametri**  >  **ID transazione GUID**. Selezionare la casella di testo per aprire il menu contenuto dinamico. Nella scheda **variabili** selezionare il nome della variabile creata. Ad esempio: `IDOCtransferID`.

1. Nella barra del titolo dell'azione **Invia IDOC** selezionare **...**  >  **Impostazioni**. Per i **criteri di ripetizione dei tentativi**, è consigliabile selezionare il **valore predefinito** &gt; **completato**. Tuttavia, è possibile configurare un criterio personalizzato per esigenze specifiche. Per i criteri personalizzati, è consigliabile configurare almeno un tentativo per superare le interruzioni della rete temporanea.

1. Dopo l'azione **Invia IDOC**, aggiungere l'azione **conferma ID transazione**.

1. Nell'editor per l'azione **conferma ID transazione** configurare le impostazioni seguenti. Salvare quindi le modifiche.

    1. Per **ID transazione**, immettere di nuovo il nome della variabile. Ad esempio: `IDOCtransferID`.

1. Facoltativamente, convalidare la deduplicazione nell'ambiente di test. Ripetere l'azione **Invia IDOC** con lo stesso GUID **ID transazione** utilizzato nel passaggio precedente. Quando si invia lo stesso IDoc due volte, è possibile verificare che SAP sia in grado di identificare la duplicazione della chiamata tRFC e risolvere le due chiamate a un singolo messaggio IDoc in ingresso.

## <a name="known-issues-and-limitations"></a>Limitazioni e problemi noti

Di seguito sono riportati i problemi e le limitazioni attualmente noti per il connettore SAP gestito (non ISE): 

* In generale, il trigger SAP non supporta i cluster di gateway dati. In alcuni casi di failover, il nodo del gateway dati che comunica con il sistema SAP potrebbe essere diverso dal nodo attivo, il che comporta un comportamento imprevisto.

  * Per gli scenari di invio, sono supportati i cluster di gateway dati in modalità di failover. 

  * I cluster di gateway dati in modalità di bilanciamento del carico non sono supportati da azioni SAP con stato. Queste azioni includono la creazione di una **sessione con stato**, la transazione di commit di una **transazione**, il **rollback della transazione**, la chiusura della sessione con **stato** e tutte le azioni che specificano un valore di **ID di sessione** . Le comunicazioni con stato devono rimanere nello stesso nodo del cluster del gateway dati. 

  * Per le azioni SAP con stato, usare il gateway dati in modalità non cluster o in un cluster configurato solo per il failover.

* Il connettore SAP attualmente non supporta le stringhe di route SAP. Il gateway dati locale deve esistere nella stessa rete LAN del sistema SAP a cui si desidera connettersi.


## <a name="connector-reference"></a>Informazioni di riferimento sui connettori

Per informazioni più tecniche su questo connettore, ad esempio trigger, azioni e limiti, come descritto dal file spavalderia del connettore, vedere la [pagina di riferimento del connettore](/connectors/sap/). Per le azioni seguenti viene fornita la documentazione aggiuntiva per le app per la logica:

* [Chiamare BAPI](#call-bapi-action)

* [Invia IDOC](#send-idoc-action)

> [!NOTE]
> Per le app per la logica in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), la versione con etichetta ISE del connettore usa invece i [limiti dei messaggi ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

### <a name="call-bapi-action"></a>Chiama azione Bova

L'azione [chiama Bruni ( `CallBapi` )](
https://docs.microsoft.com/connectors/sap/#call-bapi-(preview)) chiama il metodo Bova sul server SAP. 

Con la chiamata è necessario usare i parametri seguenti: 

* **Business Object** ( `businessObject` ), un menu a discesa ricercabile.

* **Metodo** ( `method` ), che popola i metodi disponibili dopo aver selezionato un **oggetto business**. I metodi disponibili variano a seconda dell' **oggetto business** selezionato.

* **Parametri di input Bruni** ( `body` ), in cui è possibile chiamare il documento XML che contiene i valori dei parametri di input del metodo Bova per la chiamata o l'URI del BLOB di archiviazione che contiene i parametri di Bova.

Per esempi dettagliati su come usare l'azione chiama Bova, vedere gli [esempi XML delle richieste di Bova](#xml-samples-for-bapi-requests).

> [!TIP]
> Se si usa la finestra di progettazione di app per la logica per modificare la richiesta di Bova, è possibile usare le funzioni di ricerca seguenti: 
> 
> * Selezionare un oggetto nella finestra di progettazione per visualizzare un menu a discesa di metodi disponibili.
> * Filtrare i tipi di oggetti business per parola chiave usando l'elenco ricercabile fornito dalla chiamata all'API di Bova.

### <a name="send-idoc-action"></a>Invia azione IDoc

L'azione [Send IDOC ( `SendIDoc` )](/connectors/sap/) invia il messaggio IDOC al server SAP.

Con la chiamata è necessario usare i parametri seguenti: 

* **IDOC tipo con estensione facoltativa** ( `idocType` ), un menu a discesa ricercabile.

    * Il parametro facoltativo **versione di rilascio SAP** ( `releaseVersion` ) popola i valori dopo aver selezionato il tipo IDOC e dipende dal tipo IDOC selezionato.

* **Input IDOC Message** ( `body` ), in cui viene chiamato il documento XML contenente il payload IDOC o l'URI del BLOB di archiviazione che contiene il documento XML IDOC. Questo documento deve essere conforme al XML Schema SAP IDOC in base alla documentazione di WE60 IDoc o allo schema generato per l'URI dell'azione SAP IDoc corrispondente.

Per esempi dettagliati su come usare l'azione Invia IDoc, vedere la [procedura dettagliata per l'invio di messaggi IDOC al server SAP](#send-idoc-messages-to-sap-server).

Per informazioni su come usare il parametro facoltativo **Confirm TID** ( `confirmTid` ), vedere la [procedura dettagliata per confermare la transazione in modo esplicito](#confirm-transaction-explicitly).

## <a name="next-steps"></a>Passaggi successivi

* [Connettersi ai sistemi locali](../logic-apps/logic-apps-gateway-connection.md) da app per la logica di Azure.

* Informazioni su come convalidare, trasformare e usare altre operazioni dei messaggi con la [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).

* Informazioni su altri [connettori per app](../connectors/apis-list.md)per la logica.