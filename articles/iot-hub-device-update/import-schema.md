---
title: Importazione di aggiornamenti nell'aggiornamento del dispositivo per l'hub Internet-schema e altre informazioni | Microsoft Docs
description: Schema e altre informazioni correlate (inclusi gli oggetti) che vengono usate quando si importano gli aggiornamenti nell'aggiornamento del dispositivo per l'hub Internet.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 989535d0bd6f514e63c7cea9e5fd71912f8fb08b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104780158"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Importazione di aggiornamenti nell'aggiornamento del dispositivo per l'hub Internet-schema e altre informazioni
Se si vuole importare un aggiornamento nell'aggiornamento del dispositivo per l'hub Internet, assicurarsi di aver esaminato prima i [concetti](import-concepts.md) e le [procedure guidate](import-update.md) . Se si è interessati ai dettagli dello schema usato per la costruzione di un manifesto di importazione, oltre a informazioni sugli oggetti correlati, vedere di seguito.

## <a name="import-manifest-schema"></a>Importa schema manifesto

| Nome | Tipo | Descrizione | Restrizioni |
| --------- | --------- | --------- | --------- |
| UpdateId | Oggetto `UpdateId` | Aggiornare l'identità. |
| UpdateType | string | Tipo di aggiornamento: <br/><br/> * Specificare `microsoft/apt:1` quando si esegue un aggiornamento basato su pacchetti utilizzando l'agente di riferimento.<br/> * Specificare `microsoft/swupdate:1` quando si esegue un aggiornamento basato su immagine utilizzando l'agente di riferimento.<br/> * Specificare `microsoft/simulator:1` quando si usa il simulatore dell'agente di esempio.<br/> * Specificare un tipo personalizzato se si sviluppa un agente personalizzato. | Formato: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> Massimo 32 caratteri totali |
| InstalledCriteria | string | Stringa interpretata dall'agente per determinare se l'aggiornamento è stato applicato correttamente:  <br/> * Specificare il **valore** di SWVersion per il tipo di aggiornamento `microsoft/swupdate:1` .<br/> * Specificare `{name}-{version}` per il tipo `microsoft/apt:1` di aggiornamento, di cui il nome e la versione vengono ottenuti dal file apt.<br/> * Specificare una stringa personalizzata se si sviluppa un agente personalizzato.<br/> | Massimo 64 caratteri |
| Compatibilità | Matrice di `CompatibilityInfo` [oggetti](#compatibilityinfo-object) | Informazioni sulla compatibilità del dispositivo compatibile con questo aggiornamento. | Massimo 10 elementi |
| CreatedDateTime | Data/ora | Data e ora di creazione dell'aggiornamento. | Formato di data e ora ISO 8601 delimitato, in UTC |
| ManifestVersion | string | Importa la versione dello schema del manifesto. Specificare `2.0` , che sarà compatibile con l' `urn:azureiot:AzureDeviceUpdateCore:1` interfaccia e l' `urn:azureiot:AzureDeviceUpdateCore:4` interfaccia. | Deve essere `2.0` |
| File | Matrice di `File` oggetti | Aggiornare i file di payload | Massimo 5 file |

## <a name="updateid-object"></a>Oggetto codice UpdateID

| Nome | Tipo | Descrizione | Restrizioni |
| --------- | --------- | --------- | --------- |
| Provider | string | Parte del provider dell'identità dell'aggiornamento. | 1-64 caratteri alfanumerici, punti e trattini. |
| Nome | stringa | Parte relativa al nome dell'identità dell'aggiornamento. | 1-64 caratteri alfanumerici, punti e trattini. |
| Versione | version | Parte della versione dell'identità dell'aggiornamento. | da 2 a 4 parti, numero di versione separato da punti compreso tra 0 e 2147483647. Gli zeri iniziali verranno eliminati. |

## <a name="file-object"></a>Oggetto file

| Nome | Tipo | Descrizione | Restrizioni |
| --------- | --------- | --------- | --------- |
| Nome file | string | Nome del file | Deve essere univoco all'interno di un aggiornamento |
| SizeInBytes | Int64 | Dimensioni del file in byte. | Massimo 800 MB per singolo file o 800 MB collettivamente per ogni aggiornamento |
| Hash | Oggetto `Hashes` | Oggetto JSON contenente gli hash (es) del file |

## <a name="compatibilityinfo-object"></a>Oggetto CompatibilityInfo

| Nome | Tipo | Descrizione | Restrizioni |
| --- | --- | --- | --- |
| DeviceManufacturer | string | Produttore del dispositivo con cui è compatibile l'aggiornamento. | 1-64 caratteri alfanumerici, punti e trattini. |
| DeviceModel | string | Modello del dispositivo con cui è compatibile l'aggiornamento. | 1-64 caratteri alfanumerici, punti e trattini. |

## <a name="hashes-object"></a>Hashs (oggetto)

| Nome | Obbligatoria | Tipo | Descrizione |
| --------- | --------- | --------- | --------- |
| SHA256 | True | string | Hash con codifica Base64 del file usando l'algoritmo SHA-256. |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [concetti](./import-concepts.md)relativi all'importazione.

Se si è pronti, provare la [Guida all'importazione How-To](./import-update.md), che illustra in modo dettagliato il processo di importazione.
