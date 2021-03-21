---
title: Horizon SDK
titleSuffix: Azure Defender for IoT
description: Horizon SDK consente ad Azure Defender per gli sviluppatori di software di dissettore che decodificano il traffico di rete in modo che possa essere elaborato da Defender automatico per i programmi di analisi della rete.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/13/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 53aafc4146680c89dd01174ec5fde765f1cc0c01
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523363"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Dissettore del protocollo proprietario Horizon

Horizon è un ambiente di sviluppo aperto (ODE) usato per proteggere gli Internet e i dispositivi ICS che eseguono protocolli proprietari.

Questo ambiente fornisce le seguenti soluzioni per i clienti e i partner tecnologici:

- Supporto illimitato e completo per protocolli o protocolli personalizzati comuni che si scostano da qualsiasi standard. 

- Un nuovo livello di flessibilità e ambito per lo sviluppo DPI.

- Strumento che espande in modo esponenziale la visibilità e il controllo, senza dover aggiornare Defender per le versioni della piattaforma Internet.

- Sicurezza che consente lo sviluppo proprietario senza divulgare informazioni riservate.

Horizon SDK consente ad Azure Defender per gli sviluppatori di software di dissettore che decodificano il traffico di rete in modo che possa essere elaborato da Defender automatico per i programmi di analisi della rete.

I dissettori di protocollo sono sviluppati come plug-in esterni e sono integrati con una vasta gamma di Defender per i servizi Internet. Ad esempio, servizi che forniscono funzionalità di monitoraggio, avviso e creazione di report.

## <a name="secure-development-environment"></a>Ambiente di sviluppo sicuro 

L'ODE Horizon consente lo sviluppo di protocolli personalizzati o proprietari che non possono essere condivisi all'esterno dell'organizzazione. Ad esempio, a causa delle normative legali o dei criteri aziendali.

Sviluppare plug-in di dissettore senza: 

- sono state rivelate informazioni proprietarie sulla modalità di definizione dei protocolli.

- condivisione di PCAPs sensibili.

- violazione delle normative di conformità.

## <a name="customization-and-localization"></a>Personalizzazione e localizzazione  

