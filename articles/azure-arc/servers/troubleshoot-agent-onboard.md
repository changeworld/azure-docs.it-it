---
title: Risolvere i Azure Arc problemi di connessione dell'agente server abilitati
description: Questo articolo illustra come risolvere i problemi relativi all'agente connected machine che si verificano con Azure Arc server abilitati quando si tenta di connettersi al servizio.
ms.date: 04/12/2021
ms.topic: conceptual
ms.openlocfilehash: ae26b599a72129b5ed7f47d76d10353be5c0e8ac
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107498000"
---
# <a name="troubleshoot-azure-arc-enabled-servers-agent-connection-issues"></a>Risolvere i Azure Arc problemi di connessione dell'agente server abilitati

Questo articolo fornisce informazioni sulla risoluzione dei problemi che possono verificarsi durante il tentativo di configurare l'agente connected machine Azure Arc server abilitati per Windows o Linux. Sono inclusi sia i metodi di installazione interattivi che su larga scala durante la configurazione della connessione al servizio. Per informazioni generali, vedere [Panoramica dei server abilitati per Arc.](./overview.md)

## <a name="agent-error-codes"></a>Codici di errore dell'agente

Se viene visualizzato un errore durante la configurazione dell'agente Azure Arc server abilitati, la tabella seguente consente di identificare la causa probabile e la procedura suggerita per risolvere il problema. Per continuare, è necessario che il codice di errore ("0000" sia qualsiasi numero a 4 cifre) stampato nella console o `AZCM0000` nell'output dello script.

