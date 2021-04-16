---
title: Testare il file di definizione dell'interfaccia utente
description: Descrizione di come testare l'esperienza utente per la creazione di applicazione gestita di Azure tramite il portale.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: eeef454e4c5706b39d07261ade1c2f0ffbc942ad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478897"
---
# <a name="test-your-portal-interface-for-azure-managed-applications"></a>Testare l'interfaccia del portale per Applicazioni gestite di Azure

Dopo [aver creato il createUiDefinition.jsnel file per](create-uidefinition-overview.md) l'applicazione gestita, è necessario testare l'esperienza utente. Per semplificare i test, usare un ambiente sandbox che carica il file nel portale. Non è necessario distribuire l'applicazione gestita. La sandbox presenta l'interfaccia utente nell'esperienza corrente del portale a schermo intero. In caso contrario, è possibile usare uno script per testare l'interfaccia. Entrambi gli approcci sono illustrati in questo argomento. La sandbox è il modo consigliato per visualizzare in anteprima l'interfaccia.

## <a name="prerequisites"></a>Prerequisiti

* Un file **createUiDefinition.json**. Se questo file non è presente, copiare il [file di esempio](https://github.com/Azure/azure-quickstart-templates/blob/master/demos/100-marketplace-sample/createUiDefinition.json).

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="use-sandbox"></a>Usare sandbox

1. Aprire Create [UI Definition Sandbox (Crea sandbox di definizione dell'interfaccia utente).](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade)

   ![Mostra sandbox](./media/test-createuidefinition/show-sandbox.png)

1. Sostituire la definizione vuota con il contenuto del createUiDefinition.jsnel file . Selezionare **Anteprima**.

   ![Selezionare l'anteprima](./media/test-createuidefinition/select-preview.png)

1. Verrà visualizzato il modulo creato. È possibile eseguire l'esperienza utente un'istruzione alla volta e compilare i valori.

   ![Mostra modulo](./media/test-createuidefinition/show-ui-form.png)

### <a name="troubleshooting"></a>Risoluzione dei problemi

Se il modulo non viene visualizzato dopo aver selezionato **Anteprima**, è possibile che si sia verificato un errore di sintassi. Cercare l'indicatore rosso sulla barra di scorrimento a destra e passare a esso.

![Mostra errore di sintassi](./media/test-createuidefinition/show-syntax-error.png)

Se il modulo non viene visualizzato e viene invece visualizzata un'icona di una nuvola con la descrizione, il modulo contiene un errore, ad esempio una proprietà mancante. Aprire l'Strumenti di sviluppo Web nel browser. La **Console** consente di visualizzare i messaggi importanti relativi all'interfaccia.

![Mostrare l'errore](./media/test-createuidefinition/show-error.png)

## <a name="use-test-script"></a>Usare lo script di test

Per testare l'interfaccia nel portale, copiare uno dei seguenti script nel computer locale:

* [Script di side load di PowerShell - Modulo Az](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-AzCreateUIDefinition.ps1)
* [Script di side load di PowerShell - Modulo di Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/SideLoad-CreateUIDefinition.ps1)
* [Script dell'interfaccia della riga di comando di Azure sideload](https://github.com/Azure/azure-quickstart-templates/blob/master/sideload-createuidef.sh)

Per visualizzare il file di interfaccia nel portale, eseguire lo script scaricato. Lo script crea un account di archiviazione nella sottoscrizione di Azure e carica il file createuidefinition.json nell'account di archiviazione. L'account di archiviazione viene creato la prima volta che si esegue lo script o se è stato eliminato. Se l'account di archiviazione esiste già nella sottoscrizione di Azure, lo script lo riutilizza. Lo script apre il portale e carica il file dall'account di archiviazione.

Specificare un percorso per l'account di archiviazione e specificare la cartella che contiene il file createuidefinition.json.

Per PowerShell, usare:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1 `
  -StorageResourceGroupLocation southcentralus `
  -ArtifactsStagingDirectory .\100-Marketplace-Sample
```

Per l'interfaccia della riga di comando di Azure usare:

```bash
./sideload-createuidef.sh \
  -l southcentralus \
  -a .\100-Marketplace-Sample
```

Se il file createUiDefinition.json si trova nella stessa cartella dello script, se è già stato creato l'account di archiviazione, non è necessario specificare tali parametri.

Per PowerShell, usare:

```powershell
.\SideLoad-AzCreateUIDefinition.ps1
```

Per l'interfaccia della riga di comando di Azure usare:

```bash
./sideload-createuidef.sh
```

Lo script apre una nuova scheda nel browser. Viene visualizzato il portale con l'interfaccia per la creazione dell'applicazione gestita.

Specificare i valori per il campo. Al termine, verranno visualizzati i valori passati al modello che sono disponibili nella console degli strumenti di sviluppo del browser.

![Mostrare i valori](./media/test-createuidefinition/show-json.png)

È possibile usare questi valori come il file dei parametri per eseguire il test del modello di distribuzione.

Se il portale si blocca nella schermata di riepilogo, potrebbe esserci un bug nella sezione di output. Ad esempio, è stato fatto riferimento a un controllo che non esiste. Se un parametro nell'output è vuoto, il parametro potrebbe fare riferimento a una proprietà che non esiste. Ad esempio, il riferimento al controllo è valido ma il riferimento alla proprietà non è valido.

## <a name="test-your-solution-files"></a>Testare il file della soluzione

Ora che si è verificato che l'interfaccia del portale funziona come previsto, è possibile verificare che il file createUiDefinition sia correttamente integrato con il file maintemplate.json. È possibile eseguire uno script di convalida dei test per testare il contenuto dei file di soluzione, incluso il file createUiDefinition. Lo script convalida la sintassi JSON, verifica la presenza di espressioni regex nei campi di testo e garantisce che i valori di output dell'interfaccia del portale corrispondono ai parametri del modello. Per informazioni sull'esecuzione di questo script, vedere [Eseguire i controlli di convalida statica per i modelli](https://aka.ms/arm-ttk).

## <a name="next-steps"></a>Passaggi successivi

Dopo aver verificato l'interfaccia del portale, leggere altre informazioni su come rendere disponibili le [Applicazioni gestite di Azure nel marketplace](../../marketplace/create-new-azure-apps-offer.md).
