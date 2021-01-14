---
title: Problemi noti-gemelli digitali di Azure
description: Ottenere supporto per il riconoscimento e la mitigazione di problemi noti con i dispositivi gemelli digitali di Azure.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 118b869cb97a7dd3ce65566a994a27d2a65a7402
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184935"
---
# <a name="known-issues-in-azure-digital-twins"></a>Problemi noti nei dispositivi gemelli digitali di Azure

Questo articolo fornisce informazioni sui problemi noti associati ai dispositivi gemelli digitali di Azure.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"errore client 400: richiesta non valida" nella Cloud Shell

**Descrizione del problema:** I comandi in Cloud Shell in esecuzione a *https://shell.azure.com* possono avere esito negativo a intermittenza con l'errore "400 errore del client: richiesta non valida per l'URL: http://localhost:50342/oauth2/token ", seguita da analisi dello stack completo.

| Questa operazione ha effetto? | Causa | Soluzione |
| --- | --- | --- |
| Nei &nbsp; dispositivi &nbsp; gemelli digitali di Azure &nbsp; questo influisca sui gruppi di comandi seguenti:<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | Questo è il risultato di un problema noto nel Cloud Shell: il [*recupero del token da cloud Shell intermitted ha esito negativo con errore del Client 400: richiesta non valida*](https://github.com/Azure/azure-cli/issues/11749).<br><br>Si presenta un problema con i token di autenticazione dell'istanza di Azure Digital gemelli e con l'autenticazione basata su [identità gestita](../active-directory/managed-identities-azure-resources/overview.md) predefinita di cloud Shell. <br><br>Questo non influisce sui comandi di Azure Digital gemelli dei `az dt` `az dt endpoint` gruppi di comandi o, perché usano un tipo diverso di token di autenticazione (in base a Azure Resource Manager), che non presenta un problema con l'autenticazione di identità gestita del cloud Shell. | Un modo per risolvere questo problema consiste nell'eseguire nuovamente il `az login` comando in cloud Shell e completare i passaggi di accesso successivi. Questa operazione consente di disattivare la sessione dall'autenticazione di identità gestita, evitando così il problema principale. Successivamente, dovrebbe essere possibile eseguire di nuovo il comando.<br><br>In alternativa, è possibile aprire il riquadro Cloud Shell nel portale di Azure e completare il Cloud Shell lavorare da questa posizione.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Immagine dell'icona del Cloud Shell nella barra delle icone portale di Azure" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Infine, un'altra soluzione consiste nell' [installare l'interfaccia della](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) riga di comando di Azure nel computer, in modo da poter eseguire i comandi dell'interfaccia della riga di comando Questo problema non viene riscontrato nell'interfaccia della riga di comando locale. |


## <a name="missing-role-assignment-after-scripted-setup"></a>Assegnazione di ruolo mancante dopo l'installazione tramite script

**Descrizione del problema:** Alcuni utenti potrebbero riscontrare problemi con la parte dell'assegnazione di ruolo di [*procedura: configurare un'istanza e l'autenticazione (con script)*](how-to-set-up-instance-scripted.md). Lo script non indica errori, ma il ruolo di *proprietario dei dati di dispositivi digitali gemelli di Azure* non è stato assegnato correttamente all'utente e questo problema influirà sulla capacità di creare altre risorse.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

| Questa operazione ha effetto? | Causa | Soluzione |
| --- | --- | --- |
| Per determinare se l'assegnazione di ruolo è stata configurata correttamente dopo l'esecuzione dello script, seguire le istruzioni riportate nella sezione [*verificare l'assegnazione del ruolo utente*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) dell'articolo di installazione. Se l'utente non viene visualizzato con questo ruolo, questo problema ha effetto. | Per gli utenti che hanno effettuato l'accesso con un [account Microsoft personale (MSA)](https://account.microsoft.com/account), l'ID principale dell'utente che identifica l'utente in comandi come questo può essere diverso dal messaggio di posta elettronica di accesso dell'utente, rendendo difficile l'individuazione e l'utilizzo da parte dello script per assegnare il ruolo in modo appropriato. | Per risolvere il caso, è possibile configurare manualmente l'assegnazione di ruolo usando le [istruzioni dell'interfaccia](how-to-set-up-instance-cli.md#set-up-user-access-permissions) della riga di comando o le [istruzioni portale di Azure](how-to-set-up-instance-portal.md#set-up-user-access-permissions). |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Problemi con l'autenticazione interattiva del browser in Azure. identità 1.2.0

**Descrizione del problema:** Quando si scrive il codice di autenticazione nelle applicazioni dei dispositivi gemelli digitali di Azure usando la versione **1.2.0** della **libreria [Azure. Identity](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)**, è possibile che si verifichino problemi con il metodo [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true) . Si presenta come risposta di errore "Azure. Identity. AuthenticationFailedException" quando si tenta di eseguire l'autenticazione in una finestra del browser. È possibile che la finestra del browser non venga avviata completamente o che venga eseguita correttamente l'autenticazione dell'utente, mentre l'applicazione client ha ancora esito negativo con l'errore.

| Questa operazione ha effetto? | Causa | Soluzione |
| --- | --- | --- |
| Il &nbsp; &nbsp; Metodo interessato &nbsp; viene &nbsp; usato &nbsp; negli &nbsp; &nbsp; articoli seguenti:<br><br>[*Esercitazione: Scrivere il codice di un'app client*](tutorial-code.md)<br><br>[*Procedura: scrivere codice di autenticazione dell'app*](how-to-authenticate-client.md)<br><br>[*Procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure*](how-to-use-apis-sdks.md) | Alcuni utenti hanno riscontrato questo problema con la versione **1.2.0** della `Azure.Identity` libreria. | Per risolvere il, aggiornare le applicazioni in modo che utilizzino una [versione successiva](https://www.nuget.org/packages/Azure.Identity) di `Azure.Identity` . Dopo aver aggiornato la versione della libreria, il browser dovrebbe caricare e autenticarsi come previsto. |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Problema con l'autenticazione delle credenziali di Azure predefinita in Azure. identità 1.3.0

**Descrizione del problema:** Quando si scrive il codice di autenticazione usando la versione **1.3.0** della libreria di **[identità di Azure](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true)**, alcuni utenti hanno riscontrato problemi con il metodo [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet?view=azure-dotnet&preserve-view=true) usato in molti esempi in questi documenti di Azure Digital gemelli. Si presenta come risposta di errore "Azure. Identity. AuthenticationFailedException: SharedTokenCacheCredential Authentication Failed" quando il codice tenta di eseguire l'autenticazione.

| Questa operazione ha effetto? | Causa | Soluzione |
| --- | --- | --- |
| `DefaultAzureCredential` viene utilizzato nella maggior parte degli esempi di documentazione per questo servizio che includono l'autenticazione di. Se si scrive il codice di autenticazione usando `DefaultAzureCredential` con la versione 1.3.0 della `Azure.Identity` libreria e visualizzando questo messaggio di errore, l'operazione avrà effetto sull'utente. | Questo è probabilmente il risultato di un problema di configurazione con `Azure.Identity` . | Una strategia per risolvere questo problema consiste nell'escludere `SharedTokenCacheCredential` dalle credenziali, come descritto in questo [problema DefaultAzureCredential](https://github.com/Azure/azure-sdk/issues/1970) attualmente aperto `Azure.Identity` .<br>Un'altra opzione consiste nel modificare l'applicazione per l'utilizzo di una versione precedente di `Azure.Identity` , ad esempio la [versione 1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3). Questo non ha alcun effetto funzionale sui dispositivi gemelli digitali di Azure e pertanto è anche una soluzione accettata. |

## <a name="next-steps"></a>Passaggi successivi

Scopri di più su sicurezza e autorizzazioni nei dispositivi gemelli digitali di Azure:
* [*Concetti: sicurezza per le soluzioni di dispositivi gemelli digitali di Azure*](concepts-security.md)