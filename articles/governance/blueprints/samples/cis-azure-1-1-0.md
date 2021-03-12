---
title: Esempio di progetto di benchmark v 1.1.0 di CIS Microsoft Azure Foundation
description: Panoramica dell'esempio di progetto di benchmark v 1.1.0 di CIS Microsoft Azure Foundations. Questo esempio di progetto consente ai clienti di valutare specifici controlli.
ms.date: 03/11/2021
ms.topic: sample
ms.openlocfilehash: b6188d96971329a83bc82042a1f840bd893354eb
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103197794"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v110-blueprint-sample"></a>Esempio di progetto di benchmark v 1.1.0 di CIS Microsoft Azure Foundation

L'esempio SID Microsoft Azure Foundations benchmark v 1.1.0 Blueprint fornisce Guard Guard di governance usando [criteri di Azure](../../policy/overview.md) che consentono di valutare le raccomandazioni specifiche del benchmark di Microsoft Azure delle fondamenta di CIS. Questo progetto consente ai clienti di distribuire un set di criteri di base per qualsiasi architettura distribuita in Azure che deve implementare le raccomandazioni di CIS Microsoft Azure Foundations benchmark v 1.1.0.

## <a name="recommendation-mapping"></a>Mapping di raccomandazioni

Il [mapping delle raccomandazioni sui criteri di Azure](../../policy/samples/cis-azure-1-1-0.md) fornisce informazioni dettagliate sulle definizioni dei criteri incluse in questo progetto e sul modo in cui queste definizioni di criteri sono mappate ai **consigli** in CIS Microsoft Azure Foundations benchmark v 1.1.0. Le risorse assegnate a un'architettura vengono valutate da Criteri di Azure per rilevare la mancata conformità alle definizioni di criteri assegnate. Per altre informazioni, vedere [Criteri di Azure](../../policy/overview.md).

## <a name="deploy"></a>Distribuire

Per distribuire l'esempio Blueprints CIS Microsoft Azure Foundations benchmark v 1.1.0 Blueprint, è necessario eseguire i passaggi seguenti:

> [!div class="checklist"]
> - Creare un nuovo progetto dall'esempio
> - Contrassegnare la copia dell'esempio come **Pubblicata**
> - Assegnare la copia del progetto a una sottoscrizione esistente

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free) prima di iniziare.

### <a name="create-blueprint-from-sample"></a>Creare il progetto dall'esempio

Prima di tutto, implementare l'esempio di progetto creando un nuovo progetto nell'ambiente basato sull'esempio.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Nella pagina **Getting started** (Introduzione) a sinistra selezionare il pulsante **Crea** in _Creare un progetto_.

1. Trovare l'esempio di progetto **CIS Microsoft Azure Foundations Benchmark v1.1.0** in _Altri esempi_ e selezionare **Usa questo esempio**.

1. Immettere le _informazioni di base_ dell'esempio di progetto:

   - **Nome progetto**: Specificare un nome per la copia dell'esempio di progetto CIS Microsoft Azure Foundations Benchmark.
   - **Posizione della definizione**: usare i puntini di sospensione e selezionare il gruppo di gestione in cui salvare la copia dell'esempio.

1. Selezionare la scheda _Artefatti_ nella parte superiore della pagina oppure **Avanti: Elementi** nella parte inferiore della pagina.

1. Esaminare l'elenco degli artefatti inclusi nell'esempio di progetto. Molti artefatti hanno parametri che verranno definiti in seguito. Una volta esaminato l'esempio di progetto, selezionare **Salva bozza**.

### <a name="publish-the-sample-copy"></a>Pubblicare la copia dell'esempio

La copia dell'esempio di progetto è stata creata nell'ambiente. Ma poiché è stata creata in modalità **Bozza**, è necessario **pubblicarla** per poterla assegnare e distribuire. La copia dell'esempio di progetto può essere personalizzata in base all'ambiente e alle esigenze, ma tale modifica potrebbe allontanarsi dall'allineamento con le raccomandazioni del benchmark v 1.1.0 di CIS Microsoft Azure Foundations.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Pubblica progetto** nella parte superiore della pagina. Nella nuova pagina a destra specificare un valore di **Versione** per la copia dell'esempio di progetto. Questa proprietà è utile se si apporta una modifica successivamente. In **Modifica le note** specificare ad esempio "Prima versione pubblicata dell'esempio di progetto CIS Microsoft Azure Foundations Benchmark". Quindi selezionare **Pubblica** nella parte inferiore della pagina.

### <a name="assign-the-sample-copy"></a>Assegnare la copia dell'esempio

