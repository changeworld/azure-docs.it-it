---
title: Lingua del bicipite per i modelli di Azure Resource Manager
description: Descrive il linguaggio bicipite per la distribuzione dell'infrastruttura in Azure tramite modelli di Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 0edd0063ea08978b89c3ad3067f41ae743451df7
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889689"
---
# <a name="what-is-bicep-preview"></a>Che cos'è il bicipite (anteprima)?

Bicipite è un linguaggio per la distribuzione dichiarativa delle risorse di Azure. È possibile usare il bicipite anziché JSON per lo sviluppo di modelli di Azure Resource Manager (modelli ARM). Il bicipite semplifica l'esperienza di creazione offrendo sintassi concisa, un supporto migliore per il riutilizzo del codice e una maggiore indipendenza dai tipi. Bicipite è un linguaggio specifico di dominio (DSL), ovvero è progettato per uno scenario o un dominio specifico. Non è destinato a un linguaggio di programmazione generale per la scrittura di applicazioni.

La sintassi JSON per la creazione di un modello può essere dettagliata e richiedere un'espressione complessa. Il bicipite migliora questa esperienza senza perdere le funzionalità di un modello JSON. Si tratta di un'astrazione trasparente sul codice JSON per i modelli ARM. Ogni file bicipite viene compilato in un modello ARM standard. I tipi di risorsa, le versioni API e le proprietà valide in un modello ARM sono validi in un file bicipite. Esistono alcune [limitazioni note](#known-limitations) nella versione corrente.

Per informazioni su bicipite, vedere il video seguente.

## <a name="get-started"></a>Introduzione

Per iniziare a usare bicipite, [installare gli strumenti](https://github.com/Azure/bicep/blob/main/docs/installing.md).

Dopo aver installato gli strumenti, provare l' [esercitazione sul bicipite](./bicep-tutorial-create-first-bicep.md). La serie di esercitazioni illustra la struttura e le funzionalità di bicipite. Distribuire i file bicipite e convertire un modello ARM nel file bicipite equivalente.

Per visualizzare i file JSON e bicipite equivalenti, vedere il [parco bicipite](https://aka.ms/bicepdemo).

Se si ha un modello ARM esistente che si vuole convertire in bicipite, vedere [conversione di modelli ARM tra JSON e bicipite](bicep-decompile.md).

## <a name="bicep-improvements"></a>Miglioramenti del bicipite

Il bicipite offre una sintassi più semplice e concisa rispetto all'equivalente JSON. Le espressioni non vengono usate `[...]` . Al contrario, è possibile chiamare direttamente le funzioni e ottenere i valori da parametri e variabili. Si assegna a ogni risorsa distribuita un nome simbolico, che consente di fare facilmente riferimento a tale risorsa nel modello.

Ad esempio, il codice JSON seguente restituisce un valore di output da una proprietà della risorsa.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

L'espressione di output equivalente in bicipite è più facile da scrivere. Nell'esempio seguente viene restituita la stessa proprietà utilizzando il nome simbolico **IP pubblico** per una risorsa definita all'interno del modello:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Per un confronto completo della sintassi, vedere [confronto tra JSON e bicipite per i modelli](compare-template-syntax.md).

Il bicipite gestisce automaticamente le dipendenze tra le risorse. È possibile evitare di impostare `dependsOn` quando il nome simbolico di una risorsa viene usato in un'altra dichiarazione di risorsa.

Con bicipite è possibile suddividere il progetto in più moduli.

La struttura del file bicipite è più flessibile rispetto al modello JSON. È possibile dichiarare parametri, variabili e output in qualsiasi punto del file. In JSON è necessario dichiarare tutti i parametri, le variabili e gli output all'interno delle sezioni corrispondenti del modello.

L'estensione VS Code per bicipite offre funzionalità avanzate di convalida e IntelliSense. Ad esempio, è possibile usare IntelliSense dell'estensione per ottenere le proprietà di una risorsa.

## <a name="known-limitations"></a>Limitazioni note

Attualmente esistono i limiti seguenti:

* Impossibile impostare la modalità o la dimensione del batch nei cicli di copia.
* Non è possibile combinare cicli e condizioni.
* Le matrici e gli oggetti a riga singola, ad esempio `['a', 'b', 'c']` , non sono supportati.

## <a name="faq"></a>Domande frequenti

**Perché creare un nuovo linguaggio anziché utilizzarne uno esistente?**

È possibile pensare al bicipite come revisione del linguaggio del modello ARM esistente anziché di un nuovo linguaggio. La sintassi è stata modificata, ma le funzionalità di base e il runtime rimangono invariati.

Prima di sviluppare bicipite, abbiamo preso in considerazione l'uso di un linguaggio di programmazione esistente. Abbiamo deciso che i nostri destinatari sarebbero più facili da apprendere al bicipite anziché iniziare a usare un altro linguaggio.

**Perché non concentrare l'energia sulla bonifica o su altre infrastrutture di terze parti come offerte di codice?**

Utenti diversi preferiscono diversi linguaggi e strumenti di configurazione. Si vuole garantire che tutti questi strumenti forniscano un'esperienza eccezionale in Azure. Il bicipite fa parte di tale sforzo.

Se si ha familiarità con la bonifica, non c'è alcun motivo per cambiare. Microsoft si impegna a garantire che la bonifica in Azure sia la migliore possibile.

Per i clienti che hanno selezionato modelli ARM, il bicipite è in grado di migliorare l'esperienza di creazione. Il bicipite contribuisce anche alla transizione per i clienti che non hanno adottato l'infrastruttura come codice.

**Il bicipite è solo per Azure?**

Bicipite è un linguaggio DSL incentrato sulla distribuzione di soluzioni complete in Azure. Il raggiungimento di questo obiettivo richiede l'uso di alcune API esterne ad Azure. Si prevede di fornire punti di estendibilità per questi scenari.

**Cosa accade ai modelli ARM esistenti?**

Continuano a funzionare esattamente come hanno sempre. Non è necessario apportare alcuna modifica. Continuerà a supportare il linguaggio JSON del modello ARM sottostante. I file del bicipite vengono compilati in JSON e il JSON viene inviato ad Azure per la distribuzione.

Quando si è pronti, è possibile [convertire i file JSON in bicipite](bicep-decompile.md).

## <a name="next-steps"></a>Passaggi successivi

Iniziare a usare l' [esercitazione sul bicipite](./bicep-tutorial-create-first-bicep.md).
