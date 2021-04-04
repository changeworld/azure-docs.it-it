---
title: Creare o modificare un'offerta-Azure Marketplace
description: API per creare un nuovo o aggiornare un'offerta esistente.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/29/2020
ms.openlocfilehash: dba47073a6676b17c091af048f583ca3e7712999
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87420227"
---
# <a name="create-or-modify-an-offer"></a>Create o modificare un'offerta

> [!NOTE]
> Le API portale Cloud Partner sono integrate con e continueranno a funzionare nel centro per i partner. La transizione introduce piccole modifiche. Esaminare le modifiche elencate in [portale cloud partner riferimento API](./cloud-partner-portal-api-overview.md) per assicurarsi che il codice continui a funzionare dopo la transizione al centro per i partner. Le API CPP devono essere usate solo per i prodotti esistenti già integrati prima della transizione al centro per i partner; i nuovi prodotti devono usare le API di invio del centro per i partner.

Questa chiamata crea una nuova offerta o aggiorna una specifica offerta all'interno dello spazio dei nomi del server di pubblicazione.

  `PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parametri URI

|  **Nome**         |  **Descrizione**                      |  **Tipo di dati**  |
|  --------         |  ----------------                     |  -------------  |
| publisherId       |  Identificatore dell'editore, per esempio `contoso` |   string |
| offerId           |  Identificatore dell'offerta                     |   string        |
| api-version       |  Versione più recente dell'API            |   Data           |
|  |  |  |

## <a name="header"></a>Intestazione

|  **Nome**        |  **Valore**               |
|  ---------       |  ----------              | 
| Content-Type     | `application/json`       |
| Autorizzazione    | `Bearer YOUR_TOKEN`      |
|  |  |

## <a name="body-example"></a>Esempio di corpo

Il seguente esempio crea un'offerta con l'offerID di `contosovirtualmachine`.

### <a name="request"></a>Richiesta

``` json
  {
      "publisherId": "contoso",
      "offerTypeId": "microsoft-azure-virtualmachines",
      "id": "contosovirtualmachine",
      "offerTypeVersions": {
          "microsoft-azure-virtualmachines": 87,
          "microsoft-azure-marketplace": 39
      },
      "definition": {
          "displayText": "Contoso Virtual Machine Offer",
          "offer": {
          "microsoft-azure-marketplace.title": "Contoso App",
          "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
          "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
          "microsoft-azure-marketplace.allowedSubscriptions": ["59160c40-2e25-4dcf-a2fd-6514cb08bf08"],
          "microsoft-azure-marketplace.usefulLinks": [{
              "linkTitle": "Contoso App for Azure",
              "linkUrl": "https://azuremarketplace.microsoft.com"
          }],
          "microsoft-azure-marketplace.categoryMap": [
                {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                        "visualization-and-reporting"
                    ]
                },
                {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning":[
                        "bot-services",
                        "cognitive-services",
                        "other"
                    ]
                }
            ],
          "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
          "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.heroLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/c46ec74d-d214-4fb5-9082-3cee55200eba.png?sv=2014-02-14&sr=b&sig=RfDvjowFGpP4WZGAHylbF2CuXwO2NXOrwycrXEJvJI4%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
          "microsoft-azure-marketplace.screenshots": [],
          "microsoft-azure-marketplace.videos": [],
          "microsoft-azure-marketplace.leadDestination": "None",
          "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
          "microsoft-azure-marketplace.termsOfUse": "Terms of use",
          "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
          "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.supportContactName": "Jon Doe",
          "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
          "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
          "microsoft-azure-marketplace.publicAzureSupportUrl": "",
          "microsoft-azure-marketplace.fairfaxSupportUrl": ""
      },
      "plans": 
      [
          {
              "planId": "contososkuidentifier",
              "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
              "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
              "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
              "microsoft-azure-virtualmachines.cloudAvailability": ["PublicAzure"],
              "virtualMachinePricing": {
                  "isByol": true,
                  "freeTrialDurationInMonths": 0
              },
              "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
              "microsoft-azure-virtualmachines.windowsOSType": "Other",
              "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
              "microsoft-azure-virtualmachines.recommendedVMSizes": ["a0-basic", "a0-standard", "a1-basic", "a1-standard", "a2-basic", "a2-standard"],
              "microsoft-azure-virtualmachines.openPorts": [],
              "microsoft-azure-virtualmachines.vmImages": 
              {
                  "1.0.1": 
                  {
                      "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                      "lunVhdDetails": []
                  }
              },
              "regions": ["AZ"]
          }
          ]
      },
      "eTag": "W/\"datetime'2017-06-07T06%3A15%3A40.4771399Z'\"",
      "version": 5
  }