Dopo che la copia dell'esempio di progetto è stata **pubblicata** correttamente, è possibile assegnarla a una sottoscrizione all'interno del gruppo di gestione in cui è stata salvata. In questo passaggio vengono specificati i parametri che rendono univoca ogni distribuzione della copia dell'esempio di progetto.

1. Selezionare **Tutti i servizi** nel riquadro a sinistra. Cercare e selezionare **Progetti**.

1. Selezionare la pagina **Definizioni di progetto** a sinistra. Usare i filtri per trovare la copia dell'esempio di progetto e quindi selezionarla.

1. Selezionare **Assegna progetto** nella parte superiore della pagina della definizione di progetto.

1. Specificare i valori dei parametri per l'assegnazione del progetto:

   - Nozioni di base

     - **Sottoscrizioni**: selezionare una o più sottoscrizioni presenti nel gruppo di gestione in cui è stata salvata la copia dell'esempio di progetto. Se si selezionano più sottoscrizioni, viene creata un'assegnazione per ognuna usando i parametri immessi.
     - **Nome dell'assegnazione**: il nome viene prepopolato in base al nome del progetto.
       Cambiarlo, se necessario, o lasciarlo inalterato.
     - **Località**: selezionare un'area in cui creare l'identità gestita. Azure Blueprint usa questa identità gestita per distribuire tutti gli elementi nel progetto assegnato. Per altre informazioni, vedere [Managed identities for Azure resources](../../../active-directory/managed-identities-azure-resources/overview.md) (Identità gestite per risorse di Azure).
     - **Versione della definizione di progetto**: Scegliere una versione **pubblicata** della copia dell'esempio di progetto.

   - Blocca assegnazione

     Selezionare l'impostazione di blocco del progetto per l'ambiente. Per altre informazioni, vedere [Blueprints resource locking](../concepts/resource-locking.md) (Blocco delle risorse del progetto).

   - Identità gestita

     Lasciare invariata l'opzione predefinita _Identità gestita assegnata dal sistema_.

   - Parametri dell'elemento

     I parametri definiti in questa sezione si applicano all'artefatto in cui sono definiti. Si tratta di [parametri dinamici](../concepts/parameters.md#dynamic-parameters), in quanto vengono definiti durante l'assegnazione del progetto. Per un elenco completo di parametri di artefatti e relative descrizioni, vedere la [tabella di parametri degli artefatti](#artifact-parameters-table).

1. Dopo avere immesso tutti i parametri, selezionare **Assegna** in fondo alla pagina. Viene creata l'assegnazione del progetto e inizia la distribuzione degli artefatti. La distribuzione richiede circa un'ora. Per controllare lo stato della distribuzione, aprire l'assegnazione del progetto.

> [!WARNING]
> Il servizio Azure Blueprints e gli esempi di progetti predefiniti sono **gratuiti**. Le risorse di Azure hanno un [prezzo in base al prodotto](https://azure.microsoft.com/pricing/). Usare la [calcolatrice dei prezzi](https://azure.microsoft.com/pricing/calculator/) per stimare il costo dell'esecuzione delle risorse distribuite da questo esempio di progetto.

### <a name="artifact-parameters-table"></a>Tabella di parametri degli artefatti

La tabella seguente contiene un elenco dei parametri degli artefatti del progetto:

|Nome dell'artefatto|Tipo di artefatto|Nome parametro|Descrizione|
|-|-|-|-|
|Controllare le raccomandazioni del Microsoft Azure Foundation di benchmark v 1.1.0 e distribuire estensioni di VM di supporto specifiche|Assegnazione di criteri|Elenco di aree in cui deve essere abilitato Network Watcher|Elenco di aree delimitate da punto e virgola. Per visualizzare un elenco completo delle aree, usare Get-AzLocation. Esempio: eastus; eastus2|
|Controllare le raccomandazioni del Microsoft Azure Foundation di benchmark v 1.1.0 e distribuire estensioni di VM di supporto specifiche|Assegnazione di criteri|Elenco di estensioni macchina virtuale approvate per l'uso|Elenco di estensioni delimitato da punto e virgola. Per visualizzare un elenco completo delle estensioni macchina virtuale, usare Get-AzVMExtensionImage. ad esempio AzureDiskEncryption; IaaSAntimalware|

## <a name="next-steps"></a>Passaggi successivi

Altri articoli sui progetti e su come usarli:

- Informazioni sul [ciclo di vita del progetto](../concepts/lifecycle.md).
- Informazioni su come usare [parametri statici e dinamici](../concepts/parameters.md).
- Informazioni su come personalizzare l'[ordine di sequenziazione del progetto](../concepts/sequencing-order.md).
- Informazioni su come usare in modo ottimale il [blocco delle risorse del progetto](../concepts/resource-locking.md).
- Informazioni su come [aggiornare assegnazioni esistenti](../how-to/update-existing-assignments.md).