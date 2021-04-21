---
title: Linguaggio Bicep per Azure Resource Manager modelli
description: Descrive il linguaggio Bicep per la distribuzione dell'infrastruttura in Azure Azure Resource Manager modelli.
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: af207e6ca88eab50fe6030883379c87c0ec05691
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773748"
---
# <a name="what-is-bicep-preview"></a>Che cos'è Bicep (anteprima)?

Bicep è un linguaggio per la distribuzione dichiarativa delle risorse di Azure. È possibile usare Bicep anziché JSON per sviluppare i modelli di Azure Resource Manager (modelli arm). Bicep semplifica l'esperienza di creazione fornendo una sintassi concisa, un supporto migliore per il riutilizzo del codice e una migliore sicurezza dei tipi. Bicep è un linguaggio specifico di dominio (DSL), ovvero è progettato per uno scenario o un dominio specifico. Non è inteso come linguaggio di programmazione generale per la scrittura di applicazioni.

La sintassi JSON per la creazione del modello può essere dettagliata e richiedere un'espressione complessa. Bicep migliora tale esperienza senza perdere le funzionalità di un modello JSON. Si tratta di un'astrazione trasparente sul codice JSON per i modelli arm. Ogni file Bicep viene compilato in un modello ARM standard. I tipi di risorse, le versioni dell'API e le proprietà valide in un modello arm sono validi in un file Bicep. Esistono alcune [limitazioni note nella](#known-limitations) versione corrente.

