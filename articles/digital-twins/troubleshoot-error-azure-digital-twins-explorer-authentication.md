---
title: Azure Digital Twins Explorer errore di autenticazione
description: Cause e soluzioni per 'Autenticazione non riuscita'. in Azure Digital Twins Explorer.
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 4/8/2021
ms.openlocfilehash: 1f8373130fbead2204dd0ac2515595d68dd3b2e8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107495047"
---
# <a name="authentication-failed"></a>L'autenticazione non è riuscita.

Questo articolo descrive le cause e i passaggi di risoluzione per la ricezione di un errore di "Autenticazione non [riuscita" durante l'Azure Digital Twins Explorer](/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) di esempio nel computer locale. 

## <a name="symptoms"></a>Sintomi

Quando si configura ed esegue l'applicazione Azure Digital Twins Explorer, i tentativi di autenticazione con l'app vengono soddisfatti con il messaggio di errore seguente:

:::image type="content" source="media/troubleshoot-error-azure-digital-twins-explorer-authentication/authentication-error.png" alt-text="Screenshot di un messaggio di errore in Gemelli digitali di Azure Explorer con il testo seguente: Autenticazione non riuscita. Se si esegue l'app in locale, assicurarsi di aver eseguito l'accesso ad Azure nel computer host oppure, ad esempio, eseguendo &quot;az login&quot; in un prompt dei comandi, accedendo a Visual Studio o VS Code o impostando le variabili di ambiente. Per altre informazioni, vedere il file leggimi o cercare DefaultAzureCredential nella documentazione di Azure.Identity. Se si esegue adt-explorer ospitato nel cloud, assicurarsi che per la funzione di Azure di hosting sia impostata un'identità gestita assegnata dal sistema. Per altre informazioni, vedere il file Leggimi.":::

## <a name="causes"></a>Cause

### <a name="cause-1"></a>Causa #1

L Azure Digital Twins Explorer appalto usa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) (parte della libreria), che cerca le credenziali all'interno `Azure.Identity` dell'ambiente locale.

Come indicato nel testo dell'errore, questo errore può verificarsi se non sono state fornite credenziali locali per `DefaultAzureCredential` il ritiro.

Per altre informazioni sull'uso delle credenziali locali con Azure Digital Twins Explorer, vedere la sezione Configurare le credenziali di [*Azure*](quickstart-adt-explorer.md#set-up-local-azure-credentials) locali della Guida introduttiva di *Gemelli digitali di Azure: Esplorare uno scenario di esempio.*

### <a name="cause-2"></a>Causa #2

Questo errore può verificarsi anche se l'account Azure non dispone delle autorizzazioni necessarie per il controllo degli accessi in base al ruolo di Azure impostate nell'istanza Gemelli digitali di Azure azure. Per **accedere ai** dati nell'istanza, è necessario avere il ruolo lettore dati **Gemelli digitali di Azure** o proprietario dati Gemelli digitali di Azure nell'istanza che si sta tentando di leggere o gestire, rispettivamente. 

Per altre informazioni sulla sicurezza e sui ruoli in Gemelli digitali di Azure, vedere [*Concetti: Sicurezza per*](concepts-security.md)Gemelli digitali di Azure soluzioni .

## <a name="solutions"></a>Soluzioni

### <a name="solution-1"></a>Soluzione #1

Prima di tutto, assicurarsi di aver fornito le credenziali necessarie all'applicazione.

#### <a name="provide-local-credentials"></a>Fornire credenziali locali

`DefaultAzureCredential` esegue l'autenticazione al servizio usando le informazioni di un accesso di Azure locale. È possibile fornire le credenziali di Azure accedendo all'account Azure in una finestra locale dell'interfaccia della riga di comando di [Azure](/cli/azure/install-azure-cli) o in Visual Studio o Visual Studio Code.

È possibile visualizzare i tipi di credenziali che accettano, nonché l'ordine in cui vengono tentate, nella documentazione di Identità di `DefaultAzureCredential` [Azure per DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential).

Se è già stato eseguito l'accesso in locale all'account Azure giusto e il problema non viene risolto, passare alla soluzione successiva.

### <a name="solution-2"></a>Soluzione #2

Verificare che l'utente di Azure abbia il ruolo lettore dati **Gemelli digitali di Azure** nell'istanza di Gemelli digitali di Azure se si sta solo provando a leggerne i dati o il ruolo proprietario dati **Gemelli digitali di Azure** nell'istanza se si sta tentando di gestirne i dati.

Si noti che questo ruolo è diverso da...
* il nome precedente per questo ruolo durante *l'anteprima, Gemelli digitali di Azure proprietario (anteprima) (il* ruolo è lo stesso, ma il nome è stato modificato)
* il *ruolo Proprietario* nell'intera sottoscrizione di Azure. *Gemelli digitali di Azure proprietario dei dati* è un ruolo all'interno Gemelli digitali di Azure e ha come ambito questa singola Gemelli digitali di Azure istanza.
* il *ruolo Proprietario* in Gemelli digitali di Azure. Si tratta di due ruoli Gemelli digitali di Azure di gestione e Gemelli digitali di Azure *proprietario* dei dati è il ruolo che deve essere usato per la gestione.

 Se questo ruolo non è disponibile, configurarlo per risolvere il problema.

#### <a name="check-current-setup"></a>Controllare la configurazione corrente

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>Risolvere i problemi 

Se non si dispone di questa assegnazione di ruolo, un utente con ruolo Proprietario nella sottoscrizione di **Azure** deve eseguire il comando seguente per assegnare all'utente di Azure il ruolo appropriato nell'istanza **Gemelli digitali di Azure**. 

Se si è un proprietario della sottoscrizione, è possibile eseguire questo comando manualmente. In caso contrario, contattare un proprietario per eseguire questo comando per conto dell'utente. Il nome del ruolo è Gemelli digitali di Azure **proprietario dei dati per l'accesso** in modifica o Gemelli digitali di Azure **lettore dati** per l'accesso in lettura.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "<role-name>"
```

Per altre informazioni su questo requisito del ruolo e sul processo di assegnazione, vedere la sezione Configurare le autorizzazioni di accesso dell'utente in Procedura: Configurare [ *un'istanza e l'autenticazione*](how-to-set-up-instance-CLI.md#set-up-user-access-permissions) (interfaccia della riga di comando o *portale).*

## <a name="next-steps"></a>Passaggi successivi

Leggere i passaggi di configurazione per la creazione e l'autenticazione di una nuova Gemelli digitali di Azure istanza:
* [*Procedura: Configurare un'istanza e l'autenticazione (interfaccia della riga di comando)*](how-to-set-up-instance-cli.md)

Altre informazioni sulla sicurezza e sulle autorizzazioni per Gemelli digitali di Azure:
* [*Concetti: Sicurezza per Gemelli digitali di Azure soluzioni*](concepts-security.md)
