---
author: baanders
description: file di inclusione per le esercitazioni di Gemelli digitali di Azure - configurazione del progetto di esempio
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 67a2799a93141ad84f458642d8499a58784cc19c
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103463763"
---
## <a name="configure-the-sample-project"></a>Configurare il progetto di esempio

Configurare quindi un'applicazione client di esempio che interagirà con l'istanza di Gemelli digitali di Azure.

Nel computer passare al file scaricato in precedenza dagli [*esempi end-to-end di Gemelli digitali di Azure*](/samples/azure-samples/digital-twins-samples/digital-twins-samples) e decomprimerlo se non è già stato fatto.

All'interno della cartella passare a _AdtSampleApp_. Aprire _**AdtE2ESample.sln**_ in Visual Studio 2019. 

In Visual Studio selezionare il file _SampleClientApp > **appsettings.json**_ per aprirlo nella finestra di modifica. Questo file verrà usato come file JSON preimpostato con le variabili di configurazione necessarie per eseguire il progetto.

Nel corpo del file modificare l' `instanceUrl` *URL del nome host* dell'istanza di Azure Digital Twins (aggiungendo **_https://_** davanti al *nome host*, come illustrato di seguito).

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-hostName>"
}
```

Salvare e chiudere il file. 

Configurare quindi il file *appsettings.json* in modo che venga copiato nella directory di output quando si compila *SampleClientApp*. A tale scopo, fare clic con il pulsante destro del mouse *sul fileappsettings.js* e scegliere **Proprietà**. Nel controllo **Proprietà** cercare la proprietà copia nella *directory di output* . Modificare il valore in **copia se più recente** se non è già impostato su tale valore.

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Estratto della finestra di Visual Studio che mostra il riquadro Esplora soluzioni con appsettings.json evidenziato e il riquadro Proprietà con la proprietà 'Copia nella directory di output' impostata su 'Copia se più recente'" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

Mantenere il progetto _**AdtE2ESample**_ aperto in Visual Studio per continuare a usarlo nell'esercitazione.

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
