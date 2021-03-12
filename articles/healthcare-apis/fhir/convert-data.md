---
title: Conversione dei dati per l'API di Azure per FHIR
description: Usare l'endpoint $convert-data e i modelli Customize-Converter per convertire i dati nell'API di Azure per FHIR.
services: healthcare-apis
author: ranvijaykumar
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 01/19/2021
ms.author: ranku
ms.openlocfilehash: 7518f5e2984029c087eec1e6697f3237410bda4b
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019232"
---
# <a name="how-to-convert-data-to-fhir-preview"></a>Come convertire i dati in FHIR (anteprima)

> [!IMPORTANT]
> Questa funzionalità è in anteprima pubblica, viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

L'endpoint personalizzato $convert-data nell'API di Azure per FHIR è destinato alla conversione dei dati da formati diversi a FHIR. Usa il motore del modello Liquid e i modelli del progetto [FHIR Converter](https://github.com/microsoft/FHIR-Converter) come modelli predefiniti. È possibile personalizzare questi modelli di conversione in base alle esigenze. Attualmente supporta la conversione da HL7v2 a FHIR.

## <a name="use-the-convert-data-endpoint"></a>Usare l'endpoint $convert-data

`https://<<FHIR service base URL>>/$convert-data`

$convert-data accetta una risorsa di [parametro](http://hl7.org/fhir/parameters.html) nel corpo della richiesta, come descritto di seguito:

**Risorsa parametro:**

| Nome parametro      | Descrizione | Valori accettati |
| ----------- | ----------- | ----------- |
| inputData      | Dati da convertire. | Valore valido del tipo di dati della stringa JSON|
| inputDataType   | Tipo di dati di input. | ```HL7v2``` |
| templateCollectionReference | Riferimento a una raccolta di modelli. Può trattarsi di un riferimento ai **modelli predefiniti** o di un'immagine modello personalizzata registrata con l'API di Azure per FHIR. Vedere di seguito per informazioni su come personalizzare i modelli, ospitarli in ACR e registrarsi all'API di Azure per FHIR.  | ```microsofthealth/fhirconverter:default```, \<RegistryServer\>/\<imageName\>@\<imageDigest\> |
| rootTemplate | Modello radice da utilizzare durante la trasformazione dei dati. | ```ADT_A01```, ```OML_O21```, ```ORU_R01```, ```VXU_V04``` |  

> [!WARNING]
> I modelli predefiniti consentono di iniziare rapidamente. Tuttavia, questi aggiornamenti possono essere aggiornati quando si aggiorna l'API di Azure per FHIR. Per ottenere un comportamento coerente per la conversione dei dati in diverse versioni dell'API di Azure per FHIR, è necessario ospitare una copia personalizzata dei modelli in un Container Registry di Azure, registrarli nell'API di Azure per FHIR e usare nelle chiamate API, come descritto in seguito.

**Richiesta di esempio:**

```json
{
    "resourceType": "Parameters",
    "parameter": [
        {
            "name": "inputData",
            "valueString": "MSH|^~\\&|SIMHOSP|SFAC|RAPP|RFAC|20200508131015||ADT^A01|517|T|2.3|||AL||44|ASCII\nEVN|A01|20200508131015|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|\nPID|1|3735064194^^^SIMULATOR MRN^MRN|3735064194^^^SIMULATOR MRN^MRN~2021051528^^^NHSNBR^NHSNMBR||Kinmonth^Joanna^Chelsea^^Ms^^CURRENT||19870624000000|F|||89 Transaction House^Handmaiden Street^Wembley^^FV75 4GJ^GBR^HOME||020 3614 5541^HOME|||||||||C^White - Other^^^||||||||\nPD1|||FAMILY PRACTICE^^12345|\nPV1|1|I|OtherWard^MainRoom^Bed 183^Simulated Hospital^^BED^Main Building^4|28b|||C005^Whittingham^Sylvia^^^Dr^^^DRNBR^PRSNL^^^ORGDR|||CAR|||||||||16094728916771313876^^^^visitid||||||||||||||||||||||ARRIVED|||20200508131015||"
        },
        {
            "name": "inputDataType",
            "valueString": "Hl7v2"
        },
        {
            "name": "templateCollectionReference",
            "valueString": "microsofthealth/fhirconverter:default"
        },
        {
            "name": "rootTemplate",
            "valueString": "ADT_A01"
        }
    ]
}
```

**Risposta di esempio:**

```json
{
  "resourceType": "Bundle",
  "type": "transaction",
  "entry": [
    {
      "fullUrl": "urn:uuid:9d697ec3-48c3-3e17-db6a-29a1765e22c6",
      "resource": {
        "resourceType": "Patient",
        "id": "9d697ec3-48c3-3e17-db6a-29a1765e22c6",
        ...
        ...
      "request": {
        "method": "PUT",
        "url": "Location/50becdb5-ff56-56c6-40a1-6d554dca80f0"
      }
    }
  ]
}
```

## <a name="customize-templates"></a>Personalizzare i modelli

È possibile usare l' [estensione FHIR Converter](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) per Visual Studio Code per personalizzare i modelli in base alle esigenze. L'estensione fornisce un'esperienza di modifica interattiva e consente di scaricare i modelli pubblicati da Microsoft e i dati di esempio. Per informazioni dettagliate, vedere la documentazione nell'estensione.

## <a name="host-and-use-templates"></a>Ospitare e usare i modelli

Si consiglia vivamente di ospitare una copia personalizzata dei modelli in ACR. Sono necessari quattro passaggi per ospitare una copia personalizzata dei modelli e per usarli nell'operazione $convert-Data:

1. Eseguire il push dei modelli nel Container Registry di Azure.
1. Abilitare l'identità gestita nell'API di Azure per l'istanza di FHIR.
1. Fornire l'accesso all'API di Azure per l'identità gestita di FHIR.
1. Registrare i server ACR nell'API di Azure per FHIR.

### <a name="push-templates-to-azure-container-registry"></a>Push di modelli in Container Registry di Azure

Dopo la creazione di un'istanza di ACR, è possibile usare il comando _FHIR Converter: push templates_ nell' [estensione FHIR Converter](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-health-fhir-converter) per eseguire il push dei modelli personalizzati in ACR. In alternativa, è possibile usare lo strumento dell'interfaccia della riga di comando di [Gestione modelli](https://github.com/microsoft/FHIR-Converter/blob/main/docs/TemplateManagementCLI.md) per questo scopo.

### <a name="enable-managed-identity-on-azure-api-for-fhir"></a>Abilitare l'identità gestita nell'API di Azure per FHIR

Passare all'istanza del servizio API di Azure per FHIR nel portale di Azure e selezionare il pannello **Identity (identità** ).
Impostare lo stato **su** attivato per abilitare l'identità gestita nell'API di Azure per FHIR.

![Abilita identità gestita](media/convert-data/fhir-mi-enabled.png)

### <a name="provide-access-of-the-acr-to-azure-api-for-fhir"></a>Fornire l'accesso all'API di Azure ad per FHIR

Passare al pannello controllo di accesso (IAM) nell'istanza di ACR e selezionare _Aggiungi assegnazioni di ruolo_.

![Assegnazione di ruolo ACR](media/convert-data/fhir-acr-role-assignment.png)

Concedere il ruolo AcrPull all'API di Azure per l'istanza del servizio FHIR.

![Aggiungi ruolo](media/convert-data/fhir-acr-role-add.png)

### <a name="register-the-acr-servers-in-azure-api-for-fhir"></a>Registrare i server ACR nell'API di Azure per FHIR

È possibile registrare fino a venti server ACR nell'API di Azure per FHIR.

Installare l'interfaccia della riga di comando di healthcareapis da Azure PowerShell se necessario:

```powershell
az extension add -n healthcareapis
```

Registrare i server ACR nell'API di Azure per FHIR seguendo gli esempi seguenti:

#### <a name="register-a-single-acr-server"></a>Registrare un singolo server ACR

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

#### <a name="register-multiple-acr-servers"></a>Registrare più server ACR

```powershell
az healthcareapis acr add --login-servers "fhiracr2021.azurecr.io fhiracr2020.azurecr.io" --resource-group fhir-test --resource-name fhirtest2021
```

### <a name="verify"></a>Verifica

Eseguire una chiamata all'API $convert-data specificando il riferimento del modello nel parametro templateCollectionReference.

`<RegistryServer>/<imageName>@<imageDigest>`

## <a name="known-issues-and-workarounds"></a>Problemi noti e soluzioni alternative

- Alcuni file di modello predefiniti contengono un BOM UTF-8. Di conseguenza, i valori ID generati conterranno un carattere BOM. Questo potrebbe creare un problema con FHIR server. La soluzione alternativa consiste nell'eseguire il pull dei modelli Microsoft utilizzando VS Code estensione ed effettuare il push di tali modelli nel proprio ACR dopo aver rimosso i caratteri BOM da _ID/_procedure. Liquid_, _ID/_provenience. Liquid_ e _ID/_immunizzation. Liquid_.