```

### <a name="response"></a>Risposta

``` json
 {
         "offerTypeId": "microsoft-azure-virtualmachines",
         "publisherId": "contoso",
         "status": "neverPublished",
         "id": "contosovirtualmachine",
         "version": 1,
         "definition": {
         "displayText": "Contoso Virtual Machine Offer",
         "offer": 
         {
             "microsoft-azure-marketplace-testdrive.videos": [],
             "microsoft-azure-marketplace.title": "Contoso App",
             "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
             "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
             "microsoft-azure-marketplace.allowedSubscriptions": 
             [
                 "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
             ],
             "microsoft-azure-marketplace.usefulLinks": 
             [
                 {
                     "linkTitle": "Contoso App for Azure",
                     "linkUrl": "https://azuremarketplace.microsoft.com"
                 }
             ],
             "microsoft-azure-marketplace.categoryMap":
             [
                 {
                    "categoryL1": "analytics",
                    "categoryL2-analytics": [
                    "visualization-and-reporting"
                    ]
                 },
                 {
                    "categoryL1": "ai-plus-machine-learning",
                    "categoryL2-ai-plus-machine-learning": [
                    "bot-services",
                    "cognitive-services",
                    "other"
                    ]
                 }
             ],
             "microsoft-azure-marketplace.smallLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/8affcd28-60a5-4839-adf8-c560e069fd61.png?sv=2014-02-14&sr=b&sig=nGErAgn%2BDUecrX892wcmk32kh0MHgIZeJ5jcKyY%2Fuew%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.mediumLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/39550bca-1110-432c-9ea9-e12b3a2288cd.png?sv=2014-02-14&sr=b&sig=4X0hlkXYtuZOmcYq%2BsbYVZz3k5k26kngcFX6yBAJjNI%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.largeLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/ce3576e3-df12-4074-b0a3-0b8d3f329df1.png?sv=2014-02-14&sr=b&sig=mFhtCUQh%2FbFz10nlIWbqsz6jq5MBZ0M%2F5cIREE9P6V0%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.wideLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/476d6edd-12d3-4414-9def-d2970c4a9de4.png?sv=2014-02-14&sr=b&sig=pg4MDSZjAb8w8D%2FrQ9RT%2BodpynSy%2FlYOvpx0yeam2Bw%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.heroLogo": "https://publishingstoredm.blob.core.windows.net/prodcontent/D6191_publishers_marketplace:2Dtest/testaoffer/46c85b7b-4438-4e0d-8218-24fb5651727a.png?sv=2014-02-14&sr=b&sig=wIsCOO5%2BDj8NsLVSwwzwTgogF71oA7Q1XjKhNB1ni5g%3D&se=2020-03-28T22%3A27%3A13Z&sp=r",
             "microsoft-azure-marketplace.screenshots": [],
             "microsoft-azure-marketplace.videos": [],
             "microsoft-azure-marketplace.leadDestination": "None",
             "microsoft-azure-marketplace.tableLeadConfiguration": {},
             "microsoft-azure-marketplace.blobLeadConfiguration": {},
             "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
             "microsoft-azure-marketplace.crmLeadConfiguration": {},
             "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
             "microsoft-azure-marketplace.marketoLeadConfiguration": {},
             "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
             "microsoft-azure-marketplace.termsOfUse": "Terms of use",
             "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
             "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.supportContactName": "Jon Doe",
             "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
             "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
             "microsoft-azure-marketplace.publicAzureSupportUrl": "",
             "microsoft-azure-marketplace.fairfaxSupportUrl": ""
         },
         "plans": 
         [
             {
                 "planId": "contososkuidentifier",
                 "microsoft-azure-virtualmachines.skuTitle": "Contoso App (Old Title)",
                 "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                 "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                 "microsoft-azure-virtualmachines.cloudAvailability": 
                 [
                     "PublicAzure"
                 ],
                 "microsoft-azure-virtualmachines.certificationsFairfax": [],
                 "virtualMachinePricing": {
                     "isByol": true,
                     "freeTrialDurationInMonths": 0
                 },
                 "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                 "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                 "microsoft-azure-virtualmachines.recommendedVMSizes": 
                 [
                     "a0-basic",
                     "a0-standard",
                     "a1-basic",
                     "a1-standard",
                     "a2-basic",
                     "a2-standard"
                 ],
                 "microsoft-azure-virtualmachines.openPorts": [],
                 "microsoft-azure-virtualmachines.vmImages": 
                 {
                     "1.0.1": 
                     {
                         "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sr=c&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                         "lunVhdDetails": []
                     }
                 },
                 "regions":
                 [
                     "AZ"
                 ]
             } 
         ]
     },
     "changedTime": "2018-03-28T22:27:13.8363879Z"
 }