Bicep è attualmente in anteprima. Per tenere traccia dello stato del lavoro, vedere il [repository del progetto Bicep.](https://github.com/Azure/bicep)

Per altre informazioni su Bicep, vedere il video seguente.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>Introduzione

Per iniziare con Bicep, [installare gli strumenti](bicep-install.md).

Dopo aver installato gli strumenti, provare [l'esercitazione su Bicep.](./bicep-tutorial-create-first-bicep.md) La serie di esercitazioni illustra la struttura e le funzionalità di Bicep. Si distribuiscono i file Bicep e si converte un modello arm nel file Bicep equivalente.

Per visualizzare i file JSON e Bicep equivalenti affiancati, vedere [Bicep Playground.](https://aka.ms/bicepdemo)

Se si dispone di un modello arm esistente che si vuole convertire in Bicep, vedere Conversione di modelli [ARM tra JSON e Bicep.](bicep-decompile.md)

## <a name="benefits-of-bicep-versus-other-tools"></a>Vantaggi di Bicep rispetto ad altri strumenti

Bicep offre i vantaggi seguenti rispetto ad altre opzioni:

* **Supporto per tutti i tipi di risorse e le versioni api:** Bicep supporta immediatamente tutte le versioni di anteprima e ga per i servizi di Azure. Non appena un provider di risorse introduce nuovi tipi di risorse e versioni api, è possibile usarli nel file Bicep. Non è necessario attendere l'aggiornamento degli strumenti prima di usare i nuovi servizi.
* **Esperienza di creazione:** quando si VS Code per creare i file Bicep, si ottiene un'esperienza di creazione di prima classe. L'editor offre funzionalità di sicurezza dei tipi, IntelliSense e convalida della sintassi.
* **Modularità:** è possibile suddividere il codice Bicep in parti gestibili usando [i moduli](bicep-modules.md). Il modulo distribuisce un set di risorse correlate. I moduli consentono di riutilizzare il codice e semplificare lo sviluppo. Aggiungere il modulo a un file Bicep ogni volta che è necessario distribuire tali risorse.
* **Integrazione con i servizi di Azure:** Bicep è integrato con i servizi di Azure, ad esempio Criteri di Azure, le specifiche del modello e i progetti.
* **Nessun file di stato da gestire:** tutti gli stati vengono archiviati in Azure. Gli utenti possono collaborare e avere la certezza che gli aggiornamenti siano gestiti come previsto. Usare [l'operazione di what-if per](template-deploy-what-if.md) visualizzare in anteprima le modifiche prima di distribuire il modello.
* **Nessun costo e open source:** Bicep è completamente gratuito. Non è necessario pagare per le funzionalità Premium. È supportato anche dal supporto tecnico Microsoft.

## <a name="bicep-improvements"></a>Miglioramenti del bicipite

Bicep offre una sintassi più semplice e concisa rispetto al codice JSON equivalente. Non si usano `[...]` espressioni. Al contrario, si chiamano direttamente le funzioni e si ottengono valori da parametri e variabili. Assegnare a ogni risorsa distribuita un nome simbolico, che semplifica il riferimento a tale risorsa nel modello.

Ad esempio, il codice JSON seguente restituisce un valore di output da una proprietà della risorsa.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

L'espressione di output equivalente in Bicep è più facile da scrivere. Nell'esempio seguente viene restituita la stessa proprietà usando il nome simbolico **publicIP** per una risorsa definita all'interno del modello:

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

Per un confronto completo della sintassi, vedere [Confronto tra JSON e Bicep per i modelli](compare-template-syntax.md).

Bicep gestisce automaticamente le dipendenze tra le risorse. È possibile evitare di impostare `dependsOn` quando il nome simbolico di una risorsa viene usato in un'altra dichiarazione di risorsa.

La struttura del file Bicep è più flessibile del modello JSON. È possibile dichiarare parametri, variabili e output in qualsiasi punto del file. In JSON è necessario dichiarare tutti i parametri, le variabili e gli output all'interno delle sezioni corrispondenti del modello.

## <a name="known-limitations"></a>Limitazioni note

Attualmente esistono i limiti seguenti:

* Non è possibile impostare la modalità o le dimensioni del batch nei cicli di copia.
* Non è possibile combinare cicli e condizioni.
* Le matrici e gli oggetti a riga singola, ad `['a', 'b', 'c']` esempio , non sono supportati.

## <a name="faq"></a>Domande frequenti

**Perché creare una nuova lingua invece di usarne una esistente?**

È possibile pensare a Bicep come a una revisione del linguaggio del modello arm esistente anziché di un nuovo linguaggio. La sintassi è cambiata, ma la funzionalità di base e il runtime rimangono invariati.

Prima di sviluppare Bicep, è stato preso in considerazione l'uso di un linguaggio di programmazione esistente. Si è deciso che i destinatari troveranno più facile imparare a usare Bicep anziché iniziare a usare un altro linguaggio.

**Perché non concentrare l'energia su Terraform o su altre offerte di infrastruttura come codice di terze parti?**

Utenti diversi preferiscono linguaggi e strumenti di configurazione diversi. Tutti questi strumenti offrono un'esperienza ottimale in Azure. Bicep fa parte di questo lavoro.

Se si è soddisfatti dell'uso di Terraform, non c'è alcun motivo per cambiare. Microsoft si impegna a garantire che Terraform in Azure sia il migliore possibile.

Per i clienti che hanno selezionato modelli arm, Si ritiene che Bicep migliori l'esperienza di creazione. Bicep consente anche di eseguire la transizione per i clienti che non hanno adottato l'infrastruttura come codice.

**Bicep è solo per Azure?**

Bicep è un DSL incentrato sulla distribuzione di soluzioni complete in Azure. Per raggiungere questo obiettivo, è necessario lavorare con alcune API esterne ad Azure. Si prevede di fornire punti di estendibilità per tali scenari.

**Cosa accade ai modelli arm esistenti?**

Continuano a funzionare esattamente come sempre. Non è necessario apportare modifiche. Continueremo a supportare il linguaggio JSON del modello arm sottostante. I file Bicep vengono compilati in JSON e il file JSON viene inviato ad Azure per la distribuzione.

Quando si è pronti, è possibile [convertire i file JSON in Bicep](bicep-decompile.md).

## <a name="next-steps"></a>Passaggi successivi

Introduzione [all'esercitazione su Bicep.](./bicep-tutorial-create-first-bicep.md)
