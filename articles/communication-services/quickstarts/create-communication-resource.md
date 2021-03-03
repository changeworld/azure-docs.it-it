---
title: 'Avvio rapido: Creare e gestire risorse in Servizi di comunicazione di Azure'
titleSuffix: An Azure Communication Services quickstart
description: In questo argomento di avvio rapido verrà illustrato come creare e gestire la prima risorsa di Servizi di comunicazione di Azure.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-azp-net
ms.openlocfilehash: 036df8d4c65ebd6288a526b3af7201e6f2131cd4
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656576"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>Avvio rapido: Creare e gestire le risorse di Servizi di comunicazione

Per iniziare a usare Servizi di comunicazione di Azure, effettuare il provisioning della prima risorsa di Servizi di comunicazione. È possibile eseguire il provisioning delle risorse dei servizi di comunicazione tramite il [portale di Azure](https://portal.azure.com) o con la libreria client di gestione .NET. La libreria client di gestione e la portale di Azure consentono di creare, configurare, aggiornare ed eliminare le risorse e l'interfaccia con [Azure Resource Manager](../../azure-resource-manager/management/overview.md), il servizio di distribuzione e gestione di Azure. Tutte le funzionalità disponibili nelle librerie client sono presenti nel portale di Azure.

> [!WARNING]
> Si noti che mentre i servizi di comunicazione sono disponibili in più aree geografiche, per ottenere un numero di telefono, la risorsa deve avere un percorso dati impostato su "US". Si noti anche che durante l'anteprima pubblica non è possibile trasferire le risorse di comunicazione a una sottoscrizione diversa.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-azcli"
[!INCLUDE [Azure CLI](./includes/create-resource-azcli.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

## <a name="access-your-connection-strings-and-service-endpoints"></a>Accedere alle stringhe di connessione e agli endpoint di servizio

Le stringhe di connessione consentono alle librerie client di Servizi di comunicazione di eseguire la connessione e l'autenticazione ad Azure. È possibile accedere alle stringhe di connessione e agli endpoint di servizio di Servizi di comunicazione dal portale di Azure o a livello di codice con le API di Azure Resource Manager.

Dopo aver eseguito l'accesso alla risorsa di Servizi di comunicazione, selezionare **Chiavi** dal menu di spostamento e copiare i valori di **Stringa di connessione** o **Endpoint** per l'utilizzo da parte delle librerie client di Servizi di comunicazione. Si noti che è possibile accedere alle chiavi primaria e secondaria. Questo può essere utile negli scenari in cui si vuole fornire l'accesso temporaneo alle risorse di Servizi di comunicazione a un ambiente di terze parti o di gestione temporanea.

:::image type="content" source="./media/key.png" alt-text="Screenshot della pagina Chiave di Servizi di comunicazione.":::

È anche possibile accedere alle informazioni chiave usando l'interfaccia della riga di comando di Azure:

```azurecli
az communication list --resource-group "<resourceGroup>"

az communication list-key --name "<communicationName>" --resource-group "<resourceGroup>"
```

## <a name="store-your-connection-string"></a>Archiviare la stringa di connessione

Le librerie client di Servizi di comunicazione usano le stringhe di connessione per autorizzare le richieste effettuate a Servizi di comunicazione. Sono disponibili diverse opzioni per l'archiviazione della stringa di connessione:

* Un'applicazione in esecuzione sul desktop o in un dispositivo può archiviare la stringa di connessione in un file **app.config** o in un file **web.config**. Aggiungere la stringa di connessione alla sezione **AppSettings** in tali file.
* Un'applicazione in esecuzione in un'istanza di Servizio app di Azure può archiviare la stringa di connessione nelle [impostazioni dell'applicazione di Servizio app](../../app-service/configure-common.md). Aggiungere la stringa di connessione alla sezione **Stringhe di connessione** della scheda Impostazioni applicazione nel portale.
* È possibile archiviare la stringa di connessione in [Azure Key Vault](../../data-factory/store-credentials-in-key-vault.md).
* Se l'applicazione viene eseguita localmente, potrebbe essere opportuno archiviare la stringa di connessione in una variabile di ambiente.

### <a name="store-your-connection-string-in-an-environment-variable"></a>Archiviare la stringa di connessione in una variabile di ambiente

Per configurare una variabile di ambiente, aprire una finestra della console e selezionare il sistema operativo dalle schede seguenti. Sostituire `<yourconnectionstring>` con la stringa di connessione effettiva.

#### <a name="windows"></a>[Windows](#tab/windows)

Aprire una finestra della console e immettere il comando seguente:

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

Dopo l'aggiunta della variabile di ambiente potrebbe essere necessario riavviare eventuali programmi in esecuzione che necessitano di leggere la variabile di ambiente, inclusa la finestra della console. Se ad esempio si usa Visual Studio come editor, riavviare Visual Studio prima di eseguire l'esempio.

#### <a name="macos"></a>[macOS](#tab/unix)

Modificare **.zshrc** e aggiungere la variabile di ambiente:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.zshrc` dalla finestra della console per rendere effettive le modifiche. Se la variabile di ambiente è stata creata con l'IDE aperto, per accedervi potrebbe essere necessario chiudere e riaprire l'editor, l'IDE o la shell.

#### <a name="linux"></a>[Linux](#tab/linux)

Modificare **.bash_profile** e aggiungere la variabile di ambiente:

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

Dopo avere aggiunto la variabile di ambiente, eseguire `source ~/.bash_profile` dalla finestra della console per rendere effettive le modifiche. Se la variabile di ambiente è stata creata con l'IDE aperto, per accedervi potrebbe essere necessario chiudere e riaprire l'editor, l'IDE o la shell.

---

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire e rimuovere una sottoscrizione di Servizi di comunicazione, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

Se sono presenti numeri di telefono assegnati alla risorsa al momento dell'eliminazione delle risorse, tali numeri verranno rilasciati automaticamente dalla risorsa nello stesso momento.

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come:

> [!div class="checklist"]
> * Creare una risorsa di Servizi di comunicazione
> * Configurare l'area geografica della risorsa e i tag
> * Accedere alle chiavi per la risorsa
> * Eliminare la risorsa

> [!div class="nextstepaction"]
> [Creare i primi token di accesso utente](access-tokens.md)