```

> [!NOTE]
> Per modificare questa offerta, aggiungere un'intestazione **If-Match** impostata su * per la suddetta richiesta. Usare lo stesso corpo della richiesta come illustrato in precedenza, ma modificare i valori in base alle esigenze. 

### <a name="response-status-codes"></a>Codici di stato della risposta

| **Codice**  |  **Descrizione**                                                                            |
| --------  |  ---------------                                                                            |
|  200      | `OK`. La richiesta è stata elaborata con successo e l'offerta è stata modificata correttamente.           |
|  201      | `Created`. La richiesta è stata elaborata con successo e l'offerta è stata creata correttamente.   |
|  400      | `Bad/Malformed request`. Il corpo della risposta di errore può specificare altre informazioni.            |
|  403      | `Forbidden`. Il client non ha accesso allo spazio dei nomi richiesto.                     |
|  404      | `Not found`. L'entità a cui rimanda il client non esiste.                           |
|  412      | Il server non soddisfa uno dei presupposti che il richiedente ha specificato nella richiesta. Il client deve controllare il valore ETag inviato con la richiesta. |
|  |  |

## <a name="uploading-artifacts"></a>Caricamento di artefatti

Gli artefatti, come ad esempio immagini e loghi, devono essere condivisi, caricandoli in una posizione accessibile sul Web e, successivamente, includendo ognuno come un URI, nella richiesta PUT, come illustrato nell'esempio precedente. Il sistema rileverà che questi file non sono presenti nell'archiviazione di Azure Marketplace e scaricano i file nella risorsa di archiviazione.  Di conseguenza, si noterà che future richieste GET restituiranno un URL del servizio Azure Marketplace per questi file.

## <a name="categories-and-industries"></a>Categorie e settori

Quando si crea una nuova offerta, è necessario specificare una categoria per l'offerta nel Marketplace. Facoltativamente, per alcuni tipi di offerta è anche possibile specificare i settori. In base al tipo di offerta, fornire le categorie o i settori applicabili all'offerta usando valori di chiave specifici delle tabelle seguenti.

### <a name="azure-marketplace-categories"></a>Categorie di Azure Marketplace

Queste categorie e le rispettive chiavi sono applicabili per le app di Azure, le macchine virtuali, le macchine virtuali di base, i contenitori, le app contenitore, i moduli IoT Edge e i tipi di offerta SaaS. Gli elementi in corsivo in grassetto (ad esempio, ***Analytics***) sono categorie e gli elementi di testo standard (come data-Insights) sono sottocategorie sotto di essi. Usare i valori di chiave esatti, senza modificare la spaziatura o la maiuscola.

| Category | Chiavi SaaS | Chiavi di app Azure | Macchina virtuale, contenitori, app contenitore, modulo IoT Edge, chiavi della macchina virtuale core |
| --- | --- | --- | --- |
| ***Analisi** _ | _*_analisi_*_ | _*_analisi-Azure-Apps_*_ | _ *_Analytics-amp_** |
| Informazioni dettagliate sui dati | informazioni dettagliate sui dati | informazioni dettagliate sui dati | informazioni dettagliate sui dati |
| Analisi dei dati | analisi dei dati | analisi dei dati | analisi dei dati |
| Big Data | Big Data | bigData | Big Data |
| Analisi predittiva | analisi predittiva | analisi predittiva | analisi predittiva |
| Analisi di flusso e in tempo reale | streaming in tempo reale-analisi | streaming in tempo reale-analisi | streaming in tempo reale-analisi |
| Altro | altro | Other-Analytics | altro |
| ***Ai + Machine Learning** _ | _*_ArtificialIntelligence_*_ | _*_intelligenza artificiale Plus-Machine Learning_*_ | _ *_ai-Plus-Machine Learning_** |
| Servizi Bot | bot-servizi | bot-servizi | bot-servizi |
| Servizi cognitivi | Servizi cognitivi | Servizi cognitivi | Servizi cognitivi |
| Servizio ML | ml-servizio | ml-servizio | ml-servizio |
| Funzionalità automatiche di Machine Learning | automatizzato-ml | automatizzato-ml | automatizzato-ml |
| Automazione di processi aziendali/robotici | Business-robot-process-automazione | Business-robot-process-automazione | Business-robot-process-automazione |
| Assegnazione di etichette ai dati | assegnazione di etichette ai dati | assegnazione di etichette ai dati | assegnazione di etichette ai dati |
| Preparazione dei dati | preparazione dei dati | preparazione dei dati | preparazione dei dati |
| Knowledge mining | informazioni sul data mining | informazioni sul data mining | informazioni sul data mining |
| Operazioni ML | ml-operazioni | ml-operazioni | ml-operazioni |
| Altro | Other-intelligenza artificiale-Plus-Machine-Learning | altro | altro |
| ***Blockchain** _ | _*_blockchain_*_ | _*_blockchain_*_ | _ *_blockchain_** |
| Acceleratori di app | App-Accelerator | App-Accelerator | App-Accelerator |
| Ledger a nodo singolo | singolo nodo-Ledger | singolo nodo-Ledger | singolo nodo-Ledger |
| Ledger a più nodi | più nodi-Ledger | più nodi-Ledger | più nodi-Ledger |
| Strumenti | tools | tools | tools |
| Altro | altro | altro | altro |
| ***Calcolo** _ | _*_calcolo-Saas_*_ | _*_calcolo-Azure-Apps_*_ | _ *_calcolo_** |
| Infrastruttura applicativa | appInfra | appInfrastructure | applicazione-infrastruttura |
| Sistemi operativi | clientos | clientos | sistemi operativi |
| Cache | cache | cache | cache |
| Altro | altro-calcolo | altro-calcolo | altro |
| ***Contenitori** _ | _*_contenitori_*_ | _*_contenitori_*_ | _ *_contenitori_** |
| App contenitore | contenitore-app | contenitore-app | contenitore-app |
| Immagini contenitore | contenitori-immagini | contenitori-immagini | contenitori-immagini |
| Introduzione ai contenitori | Get-Started-with-containers | Get-Started-with-containers | Get-Started-with-containers |
| Altro | altro | altro | altro |
| ***Database** _ | _*_database-Saas_*_ | _*_database_*_ | _ *_database_** |
| Database NoSQL | NoSQL-database | NoSQL-database | NoSQL-database |
| Database relazionali | database relazionali | database relazionali | database relazionali |
| Database Ledger/blockchain | Ledger-blockchain-database | Ledger-blockchain-database | Ledger-blockchain-database |
| Data Lake | Data Lake | Data Lake | Data Lake |
| data warehouse | data warehouse | data warehouse | data warehouse |
| Altro | altri database | altri database | altro |
| ***Strumenti di sviluppo** _ | _*_Developer-Tools-Saas_*_ | _*_Developer-Tools-Azure-Apps_*_ | _ *_Developer-Tools_** |
| Strumenti | strumenti-Developer-Tools | strumenti-Developer-Tools | strumenti-Developer-Tools |
| Script | script | script | script |
| Servizio per sviluppatori | devService | devService | Developer-Service |
| Altro | altri strumenti per sviluppatori | altri strumenti per sviluppatori | altro |
| ***DevOps** _ | _*_DevOps_*_ | _*_DevOps_*_ | _ *_DevOps_** |
| Altro | altro | altro | altro |
| ***Identità** _ | _*_identità_*_ | _*_identità_*_ | _ *_identità_** |
| Gestione degli accessi | gestione degli accessi | gestione degli accessi | gestione degli accessi |
| Altro | altro | altro | altro |
| ***Integrazione** _ | _*_integrazione_*_ | _*_integrazione_*_ | _ *_integrazione_** |
| Messaggistica | messaggi | messaggi | messaggi |
| Altro | altro | altro | altro |
| ***Internet delle cose** _ | _*_IoT_*_ | _*_Internet-of-Things-Azure-Apps_*_ | _ *_Internet-of-Things_** |
| Servizi di base di Internet delle cose | N/D | Servizi Internet di base | Servizi Internet di base |
| Moduli IoT Edge | N/D | tutti i moduli Edge | tutti i moduli Edge |
| Soluzioni Internet delle cose | Internet delle cose-soluzioni | Internet delle cose-soluzioni | Internet delle cose-soluzioni |
| Visualizzazione & analisi dei dati | Data-Analytics-and-Visualization | Data-Analytics-and-Visualization | Data-Analytics-and-Visualization |
| Connettività Internet | connettività | connettività | connettività |
| Altro | Other-Internet-of-Things | Other-Internet-of-Things | altro |
| ***Strumenti di gestione &** _ | _*_ITandAdministration_*_ | _*_it-and-Management-Tools-Azure-Apps_*_ | _ *_it-and-Management-Tools_** |
| Soluzioni di gestione | Gestione-soluzioni | Gestione-soluzioni | Gestione-soluzioni |
| Applicazioni aziendali | businessApplication | businessApplication | applicazioni aziendali |
| Altro | Other-it-Management-Tools | Other-it-Management-Tools | altro |
| ***Monitoraggio & diagnostica** _ | _*_monitoraggio e diagnostica_*_ | _*_monitoraggio e diagnostica_*_ | _ *_monitoraggio e diagnostica_** |
| Altro | altro | altro | altro |
| ***Media** _ | _*_media_*_ | _*_media_*_ | _ *_supporti_** |
| Servizi multimediali | media-services | media-services | media-services |
| Protezione del contenuto | content-protection | content-protection | content-protection |
| Streaming Live & on demand | streaming live e on demand | streaming live e on demand | streaming live e on demand |
| Altro | altro | altro | altro |
| ***Migrazione** _ | _*_migrazione_*_ | _*_migrazione_*_ | _ *_migrazione_** |
| Migrazione dei dati | migrazione dei dati | migrazione dei dati | migrazione dei dati |
| Altro | altro | altro | altro |
| ***Realtà mista** _ | _*_mixed-reality_*_ | _*_mixed-reality_*_ | _ in *_realtà mista_** |
| Altro | altro | altro | altro |
| ***Rete** _ | _*_rete_*_ | _*_rete_*_ | _ *_rete_** |
| Gestione Appliance | Appliance-Manager | Appliance-Manager | Appliance-Manager |
| Connettività | connettività | connettività | connettività |
| Firewall | firewall | firewall | firewall |
| Servizi di bilanciamento del carico | bilanciamento del carico | bilanciamento del carico | bilanciamento del carico |
| Altro | altro | altro | altro |
| ***Sicurezza** _ | _*_sicurezza_*_ | _*_sicurezza_*_ | _ *_sicurezza_** |
| Gestione dell'identità e dell'accesso | gestione delle identità e dell'accesso | gestione delle identità e dell'accesso | gestione delle identità e dell'accesso |
| Threat Protection | protezione dalle minacce | protezione dalle minacce | protezione dalle minacce |
| Information Protection | protezione delle informazioni | protezione delle informazioni | protezione delle informazioni |
| Altro | altro | altro | altro |
| ***Spazio di archiviazione** _ | _*_archiviazione-Saas_*_ | _*_archiviazione-Azure-Apps_*_ | _ *_archiviazione_** |
| Backup & ripristino | backup | backup | backup e ripristino |
| Archiviazione ibrida aziendale | Enterprise-Hybrid-Storage | Enterprise-Hybrid-Storage | Enterprise-Hybrid-Storage |
| Condivisione file | condivisione di file | condivisione di file | condivisione di file |
| Gestione del ciclo di vita dei dati | gestione del ciclo di vita dei dati | gestione del ciclo di vita dei dati | gestione del ciclo di vita dei dati |
| Altro | altro-archiviazione | altro-archiviazione | altro |
| ***Web** _ | _*_Web_*_ | _*_Web_*_ | _ *_Web_** |
| Blog & CMSs | Blog-e-CMSS | Blog-e-CMSS | Blog-e-CMSS |
| App Web di avvio | Starter-Web-App | Starter-Web-App | Starter-Web-App |
| Ecommerce | ecommerce | ecommerce | ecommerce |
| Framework per app Web | app Web-Framework | app Web-Framework | app Web-Framework |
| App Web | app Web | app Web | app Web |
| Altro | altro | altro | altro |
||||

### <a name="microsoft-appsource-categories"></a>Categorie di Microsoft AppSource

Queste categorie e le rispettive chiavi sono applicabili per i tipi di offerta SaaS, Power bi app, Dynamics 365 business Central, Dynamics 365 for Customer Engagement e Dynamics 365 for operation. Gli elementi in corsivo in grassetto, come ***Analytics***, sono categorie e gli elementi di testo standard, ad esempio Advanced-Analytics, sono sottocategorie sotto di essi. Usare i valori di chiave esatti, senza modificare la spaziatura o la maiuscola.

| Category | Chiavi SaaS | Dynamics 365 business Central, Dynamics 365 per Customer Engagement, Dynamics 365 per chiavi operative | Chiavi dell'app Power bi |
| --- | --- | --- | --- |
| ***Analisi** _ | _*_analisi_*_ | _*_Analisi_*_ | _ *_Analytics_** |
| Analisi avanzata | Advanced-Analytics | Advanced-Analytics | Advanced-Analytics |
| Visualizzazione & Reporting | visualizzazione-Creazione di report | visualizzazione-Creazione di report | visualizzazione-Creazione di report |
| Altro | altro | Other-Analytics | Other-Analytics |
| ***Ai + Machine Learning** _ | _*_ArtificialIntelligence_*_ | _*_intelligenza artificiale-Plus-Machine-Learning-Dynamics_*_ | _ *_intelligenza artificiale-Plus-Machine-Learning-AppSource_** |
| Intelligenza artificiale per le aziende | intelligenza artificiale per le aziende | intelligenza artificiale per le aziende | intelligenza artificiale per le aziende |
| App bot | bot-app | bot-app | bot-app |
| Altro | Other-intelligenza artificiale-Plus-Machine-Learning | Other-intelligenza artificiale-Plus-Machine-Learning | Other-intelligenza artificiale-Plus-Machine-Learning |
| ***Collaborazione** _ | _*_Collaborazione_*_ | _*_Collaborazione_*_ | _ *_collaborazione_** |
| Contattare & persone | contatti-persone | contatti-persone | contatti e persone |
| Gestione delle riunioni | gestione delle riunioni | gestione delle riunioni | gestione delle riunioni |
| Progettazione di siti & gestione | gestione del sito-progettazione | gestione del sito-progettazione | site-design-and-Management |
| Gestione dei progetti & attività | attività-Gestione progetti | attività-Gestione progetti | Gestione attività e progetto |
| Videoconferenza di Voice & video | video di conferenze vocali | video di conferenze vocali | comunicazione vocale e video |
| Altro | altra collaborazione | altra collaborazione | altro |
| ***Conformità & note legali** | _*_conformità_*_ | _*_conformità_*_ | _ *_conformità e-Legal_** |
| Controllo imposta & | controllo fiscale | controllo fiscale | Tax-and-audit |
| Legale | Legale | Legale | legal |
| Dati, governance & privacy | governance dei dati-privacy | governance dei dati-privacy | governance dei dati e privacy |
| Sicurezza & integrità | integrità-sicurezza | integrità-sicurezza | integrità e sicurezza |
| Altro | altro-conformità-legale | altro-conformità-legale | altro |
| ***Servizio clienti** _ | _*_CustomerService_*_ | _*_CustomerService_*_ | _ *_cliente-servizio_** |
| Centro contatti | Contact-Center | Contact-Center | Contact-Center |
| Servizio facet | servizio viso-a-faccia | servizio viso-a-faccia | servizio viso-a-faccia |
| Back Office & Employee Service | Back-Office-Employee-Service | Back-Office-Employee-Service | Back-Office-and-Employee-Service |
| Gestione case & Knowledge base | Knowledge base-gestione | Knowledge base-gestione | Knowledge base e-Case-Management |
| Social media & Engagement omnicanale | social-media-omnicanale-Engagement | social-media-omnicanale-Engagement | Social-Media-and-omnicanale-Engagement |
| Altro | altro-Customer-Service | altro-Customer-Service | altro |
| ***Finanza** _ | _*_Finance_*_ | _*_Finance_*_ | _ *_finanza_** |
| Contabilità | accounting | accounting | accounting |
| Asset Management (Gestione degli asset) | gestione delle risorse | gestione delle risorse | gestione delle risorse |
| Analisi, consolidamento & Reporting | analisi-consolidamento-creazione di report | analisi-consolidamento-creazione di report | analisi: consolidamento e creazione di report |
| Raccolte di & di credito | raccolte di crediti | raccolte di crediti | Credit-and-Collections |
| Gestione dei rischi di conformità & | conformità-gestione dei rischi | conformità-gestione dei rischi | conformità e gestione dei rischi |
| Altro | altro-finanza | altro-finanza | altro |
| ***Risorse umane** _ | _*_HumanResources_*_ | _*_HumanResources_*_ | _ *_risorse umane_** |
| Acquisizione del talento | talento-acquisizione | talento-acquisizione | talento-acquisizione |
| Gestione dei talent | gestione del talento | gestione del talento | gestione del talento |
| Operazioni HR | HR-operazioni | HR-operazioni | HR-operazioni |
| Pianificazione della forza lavoro & analisi | forza lavoro-Pianificazione-analisi | forza lavoro-Pianificazione-analisi | forza lavoro-pianificazione e analisi |
| Altro | altre risorse umane | altre risorse umane | altro |
| ***Internet delle cose** _ | _*_IoT_*_ | _*_Internet-of-Things-Dynamics_*_ | _ *_Internet-of-Things-AppSource_** |
| Operazioni di & di gestione delle risorse | Asset-Management-operazioni | Asset-Management-operazioni | Asset-Management-and-Operations |
| Prodotti connessi | connessi-prodotti | connessi-prodotti | connessi-prodotti |
| Supply Chain intelligente | Supply Chain intelligente | Supply Chain intelligente | Supply Chain intelligente |
| Manutenzione predittiva | manutenzione predittiva | manutenzione predittiva | manutenzione predittiva |
| Monitoraggio remoto | monitoraggio remoto | monitoraggio remoto | monitoraggio remoto |
| Sicurezza & sicurezza | sicurezza-sicurezza | sicurezza-sicurezza | sicurezza e sicurezza |
| Risorse & di infrastruttura intelligenti | risorse intelligenti-infrastruttura | risorse intelligenti-infrastruttura | infrastruttura e risorse intelligenti |
| Veicoli & mobilità | veicoli-mobilità | veicoli-mobilità | veicoli e mobilità |
| Altro | Other-Internet-of-Things | Other-Internet-of-Things | altro |
| ***Strumenti di gestione &** _ | _*_ITandAdministration_*_ | _*_ITandAdministration_*_ | _ *_it-and-Management-Tools_** |
| Soluzioni di gestione | Gestione-soluzioni | Gestione-soluzioni | Gestione-soluzioni |
| Applicazioni aziendali | businessApplication | businessApplication | applicazioni aziendali |
| Altro | Other-it-Management-Tools | Other-it-Management-Tools | altro |
| ***Marketing** _ | _*_Marketing_*_ | _*_Marketing_*_ | _ *_Marketing_** |
| Annuncio pubblicitario | annuncio | annuncio | annuncio |
| Analisi | analisi-marketing | analisi-marketing | analisi-marketing |
| Gestione delle campagne & automazione | gestione della campagna-automazione | gestione della campagna-automazione | campagna-gestione e automazione |
| Marketing tramite posta elettronica | posta elettronica-marketing | posta elettronica-marketing | posta elettronica-marketing |
| L2-eventi & gestione delle risorse | Eventi-gestione delle risorse | Eventi-gestione delle risorse | gestione eventi e risorse |
| Analisi del & di ricerca | ricerca-analisi | ricerca-analisi | ricerca e analisi |
| Social media | social media | social media | social media |
| Altro | altro-marketing | altro-marketing | altro |
| ***Operazioni & Supply Chain** _ | _*_OperationsSupplyChain_*_ | _*_OperationsSupplyChain_*_ | _ *_operazioni-e-Supply-Chain_** |
| Gestione della produzione di asset & | Asset-produzione-gestione | Asset-produzione-gestione | Gestione asset e produzione |
| Previsione della domanda | previsione della domanda | previsione della domanda | previsione della domanda |
| Connettività & Information Management | gestione delle informazioni-connettività | gestione delle informazioni-connettività | Information-Management-and-Connectivity |
| Pianificazione, acquisto & Reporting | pianificazione-acquisto-creazione di report | pianificazione-acquisto-creazione di report | pianificazione-acquisto e creazione di report |
| Gestione dei servizi di & qualità | qualità-gestione dei servizi | qualità-gestione dei servizi | gestione di qualità e servizio |
| Gestione ordini di vendita & | gestione degli ordini di vendita | gestione degli ordini di vendita | gestione delle vendite e degli ordini |
| Gestione del trasporto & warehouse | trasporto-gestione warehouse | trasporto-gestione warehouse | Gestione trasporto e warehouse |
| Altro | Other-Operations-Supply-Chain | Other-Operations-Supply-Chain | altro |
| ***Produttività** _ | _*_Produttività_*_ | _*_Produttività_*_ | _ *_produttività_** |
| Gestione & di creazione di contenuti | gestione della creazione di contenuti | gestione della creazione di contenuti | Content-Creation-and-Management |
| Traduzione & lingua | Traduzione lingua | Traduzione lingua | lingua e traduzione |
| Gestione documentale | gestione dei documenti | gestione dei documenti | gestione dei documenti |
| Gestione della posta elettronica | gestione della posta elettronica | gestione della posta elettronica | gestione della posta elettronica |
| Cerca & di riferimento | riferimento di ricerca | riferimento di ricerca | ricerca e riferimenti |
| Altro | altro-produttività | altro-produttività | altro |
| Gamification | Gamification | Gamification | Gamification |
| ***Vendite** _ | _*_Sales_*_ | _*_Sales_*_ | _ *_Vendite_** |
| TELESALES | TELESALES | TELESALES | TELESALES |
| Configure, price, quote (CPQ) | Configure-Price-quote | Configure-Price-quote | Configure-Price-quote |
| Gestione dei contratti | gestione dei contratti | gestione dei contratti | gestione dei contratti |
| CRM | crm | crm | crm |
| E-Commerce | e-commerce | e-commerce | e-commerce |
| Arricchimento dei dati aziendali | arricchimento dei dati aziendali | arricchimento dei dati aziendali | arricchimento dei dati aziendali |
| Abilitazione vendite | Abilitazione vendite | Abilitazione vendite | Abilitazione vendite |
| Altro | altre-vendite | altre-vendite | altre-vendite |
| ***Georilevazione** _ | _*_georilevazione_*_ | _*_georilevazione_*_ | _ *_georilevazione_** |
| Mappe | maps | maps | maps |
| Notizie & Meteo | Notizie e meteo | Notizie e meteo | Notizie e meteo |
| Altro | altro-georilevazione | altro-georilevazione | altro-georilevazione |
||||

### <a name="microsoft-appsource-industries"></a>Settori Microsoft AppSource

Questi settori e le rispettive chiavi sono applicabili per i tipi di offerta SaaS, Power bi app, Dynamics 365 business Central, Dynamics 365 for Customer Engagement e Dynamics 365 for operation. Gli elementi in grassetto corsivo (ad esempio ***Automotive***) sono categorie e gli elementi di testo standard (ad esempio AutomotiveL2) sono sottocategorie sotto di essi. Usare i valori di chiave esatti, senza modificare la spaziatura o la maiuscola.

| Settore | SaaS, Dynamics 365 business Central, Dynamics 365 per Customer Engagement, Dynamics 365 per chiavi operative | Chiavi app Power bi |
| --- | --- | --- |
| ***Automotive** _ | _*_Automobilistico_*_ | _ *_Automotive_** |
| Automobilistico | AutomotiveL2 | AutomotiveL2 |
| ***Agricoltura** _ | _*_Agricoltura_*_ | _ *_agricoltura_** |
| Altro-non segmentato | \_OtherUnsegmented agricoltura | altro-non segmentato |
| ***Distribuzione** _ | _*_Distribuzione_*_ | _ *_distribuzione_** |
| Vendita all'ingrosso | Vendita all'ingrosso | Vendita all'ingrosso |
| Spedizione pacchetti & pacco | ParcelAndPackageShipping | distribuzione di pacchetti |
| ***Istruzione** _ | _*_Education_*_ | _ *_istruzione_** |
| Istruzione superiore | HigherEducation | educazione superiore |
| Primario & istruzione secondaria/K-12 | PrimaryAndSecondaryEducationK12 | educazione primaria e secondaria |
| Librerie & musei | LibrariesAndMuseums | librerie e musei |
| ***Servizi finanziari** _ | _*_FinancialServices_*_ | _ *_Financial-Services_** |
| Settore bancario & mercati finanziari | BankingAndCapitalMarkets | Banking-and-Capital-Markets |
| Assicurazioni | Assicurazioni | insurance |
| ***Governo** _ | _*_Enti governativi_*_ | _ *_Government_** |
| Difesa & Intelligence | DefenseAndIntelligence | difesa e intelligence |
| Sicurezza pubblica & giustizia | PublicSafetyAndJustice | Public-Safety-and-Justice |
| Governo civile | CivilianGovernment | civili pubblici |
| ***Assistenza sanitaria** _ | _*_HealthCareandLifeSciences_*_ | _ *_Healthcare_** |
| Pagante di integrità | HealthPayor | integrità-pagante |
| Provider di integrità | Jimmy | Health-provider |
| Prodotti farmaceutici | Prodotti farmaceutici | pharmaceuticals |
| ***Produzione & risorse** _ | _*_Produzione_*_ | _ *_produzione e risorse_** |
| & chimico agrochimica | ChemicalAndAgrochemical | Chemical-and-agrochimica |
| Produzione discreta | DiscreteManufacturing | discreto-produzione |
| Energia | Energia | energy |
| ***Vendita al dettaglio & beni di consumo** _ | _*_RetailandConsumerGoods_*_ | _ *_Retail-and-Consumer-Goods_** |
| Beni di consumo | ConsumerGoods | beni di consumo |
| Rivenditori | Rivenditori | rivenditori |
| ***Media & Communications** _ | _*_MediaAndCommunications_*_ | _ *_media e comunicazione_** |
| Media & Entertainment | MediaandEntertainment | Media-and-Entertainment |
| Telecomunicazioni | Telecomunicazioni | telecomunicazioni |
| ***Servizi professionali** _ | _*_ProfessionalServices_*_ | _ *_Professional-Services_** |
| Legale | Legale | legal |
| Servizi professionali per i partner | PartnerProfessionalServices | partner-Professional-Services |
| ***Architettura & costruzione** _ | _*_ArchitectureAndConstruction_*_ | _ *_architettura e costruzione_** |
| Altro-non segmentato | \_OtherUnsegmented ArchitectureAndConstruction | altro-non segmentato |
| ***Hospitality & Travel** _ | _*_HospitalityandTravel_*_ | _ *_Hospitality-and-Travel_** |
|    Alberghi & Leisure | HotelsAndLeisure | Alberghi-e-Leisure |
| Travel & Transportation | TravelAndTransportation | Viaggi e trasporti |
| Ristoranti & servizi alimentari | RestaurantsAndFoodServices | ristoranti e cibo-servizi |
| ***Altri settori pubblici** _ | _*_OtherPublicSectorIndustries_*_ | _ *_altro-pubblico-settore-settori_** |
| Selvicoltura & pesca | ForestryAndFishing | silvicoltura e pesca |
| Organizzazioni no profit | Organizzazioni no profit | organizzazioni no profit |
| ***Proprietà immobiliari** _ | _*_RealEstate_*_ | _ *_Real-estate_** |
| Altro-non segmentato | \_OtherUnsegmented immobiliare | altro-non segmentato |
|||