| Codice di errore | Possibile causa | Correzione consigliata |
|------------|----------------|-----------------------|
| AZCM0000 | L'azione è riuscita | N/D |
| AZCM0001 | Si è verificato un errore sconosciuto | Contattare Supporto tecnico Microsoft per ulteriore assistenza |
| AZCM0011 | L'utente ha annullato l'azione (CTRL+C) | Ripetere il comando precedente |
| AZCM0012 | Il token di accesso fornito non è valido | Ottenere un nuovo token di accesso e riprovare |
| AZCM0013 | I tag specificati non sono validi | Verificare che i tag siano racchiusi tra virgolette doppie, separati da virgole, e che i nomi o i valori con spazi siano racchiusi tra virgolette singole: `--tags "SingleName='Value with spaces',Location=Redmond"`
| AZCM0014 | Il cloud non è valido | Specificare un cloud supportato: `AzureCloud` o `AzureUSGovernment` |
| AZCM0015 | L'ID di correlazione specificato non è un GUID valido | Specificare un GUID valido per `--correlation-id` |
| AZCM0016 | Manca un parametro obbligatorio | Esaminare l'output per identificare i parametri mancanti |
| AZCM0017 | Il nome della risorsa non è valido | Specificare un nome che usi solo caratteri alfanumerici, trattini e/o caratteri di sottolineatura. Il nome non può terminare con un trattino o un carattere di sottolineatura. |
| AZCM0018 | Il comando è stato eseguito senza privilegi amministrativi | Ripetere il comando con privilegi di amministratore o radice in un prompt dei comandi con privilegi elevati o in una sessione della console. |
| AZCM0041 | Le credenziali specificate non sono valide | Per gli account di accesso del dispositivo, verificare che l'account utente specificato abbia accesso al tenant e alla sottoscrizione in cui verrà creata la risorsa server. Per gli account di accesso dell'entità servizio, verificare la correttezza dell'ID client e del segreto, la data di scadenza del segreto e che l'entità servizio sia dello stesso tenant in cui verrà creata la risorsa server. |
| AZCM0042 | Creazione della risorsa server abilitata per Arc non riuscita | Verificare che l'utente o l'entità servizio specificata abbia accesso per creare risorse server abilitate per Arc nel gruppo di risorse specificato. |
| AZCM0043 | Eliminazione della risorsa server abilitata per Arc non riuscita | Verificare che l'utente o l'entità servizio specificata abbia accesso per eliminare le risorse del server abilitate per Arc nel gruppo di risorse specificato. Se la risorsa non esiste più in Azure, usare il `--force-local-only` flag per continuare. |
| AZCM0044 | Esiste già una risorsa con lo stesso nome | Specificare un nome diverso per il `--resource-name` parametro oppure eliminare il server esistente abilitato per Arc in Azure e riprovare. |
| AZCM0061 | Impossibile raggiungere il servizio agente | Verificare che il comando sia in esecuzione in un contesto utente con privilegi elevati (amministratore/radice) e che il servizio HIMDS sia in esecuzione nel server. |
| AZCM0062 | Si è verificato un errore durante la connessione del server | Esaminare altri codici di errore nell'output per informazioni più specifiche. Se l'errore si è verificato dopo la creazione della risorsa di Azure, è necessario eliminare il server Arc dal gruppo di risorse prima di riprovare. |
| AZCM0063 | Si è verificato un errore durante la disconnessione del server | Esaminare altri codici di errore nell'output per informazioni più specifiche. Se si continua a riscontrare questo errore, è possibile eliminare la risorsa in Azure e quindi eseguire nel `azcmagent disconnect --force-local-only` server per disconnettere l'agente. |
| AZCM0064 | Il servizio agente non risponde | Controllare lo stato del `himds` servizio per assicurarsi che sia in esecuzione. Avviare il servizio se non è in esecuzione. Se è in esecuzione, attendere un minuto e riprovare. |
| AZCM0065 | Si è verificato un errore di comunicazione interno dell'agente | Contattare Supporto tecnico Microsoft assistenza |
| AZCM0066 | Il servizio Web dell'agente non risponde o non è disponibile | Contattare Supporto tecnico Microsoft assistenza |
| AZCM0067 | L'agente è già connesso ad Azure | Seguire prima i passaggi descritti in [Disconnettere l'agente,](manage-agent.md#unregister-machine) quindi riprovare. |
| AZCM0068 | Si è verificato un errore interno durante la disconnessione del server da Azure | Contattare Supporto tecnico Microsoft assistenza |
| AZCM0081 | Si è verificato un errore durante il download del certificato Azure Active Directory di identità gestita | Se questo messaggio viene visualizzato durante il tentativo di connettere il server ad Azure, l'agente non sarà in grado di comunicare con il Azure Arc servizio. Eliminare la risorsa in Azure e riprovare a connettersi. |
| AZCM0101 | Il comando non è stato analizzato correttamente | Eseguire `azcmagent <command> --help` per esaminare la sintassi corretta del comando |
| AZCM0102 | Impossibile recuperare il nome host del computer | Eseguire per verificare la presenza di eventuali messaggi di errore a livello `hostname` di sistema, quindi contattare Supporto tecnico Microsoft. |
| AZCM0103 | Si è verificato un errore durante la generazione di chiavi RSA | Contattare Supporto tecnico Microsoft assistenza |
| AZCM0104 | Impossibile leggere le informazioni di sistema | Verificare che l'identità usata per l'esecuzione abbia privilegi `azcmagent` di amministratore/radice nel sistema e riprovare. |

## <a name="agent-verbose-log"></a>Log dettagliato dell'agente

Prima di seguire la procedura di risoluzione dei problemi descritta più avanti in questo articolo, le informazioni minime necessarie sono il log dettagliato. Contiene l'output dei comandi dello strumento **azcmagent,** quando viene usato l'argomento dettagliato (-v). I file di log vengono scritti in `%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log` per Windows e Linux in `/var/opt/azcmagent/log/azcmagent.log` .

### <a name="windows"></a>Windows

Di seguito è riportato un esempio del comando per abilitare la registrazione dettagliata con l'agente Connected Machine per Windows quando si esegue un'installazione interattiva.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Di seguito è riportato un esempio del comando per abilitare la registrazione dettagliata con l'agente Connected Machine per Windows quando si esegue un'installazione su larga scala usando un'entità servizio.

```console
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
  --verbose
```

### <a name="linux"></a>Linux

Di seguito è riportato un esempio del comando per abilitare la registrazione dettagliata con l'agente Connected Machine per Linux quando si esegue un'installazione interattiva.

>[!NOTE]
>Per eseguire  **azcmagent,** è necessario disporre delle autorizzazioni di accesso radice nei computer Linux.

```bash
azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --verbose
```

Di seguito è riportato un esempio del comando per abilitare la registrazione dettagliata con l'agente Connected Machine per Linux quando si esegue un'installazione su larga scala usando un'entità servizio.

```bash
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
  --verbose
```

## <a name="agent-connection-issues-to-service"></a>Problemi di connessione dell'agente al servizio

Nella tabella seguente sono elencati alcuni degli errori noti e i suggerimenti su come risolverli e risolverli.

|Message |Errore |Possibile causa |Soluzione |
|--------|------|---------------|---------|
|Impossibile acquisire il flusso del dispositivo del token di autorizzazione |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is unreachable.` |Impossibile raggiungere `login.windows.net` l'endpoint | Verificare la connettività all'endpoint. |
|Impossibile acquisire il flusso del dispositivo del token di autorizzazione |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp 40.126.9.7:443: connect: network is Forbidden`. |Il proxy o il firewall blocca l'accesso `login.windows.net` all'endpoint. | Verificare la connettività all'endpoint e non è bloccato da un firewall o da un server proxy. |
|Impossibile acquisire il flusso del dispositivo del token di autorizzazione  |`Error occurred while sending request for Device Authorization Code: Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/devicecode?api-version=1.0:  dial tcp lookup login.windows.net: no such host`. | Criteri di gruppo Configurazione *computer oggetti\ Modelli amministrativi\ Sistema\ Profili utente\* L'opzione Elimina profili utente precedenti a un numero specificato di giorni al riavvio del sistema è abilitata. | Verificare che l'oggetto Criteri di gruppo sia abilitato e che la destinazione sia il computer interessato. Per altri dettagli, vedere la nota a piè di pagina <sup>[1.](#footnote1)</sup> |
|Impossibile acquisire il token di autorizzazione dal nome SPN |`Failed to execute the refresh request. Error = 'Post https://login.windows.net/fb84ce97-b875-4d12-b031-ef5e7edf9c8e/oauth2/token?api-version=1.0: Forbidden'` |Il proxy o il firewall blocca l'accesso `login.windows.net` all'endpoint. |Verificare la connettività all'endpoint e non è bloccato da un firewall o da un server proxy. |
|Impossibile acquisire il token di autorizzazione dal nome SPN |`Invalid client secret is provided` |Segreto dell'entità servizio errato o non valido. |Verificare il segreto dell'entità servizio. |
| Impossibile acquisire il token di autorizzazione dal nome SPN |`Application with identifier 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' was not found in the directory 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'. This can happen if the application has not been installed by the administrator of the tenant or consented to by any user in the tenant` |Entità servizio e/o ID tenant non corretti. |Verificare l'entità servizio e/o l'ID tenant.|
|Ottenere la risposta alle risorse arm |`The client 'username@domain.com' with object id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' does not have authorization to perform action 'Microsoft.HybridCompute/machines/read' over scope '/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01' or the scope is invalid. If access was recently granted, please refresh your credentials."}}" Status Code=403` |Credenziali e/o autorizzazioni erre |Verificare che l'utente o l'entità servizio sia membro del Azure Connected Machine **onboarding.** |
|Impossibile connettersi alla risorsa arm AzcmagentConnect |`The subscription is not registered to use namespace 'Microsoft.HybridCompute'` |I provider di risorse di Azure non sono registrati. |Registrare i [provider di risorse](./agent-overview.md#register-azure-resource-providers). |
|Impossibile accedere alla risorsa arm AzcmagentConnect |`Get https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.HybridCompute/machines/MSJC01?api-version=2019-03-18-preview:  Forbidden` |Il server proxy o il firewall blocca l'accesso `management.azure.com` all'endpoint. |Verificare la connettività all'endpoint e non è bloccato da un firewall o da un server proxy. |

<a name="footnote1"></a><sup>1</sup> Se questo oggetto Criteri di gruppo è abilitato e si applica ai computer con l'agente Connected Machine, elimina il profilo utente associato all'account predefinito specificato per il *servizio himds.* Di conseguenza, elimina anche il certificato di autenticazione usato per comunicare con il servizio memorizzato nella cache nell'archivio certificati locale per 30 giorni. Prima del limite di 30 giorni, viene effettuato un tentativo di rinnovo del certificato. Per risolvere questo problema, seguire la procedura per annullare la registrazione [del computer](manage-agent.md#unregister-machine) e quindi registrarlo di nuovo con il servizio che esegue `azcmagent connect` .

## <a name="next-steps"></a>Passaggi successivi

Se il problema riscontrato non è presente in questo elenco o se non si riesce a risolverlo, visitare uno dei canali seguenti per ottenere ulteriore assistenza:

* Ottenere risposte da esperti di Azure tramite [Microsoft Q&A.](/answers/topics/azure-arc.html)

* Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Il supporto di Azure consente di entrare in contatto con la community di Azure e quindi di ottenere risposte, assistenza e consulenza.

* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Supporto tecnico**.