L'SDK supporta varie opzioni di personalizzazione, tra cui:

  - Testo per i codici di funzione. 

  - Testo di localizzazione completo per gli avvisi, gli eventi e i parametri del protocollo. Per ulteriori informazioni, vedere [create mapping files (JSON)](#create-mapping-files-json).

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Visualizza gli avvisi completamente localizzati.":::

## <a name="horizon-architecture"></a>Architettura Horizon

Il modello di architettura include tre livelli di prodotti.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Defender per il livello piattaforma Internet

Consente l'integrazione immediata e il monitoraggio in tempo reale di plug-in di dissettori personalizzati nel Defender per la piattaforma Internet, senza la necessità di aggiornare la versione di Defender per la piattaforma Internet.

## <a name="defender-for-iot-services-layer"></a>Livello di Defender per i servizi Internet

Ogni servizio è progettato come una pipeline, separato da un protocollo specifico, permettendo uno sviluppo più efficiente e indipendente.

Ogni servizio è progettato come una pipeline, separato da un protocollo specifico. Il servizio è in ascolto del traffico sulla pipeline. Interagiscono con i dati del plug-in e il traffico acquisito dai sensori per indicizzare i protocolli distribuiti e analizzare il payload del traffico e consentire uno sviluppo più efficiente e indipendente.

## <a name="custom-dissector-layer"></a>Livello di dissettore personalizzato 

Consente la creazione di plug-in usando il Defender per il file SDK proprietario (inclusa l'implementazione C++ e la configurazione JSON) per: 

- Definire come identificare il protocollo

- Definire la modalità di mapping dei campi che si desidera estrarre dal traffico ed estrarli 

- Definire le modalità di integrazione con Defender per i servizi Internet

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="Livelli predefiniti.":::

Defender for Internet è una funzionalità di base per i protocolli comuni. È possibile compilare le dissettoriali su questi protocolli.

## <a name="before-you-begin"></a>Prima di iniziare

## <a name="what-this-sdk-contains"></a>Contenuto dell'SDK 

Questo kit contiene i file di intestazione necessari per lo sviluppo. Il processo di sviluppo richiede passaggi di base e passaggi avanzati facoltativi, descritti in questo SDK.

<support@cyberx-labs.com>Per informazioni sulla ricezione di file di intestazione e altre risorse, contattare.

## <a name="about-the-environment-and-setup"></a>Informazioni sull'ambiente e sull'installazione 

### <a name="requirements"></a>Requisiti 

- L'ambiente di sviluppo preferito è Linux. Se si sviluppa in un ambiente Windows, è consigliabile usare una macchina virtuale con un sistema Linux.

- Per il processo di compilazione, usare GCC 7.4.0 o versione successiva. Usare qualsiasi classe standard di stdlib supportata in C++ 17.

- Defender per la versione 3,0 e successive.

### <a name="process"></a>Processo

1. [Scaricare](https://www.eclipse.org/) l'IDE di Eclipse per gli sviluppatori C/C++. È possibile usare qualsiasi altro IDE preferito. Questo documento illustra la configurazione con l'IDE di Eclipse.

1. Dopo l'avvio dell'IDE di Eclipse e la configurazione dell'area di lavoro (in cui verranno archiviati i progetti), premere **CTRL + n** e crearla come progetto C++.

1. Nella schermata successiva impostare il nome sul protocollo che si vuole sviluppare e selezionare il tipo di progetto come `Shared Library` e `AND Linux GCC` .

1. Modificare le proprietà del progetto, in **C/C++** impostazioni di compilazione impostazioni  >    >  **dello strumento**  >  **gcc C++ compilatore**  >  **varie**  >  **position codice indipendente**.

1. Incollare i codici di esempio ricevuti con l'SDK e compilarli.

1. Aggiungere gli elementi (libreria, config.jse metadati) a un file tar. gz e modificare l'estensione del file in \<XXX> . HDP, dove è \<XXX> il nome del plug-in.

### <a name="research"></a>Ricerca 

Prima di iniziare, verificare che:

- Leggere la specifica del protocollo, se disponibile.

- Individuare i campi di protocollo che si intende estrarre.

- Hanno pianificato gli obiettivi di mapping.

## <a name="about-plugin-files"></a>Informazioni sui file del plug-in 

Tre file vengono definiti durante il processo di sviluppo.

### <a name="json-configuration-file-required"></a>File di configurazione JSON (obbligatorio) 

Questo file deve definire l'ID e le dichiarazioni di dissettore, le dipendenze, i requisiti di integrazione, i parametri di convalida e le definizioni di mapping per convertire i valori in nomi, numeri in testo. Per ulteriori informazioni, vedere i collegamenti seguenti:

- [Preparare il file di configurazione (JSON)](#prepare-the-configuration-file-json)

- [Preparare le convalide del codice di implementazione](#prepare-implementation-code-validations)

- [Estrai metadati del dispositivo](#extract-device-metadata)

- [Connettersi a un servizio di indicizzazione (baseline)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>Codice di implementazione: C++ (obbligatorio)

Il codice di implementazione (CPP) analizza il traffico non elaborato e ne esegue il mapping a valori quali servizi, classi e codici di funzione. Estrae i campi livello e ne esegue il mapping ai nomi di indice dai file di configurazione JSON. I campi da estrarre da CPP vengono definiti nel file di configurazione. per altre informazioni, vedere [preparare il codice di implementazione (C++)](#prepare-the-implementation-code-c).

### <a name="mapping-files-optional"></a>File di mapping (facoltativo)

È possibile personalizzare il testo di output del plug-in per soddisfare le esigenze dell'ambiente aziendale.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migrazione":::

È possibile definire e aggiornare i file di mapping per aggiornare il testo senza modificare il codice. Ogni file può eseguire il mapping di uno o più campi:

  - Mapping dei valori dei campi ai nomi, ad esempio 1: Reset, 2: Start, 3: stop.

  - Mapping del testo per supportare più lingue.

Per ulteriori informazioni, vedere [create mapping files (JSON)](#create-mapping-files-json).

## <a name="create-a-dissector-plugin-overview"></a>Creazione di un plug-in di dissettore (panoramica)

1. Esaminare la sezione [informazioni sull'ambiente e sull'installazione](#about-the-environment-and-setup) .

2.  [Preparare il codice di implementazione (C++)](#prepare-the-implementation-code-c). Copiare il **template.jsnel** file e modificarlo in base alle esigenze. Non modificare le chiavi. 

3. [Preparare il file di configurazione (JSON)](#prepare-the-configuration-file-json). Copiare il file **template. cpp** e implementare un metodo di override. Per ulteriori informazioni, vedere [Horizon::p rotocol:: BaseParser](#horizonprotocolbaseparser) per informazioni dettagliate.

4. [Preparare le convalide del codice di implementazione](#prepare-implementation-code-validations).

## <a name="prepare-the-implementation-code-c"></a>Preparare il codice di implementazione (C++)

Il file CPP è un parser responsabile di:

- Convalida dell'intestazione e del payload del pacchetto (ad esempio, la lunghezza dell'intestazione o la struttura del payload).

- Estrazione dei dati dall'intestazione e dal payload in campi definiti.

- Implementazione dei campi configurati estrazione dal file JSON.

### <a name="what-to-do"></a>Operazioni da eseguire

Copiare il file template **. cpp** e implementare un metodo di override. Per ulteriori informazioni, vedere [Horizon::p rotocol:: BaseParser](#horizonprotocolbaseparser).

### <a name="basic-c-template-sample"></a>Esempio di modello di base C++ 

Questa sezione fornisce il modello di protocollo di base, con le funzioni standard per un campione Defender per il protocollo Internet degli orizzonti.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>Descrizione modello di base C++  

Questa sezione include il modello di protocollo di base, con una descrizione delle funzioni standard per un campione Defender per il protocollo Internet delle cose. 

### <a name="include-pluginpluginh"></a>#include "plugin/plugin. h"

Definizione utilizzata dal plug-in. Il file di intestazione contiene tutti gli elementi necessari per completare lo sviluppo.

### <a name="horizonprotocolbaseparser"></a>Horizon::p rotocol:: BaseParser

Interfaccia di comunicazione tra l'infrastruttura Horizon e il livello di plug-in. Per ulteriori informazioni, vedere [architettura Horizon](#horizon-architecture) per una panoramica sui livelli.

ProcessLayer è il metodo utilizzato per elaborare i dati.

- Il primo parametro nel codice della funzione è l'utilità di elaborazione utilizzata per recuperare i dati elaborati in precedenza, creando nuovi campi e livelli.

- Il secondo parametro nel codice della funzione corrisponde ai dati correnti passati dal parser precedente.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

Usare per creare l'istanza del parser.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>Esempio di codice della funzione protocollo 

In questa sezione viene fornito un esempio di come vengono estratti il numero di codice (2 byte) e la lunghezza dei messaggi (4 byte).

Questa operazione viene eseguita in base all'oggetto specificato nel file di configurazione JSON, il che significa che se il protocollo è *poco semplice* e il sensore viene eseguito in un computer con un minimo di endian, viene convertito.

Viene inoltre creato un livello per archiviare i dati. Per creare nuovi campi, usare *fieldsManager* dell'utilità di elaborazione. Un campo può avere solo uno dei seguenti tipi: *stringa*, *numero*, *dati non elaborati*, *matrice* (di tipo specifico) o *complesso*. Questo livello può contenere un numero, un valore non elaborato o una stringa con ID.

Nell'esempio seguente vengono estratti i due campi seguenti:

- `function_code_number`

- `headerLength`

Viene creato un nuovo livello e il campo estratto viene copiato al suo interno.

Nell'esempio seguente viene descritta una funzione specifica, ovvero la logica principale implementata per l'elaborazione del plug-in.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>Campo JSON correlato 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="Campo JSON correlato.":::

## <a name="prepare-the-configuration-file-json"></a>Preparare il file di configurazione (JSON)

Horizon SDK usa il JavaScript Object Notation standard (JSON), un formato leggero per l'archiviazione e il trasporto di dati e non richiede linguaggi di scripting proprietari.

Questa sezione descrive le dichiarazioni di configurazione JSON minime, la struttura correlata e fornisce un file di configurazione di esempio che definisce un protocollo. Questo protocollo viene integrato automaticamente con il servizio di individuazione dei dispositivi.

## <a name="file-structure"></a>Struttura dei file

Nell'esempio seguente viene descritta la struttura di file.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="Esempio della struttura di file.":::

### <a name="what-to-do"></a>Operazioni da eseguire

Copiare il `config.json` file modello e modificarlo in base alle esigenze. Non modificare la chiave. Le chiavi sono contrassegnate in rosso nel [file di configurazione JSON di esempio](#sample-json-configuration-file). 

### <a name="file-naming-requirements"></a>Requisiti per la denominazione dei file 

Il file di configurazione JSON deve essere salvato come `config.json` .

### <a name="json-configuration-file-fields"></a>Campi del file di configurazione JSON

Questa sezione descrive i campi di configurazione JSON che verranno definiti. Non modificare le *etichette* dei campi.

### <a name="basic-parameters"></a>Parametri di base

In questa sezione vengono descritti i parametri di base.

| Etichetta parametro | Descrizione | Type |
|--|--|--|
| **ID** | Nome del protocollo. Eliminare l'impostazione predefinita e aggiungere il nome del protocollo come visualizzato. | string |
| **che** | Definisce la modalità di codifica dei dati multibyte. Usare solo il termine "Little" o "Big". Tratto dalla specifica del protocollo o dalla registrazione del traffico | string |
| **sanity_failure_codes** | Questi sono i codici restituiti dal parser quando si verifica un conflitto di integrità in relazione all'identità del codice. Vedere la pagina relativa alla convalida dei numeri Magic nella sezione C++. | string |
| **malformed_codes** | Si tratta di codici che sono stati identificati correttamente, ma viene rilevato un errore. Ad esempio, se la lunghezza del campo è troppo corta o lunga oppure un valore non è valido. | string |
| **dissect_as** | Matrice che definisce la posizione in cui deve arrivare il traffico del protocollo specifico. | TCP/UDP, porta e così via. |
| **campi** | Dichiarazione dei campi che verranno estratti dal traffico. Ogni campo ha il proprio ID (nome) e il tipo (numeric, String, RAW, array, Complex). Ad esempio, la [funzione](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) Field estratta nel file del parser di implementazione. I campi scritti nel file di configurazione sono gli unici che è possibile aggiungere al livello. |  |

### <a name="other-advanced-fields"></a>Altri campi avanzati 

In questa sezione vengono descritti gli altri campi.

| Etichetta parametro | Descrizione |
|-----------------|--------|
| **Consenti elenchi** | È possibile indicizzare i valori del protocollo e visualizzarli nei report di data mining. Questi report riflettono la linea di base di rete. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Esempio della vista data mining."::: <br /> Per ulteriori informazioni, vedere [la pagina relativa alla connessione a un servizio di indicizzazione (baseline)](#connect-to-an-indexing-service-baseline) . |
| **firmware** | È possibile estrarre le informazioni sul firmware, definire i valori di indice e attivare gli avvisi del firmware per il protocollo di plug-in. Per ulteriori informazioni, vedere [estrarre i dati del firmware](#extract-firmware-data) per informazioni dettagliate. |
| **value_mapping** | È possibile personalizzare il testo di output del plug-in per soddisfare le esigenze dell'ambiente aziendale definendo e aggiornando i file di mapping. Ad esempio, eseguire il mapping ai file di linguaggio. È possibile implementare facilmente le modifiche nel testo senza modificare o influisca sul codice. Per ulteriori informazioni, vedere [create mapping files (JSON)](#create-mapping-files-json) per i dettagli. |

## <a name="sample-json-configuration-file"></a>File di configurazione JSON di esempio

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>Preparare le convalide del codice di implementazione

In questa sezione viene descritta l'implementazione delle funzioni di convalida del codice C++ e viene fornito codice di esempio. Sono disponibili due livelli di convalida:

- Integrità.

- Codice non valido.

Non è necessario creare codice di convalida per compilare un plug-in funzionante. Se non si prepara il codice di convalida, è possibile esaminare i report di data mining del sensore come indicazione della riuscita dell'elaborazione.

I valori dei campi possono essere mappati al testo nei file di mapping e aggiornati senza interruzioni senza alcun effetto sull'elaborazione.

## <a name="sanity-code-validations"></a>Convalide del codice di integrità 

Ciò consente di verificare che il pacchetto trasmesso corrisponda ai parametri di convalida del protocollo, che consente di identificare il protocollo all'interno del traffico.

Usare, ad esempio, i primi 8 byte come *numero magico*. Se lo stato di integrità non riesce, viene restituita una risposta di errore di integrità.

Ad esempio:

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

Se sono stati distribuiti altri plug-in pertinenti, il pacchetto verrà convalidato in base a tali plug-in.

## <a name="malformed-code-validations"></a>Convalide di codice in formato non valido 

Le convalide non valide vengono usate dopo che il protocollo è stato convalidato in modo positivo.

Se si verifica un errore durante l'elaborazione dei pacchetti in base al protocollo, viene restituita una risposta di errore.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="codice con formato non valido":::

## <a name="c-sample-with-validations"></a>Esempio C++ con convalide

In base alla funzione, il processo viene eseguito, come illustrato nell'esempio riportato di seguito.

### <a name="function-20"></a>Funzione 20 

- Viene elaborato come firmware.

- I campi vengono letti in base alla funzione.

- I campi vengono aggiunti al livello.

### <a name="function-10"></a>Funzione 10 

- La funzione contiene un'altra funzione secondaria, che è un'operazione più specifica

- La sottofunzione viene letta e aggiunta al livello.

Al termine dell'operazione, l'elaborazione è terminata. Il valore restituito indica se il livello di dissettore è stato elaborato correttamente. In caso contrario, il livello diventa utilizzabile.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>Estrai metadati del dispositivo

È possibile estrarre i metadati seguenti negli asset:

  - `Is_distributed_control_system` : Indica se il protocollo fa parte del sistema di controllo distribuito. (esempio: il protocollo SCADA deve essere false)

  - `Has_protocol_address` : Indica se è presente un indirizzo del protocollo. indirizzo specifico per il protocollo corrente, ad esempio identificatore unità MODBUS

  - `Is_scada_protocol` -Indica se il protocollo è specifico per le reti OT

  - `Is_router_potential` : Indica se il protocollo viene utilizzato principalmente dai router. Ad esempio, LLDP, CDP o STP.

Per ottenere questo risultato, è necessario aggiornare il file di configurazione JSON utilizzando la proprietà Metadata.

## <a name="json-sample-with-metadata"></a>Esempio JSON con metadati 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>Estrai codice di programmazione 

Quando si verifica un evento di programmazione, è possibile estrarre il contenuto del codice. Il contenuto estratto consente di:

- Confrontare il contenuto del file di codice in diversi eventi di programmazione.

- Attiva un avviso sulla programmazione non autorizzata.  

- Attiva un evento per la ricezione del file di codice di programmazione.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="Log delle modifiche di programmazione.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="Per visualizzare la programmazione, fare clic sul pulsante.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="Avviso di programmazione del PLC non autorizzato.":::

Per ottenere questo risultato, è necessario aggiornare il file di configurazione JSON utilizzando la `code_extraction` Proprietà. 

### <a name="json-configuration-fields"></a>Campi di configurazione JSON 

Questa sezione descrive i campi di configurazione JSON. 

- **Metodo**

  Indica il modo in cui vengono ricevuti i file degli eventi di programmazione. 

  ALL (ogni azione di programmazione causerà la ricezione di tutti i file di codice anche se sono presenti file senza modifiche).

- **file_type**  

  Indica il tipo di contenuto del codice.  

  TESTO (ogni file di codice contiene informazioni testuali).

- **code_data_field**
  
  Indica il campo di implementazione da usare per fornire il contenuto del codice.

  Campo.

- **code_name_field**

  Indica il campo di implementazione da utilizzare per fornire il nome del file di codifica.

  Campo.

- **size_limit**

  Indica il limite delle dimensioni di ogni contenuto del file di codifica in byte, se un file di codice supera il limite impostato che verrà eliminato. Se questo campo non è specificato, il valore predefinito sarà 15 milioni 15 MB.

  Numero.

- **metadati**

  Indica informazioni aggiuntive per un file di codice.

  Matrice contenente oggetti con due proprietà:

    - Name (String): indica che la chiave dei metadati XSense supporta attualmente solo la chiave username.
 
    - valore (campo): indica il campo di implementazione da utilizzare per fornire i dati dei metadati.

## <a name="json-sample-with-programming-code"></a>Esempio JSON con codice di programmazione

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Avvisi di Horizon personalizzati

Il codice di funzione di alcuni protocolli potrebbe indicare un errore. Ad esempio, se il protocollo controlla un contenitore con un prodotto chimico specifico che deve essere archiviato sempre a una temperatura specifica. In questo caso, potrebbe essere presente un codice di funzione che indica un errore nel termometro. Se, ad esempio, il codice della funzione è 25, è possibile attivare un avviso nella console Web che indica che si è verificato un problema con il contenitore. In tal caso, è possibile definire avvisi di pacchetti profondi.

Aggiungere il parametro **Alerts** alla proprietà `config.json` del plug-in.

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>Campi di configurazione JSON

Questa sezione descrive i campi di configurazione JSON. 

| Nome campo | Descrizione | Valori possibili |
|--|--|--|
| **ID** | Rappresenta un singolo ID avviso. Deve essere univoco in questo contesto. | Valore numerico 0-10000 |
| **message** | Informazioni visualizzate all'utente. Questo campo consente di usare campi diversi. | Usare qualsiasi campo del protocollo o qualsiasi protocollo di livello inferiore. |
| **title** | Titolo dell'avviso |  |
| **expression** | Quando si desidera che questo avviso venga visualizzato. | Usare qualsiasi campo numerico trovato nei livelli inferiori o nel livello corrente.</br></br> Ogni campo deve essere wrapper con `{}` , per consentire all'SDK di rilevarlo come campo, gli operatori logici supportati sono</br> = =-Uguale a</br> <=-minore o uguale a</br> >=-maggiore o uguale a</br> >-più di</br> <-minore di</br> ~ =-Non uguale |

## <a name="more-about-expressions"></a>Ulteriori informazioni sulle espressioni

Ogni volta che Horizon SDK richiama l'espressione e diventa *true*, verrà generato un avviso nel sensore.

È possibile includere più espressioni nello stesso avviso. Ad esempio,

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

Questa espressione convalida il codice della funzione solo quando il pacchetto src IPv4 è 10.10.10.10. Che è una rappresentazione non elaborata dell'indirizzo IP nella rappresentazione numerica.

È possibile utilizzare `and` o per `or` connettere le espressioni.

## <a name="json-sample-custom-horizon-alerts"></a>Avvisi dell'orizzonte personalizzato di esempio JSON

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>Connettersi a un servizio di indicizzazione (baseline)

È possibile indicizzare i valori del protocollo e visualizzarli nei report di data mining.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Visualizzazione dell'opzione data mining.":::

Questi valori possono essere mappati in un secondo momento a testi specifici, ad esempio per eseguire il mapping di numeri come testi o per aggiungere informazioni in qualsiasi linguaggio.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migrazione":::

Per ulteriori informazioni, vedere [create mapping files (JSON)](#create-mapping-files-json) per i dettagli.

È anche possibile usare i valori dei protocolli analizzati in precedenza per estrarre informazioni aggiuntive.

Ad esempio, per il valore, che è basato su TCP, è possibile usare i valori del livello IPv4. Da questo livello è possibile estrarre valori come l'origine del pacchetto e la destinazione.

Per ottenere questo risultato, è necessario aggiornare il file di configurazione JSON utilizzando la `whitelists` Proprietà.

## <a name="allow-list-data-mining-fields"></a>Campi Consenti elenco (data mining)

Sono disponibili i campi Consenti elenco seguenti:

- Nome: nome usato per l'indicizzazione.

- alert_title: titolo breve e univoco che illustra l'evento.

- alert_text: informazioni aggiuntive

È possibile aggiungere più elenchi Consenti, consentendo la massima flessibilità nell'indicizzazione.

## <a name="json-sample-with-indexing"></a>Esempio JSON con indicizzazione 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>Estrai dati firmware

È possibile estrarre le informazioni sul firmware, definire i valori di indice e attivare gli avvisi del firmware per il protocollo di plug-in. Ad esempio,

- Estrarre il modello o la versione del firmware. Queste informazioni possono essere utilizzate ulteriormente per identificare CVEs.

- Attiva un avviso quando viene rilevata una nuova versione del firmware.

Per ottenere questo risultato, è necessario aggiornare il file di configurazione JSON usando la proprietà firmware.

## <a name="firmware-fields"></a>Campi del firmware

Questa sezione descrive i campi di configurazione del firmware JSON.

- **nome**
  
  Indica come viene presentato il campo nella console del sensore.

- **value**

 Indica il campo di implementazione da utilizzare per fornire i dati. 

- **firmware_index:**

  Selezionare un'opzione:  
  - **modello**: modello del dispositivo. Abilita il rilevamento dei CVEs.
  - **seriale**: numero di serie del dispositivo. Il numero di serie non è sempre disponibile per tutti i protocolli. Questo valore è univoco per ogni dispositivo.
  - **rack**: indica l'identificatore del rack, se il dispositivo fa parte di un rack.
  - **slot**: identificatore dello slot, se il dispositivo fa parte di un rack.  
  - **module_address**: usare per presentare una gerarchia se il modulo può essere visualizzato dietro un altro dispositivo. Applicabile in alternativa se una combinazione di slot rack, che è una presentazione più semplice.  
  - **firmware_version**: indica la versione del dispositivo. Abilita il rilevamento dei CVEs.
  - **alert_text**: indica il testo che descrive le deviazioni del firmware, ad esempio le modifiche della versione.  
  - **index_by**: indica i campi usati per identificare e indicizzare il dispositivo. Nell'esempio seguente il dispositivo viene identificato in base al relativo indirizzo IP. In alcuni protocolli è possibile utilizzare un indice più complesso. Ad esempio, se un altro dispositivo è connesso e si sa come estrarre il percorso interno. Ad esempio, l'ID di unità MODBUS, può essere usato come parte dell'indice, come una combinazione diversa di indirizzo IP e identificatore di unità.
  - **firmware_fields**: indica i campi che rappresentano i campi di metadati del dispositivo. In questo esempio vengono utilizzati gli elementi seguenti: Model, Revision e Name. Ogni protocollo può definire i propri dati del firmware.

## <a name="json-sample-with-firmware"></a>Esempio JSON con firmware 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>Estrai attributi del dispositivo 

È possibile migliorare il dispositivo con le informazioni disponibili nel dispositivo in inventario, data mining e altri report.

- Nome

- Tipo

- Vendor

- Sistema operativo

Per ottenere questo risultato, è necessario aggiornare il file di configurazione JSON usando la proprietà **Properties** . 

Questa operazione può essere eseguita dopo aver scritto il plug-in di base ed estrarre i campi obbligatori.

## <a name="properties-fields"></a>Campi delle proprietà

Questa sezione descrive i campi di configurazione delle proprietà JSON. 

**config_file** 

Contiene il nome file che definisce come elaborare ogni chiave nei `key` campi. Il file di configurazione deve essere in formato JSON ed essere incluso come parte della cartella del protocollo del plug-in.

Ogni chiave nel codice JSON definisce il set di azioni che devono essere eseguite quando si estrae la chiave da un pacchetto.

Ogni chiave può avere:

- **Dati pacchetto** : indica le proprietà che verranno aggiornate in base ai dati estratti dal pacchetto (il campo di implementazione usato per fornire tali dati).

- **Dati statici** : indica un set predefinito di `property-value` azioni che devono essere aggiornate.

Le proprietà che possono essere configurate in questo file sono: 

- **Nome** : indica il nome del dispositivo.

- **Tipo** : indica il tipo di dispositivo.

- **Fornitore** : indica il fornitore del dispositivo.

- **Description (Descrizione** ): indica il modello di firmware del dispositivo (priorità più bassa rispetto a "Model").

- **OperatingSystem** : indica il sistema operativo del dispositivo.

### <a name="fields"></a>Campi

| Campo | Descrizione |
|--|--|
| key | Indica la chiave. |
| Valore | Indica il campo di implementazione da utilizzare per fornire i dati. |
| is_static_key | Indica se il `key` campo è derivato come valore dal pacchetto o se è un valore predefinito. |

### <a name="working-with-static-keys-only"></a>Utilizzo solo di chiavi statiche

Se si utilizzano chiavi statiche, non è necessario configurare il `config.file` . È possibile configurare solo il file JSON.

## <a name="json-sample-with-properties"></a>Esempio JSON con proprietà 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>Creare file di mapping (JSON)

È possibile personalizzare il testo di output del plug-in in modo che soddisfi le esigenze dell'ambiente aziendale definendo e aggiornando i file di mapping. È possibile implementare facilmente le modifiche nel testo senza modificare o influisca sul codice. Ogni file può eseguire il mapping di uno o più campi.

- Mapping dei valori dei campi ai nomi, ad esempio 1: Reset, 2: Start, 3: stop.

- Mapping del testo per supportare più lingue.

È possibile definire due tipi di file di mapping.

  - [File di mapping semplice](#simple-mapping-file).

  - [File di mapping delle dipendenze](#dependency-mapping-file).

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="rete etere":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="Visualizzazione degli avvisi non gestiti.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="Elenco di violazioni di criteri note.":::

## <a name="file-naming-and-storage-requirements"></a>Requisiti per la denominazione e l'archiviazione dei file 

I file di mapping devono essere salvati nella cartella dei metadati.

Il nome del file deve corrispondere all'ID del file di configurazione JSON.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="Esempio di un file di configurazione JSON.":::

## <a name="simple-mapping-file"></a>File di mapping semplice 

Nell'esempio seguente viene presentato un file JSON di base come valore di chiave.

Quando si crea un elenco Consenti e contiene uno o più campi di cui è stato eseguito il mapping. Il valore verrà convertito da un numero, una stringa o qualsiasi tipo in un testo formattato presentato nel file di mapping.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>File di mapping delle dipendenze 

Per indicare che il file è un file di dipendenza, aggiungere la parola chiave `dependency` alla configurazione di mapping.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

Il file contiene un mapping tra il campo Dependency e il campo function. Ad esempio, tra la funzione e la funzione secondaria. La funzione Sub viene modificata in base alla funzione specificata.

Nell'elenco Consenti configurato in precedenza non è presente alcuna configurazione delle dipendenze, come illustrato di seguito.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

La dipendenza può essere basata su un valore specifico o su un campo. Nell'esempio seguente si basa su un campo. Se si basa su un valore, definire il valore di estrazione da leggere dal file di mapping.

Nell'esempio seguente, la dipendenza segue per lo stesso valore del campo.

Nella funzione Sub Five, ad esempio, il significato viene modificato in base alla funzione.

  - Se si tratta di una funzione di lettura, cinque significa leggere la memoria.

  - Se si tratta di una funzione Write, viene usato lo stesso valore per leggere da un file.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>File di esempio

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>Esempio JSON con mapping

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>Creare un pacchetto, caricare e monitorare il plug-in 

Questa sezione illustra come specificare i certificati client.

  - Assemblare il plug-in.

  - Caricare il plug-in.

  - Monitorare ed eseguire il debug del plug-in per valutare le prestazioni.

Per creare il pacchetto del plug-in:

1. Aggiungere l' **artefatto** (può essere, library, config.json o Metadata) a un `tar.gz` file.

1. Modificare l'estensione del file in \<XXX.hdp> , dove è \<XXX> il nome del plug-in.

Per accedere alla console di Horizon:

1.  Accedere all'interfaccia della riga di comando del sensore come amministratore, CyberX o utente del supporto.

2.  Nel file: `/var/cyberx/properties/horizon.properties` impostare la proprietà **UI. Enabled** su **true** ( `horizon.properties:ui.enabled=true` ).

3.  Accedere alla console del sensore.

4.  Selezionare l'opzione **Horizon** dal menu principale.

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="Selezionare l'opzione Horizon dal riquadro sinistro.":::

    Si apre la console di Horizon.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Visualizzazione della console di Horizon e di tutti i relativi plug-in.":::

## <a name="plugins-pane"></a>Riquadro plug-in

Il riquadro plugin elenca:

  - Plug-in dell'infrastruttura: i plug-in dell'infrastruttura installati per impostazione predefinita con Defender for Internet.

  - Plug-in dell'applicazione: plug-in dell'applicazione installati per impostazione predefinita con Defender per le cose e altri plug-in sviluppati da Defender per l'it o sviluppatori esterni.
    
Abilitare e disabilitare i plug-in che sono stati caricati usando l'interruttore.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="Interruttore CIP.":::

### <a name="uploading-a-plugin"></a>Caricamento di un plug-in

Dopo aver creato e creato il plug-in, è possibile caricarlo in Defender per il sensore Internet. Per ottenere una copertura completa della rete, è necessario caricare il plug-in per ogni sensore nell'organizzazione.

Per caricare:

1.  Accedere al sensore.


2. Selezionare **Carica**.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="Caricare i plug-in.":::

3. Passare al plug-in e trascinarlo nella finestra di dialogo del plug-in. Verificare che il prefisso sia `.hdp` . Il plug-in viene caricato.

## <a name="plugin-status-overview"></a>Panoramica dello stato del plug-in 

La finestra **Panoramica** della console di Horizon fornisce informazioni sul plug-in caricato e consente di disabilitarli e abilitarli.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="Panoramica della console di Horizon.":::

| Campo | Descrizione |
|--|--|
| Applicazione | Nome del plug-in caricato. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="Opzione on e off."::: | Attivare o **disattivare** il plug- **in** . Defender for Internet non gestisce il traffico del protocollo definito nel plug-in quando si disattiva il plug-in. |
| Tempo | Ora dell'ultima analisi dei dati. Aggiornato ogni 5 secondi. |
| PPS | Numero di pacchetti al secondo. |
| Larghezza di banda | Larghezza di banda media rilevata negli ultimi 5 secondi. |
| Formato non valido | Le convalide non valide vengono usate dopo che il protocollo è stato convalidato in modo positivo. Se si verifica un errore durante l'elaborazione dei pacchetti in base al protocollo, viene restituita una risposta di errore.   <br><br>Questa colonna indica il numero di errori di formato non valido negli ultimi 5 secondi. Per ulteriori informazioni, vedere [convalide di codice in formato](#malformed-code-validations) non valido per i dettagli. |
| Avvisi | I pacchetti corrispondono alla struttura e alla specifica, ma si verifica un comportamento imprevisto in base alla configurazione di avviso del plug-in. |
| Errors | Numero di pacchetti per i quali non è stato possibile convalidare il protocollo di base. Verifica che il pacchetto corrisponda alle definizioni del protocollo. Il numero visualizzato qui indica il numero di errori rilevati negli ultimi 5 secondi. Per ulteriori informazioni, vedere [convalide del codice di integrità](#sanity-code-validations) per i dettagli. |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="Icona di monitoraggio."::: | Esaminare i dettagli relativi a formato non valido e avvisi rilevati per il plug-in |

## <a name="plugin-details"></a>Dettagli sul plug-in

È possibile monitorare il comportamento dei plug-in in tempo reale analizzando il numero di messaggi non *validi* e gli *avvisi* rilevati per il plug-in. È disponibile un'opzione per bloccare la schermata ed esportare per un'ulteriore analisi

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="Schermata di monitoraggio SNMP.":::

Per eseguire il monitoraggio:

Nella Panoramica selezionare il pulsante monitoraggio per il plug-in.

Passaggi successivi

Configurare l' [API Horizon](references-horizon-api.md)
