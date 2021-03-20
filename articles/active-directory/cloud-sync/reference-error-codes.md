---
title: Codici di errore e descrizioni di Azure AD Connect Cloud Sync
description: articolo di riferimento per i codici di errore di sincronizzazione cloud
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/14/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0b1dbd9064e24327f129e8b8f957414d9162386
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650941"
---
# <a name="azure-ad-connect-cloud-sync-error-codes-and-descriptions"></a>Codici di errore e descrizioni di Azure AD Connect Cloud Sync
Di seguito è riportato un elenco di codici di errore e la relativa descrizione


## <a name="error-codes"></a>Codici di errore

|Codice di errore|Dettagli|Scenario|Soluzione|
|-----|-----|-----|-----|
|TimeOut|Messaggio di errore: è stato rilevato un errore di timeout della richiesta quando si contatta l'agente locale e si sincronizza la configurazione. Per ulteriori problemi relativi all'agente di sincronizzazione cloud, vedere le linee guida per la risoluzione dei problemi.|Timeout della richiesta. Il valore di timeout corrente è 10 minuti.|Vedere le [indicazioni per la risoluzione dei problemi](how-to-troubleshoot.md)|
|HybridSynchronizationActiveDirectoryInternalServerError|Messaggio di errore: non è stato possibile elaborare la richiesta in questo momento. Se il problema persiste, contattare il supporto tecnico e fornire il seguente identificatore di processo: AD2AADProvisioning. 30b500eaf9c643b2b78804e80c1421fe. 5c291d3c-d29f-4570-9d6b-f0c2fa3d5926. Dettagli aggiuntivi: l'elaborazione della richiesta HTTP ha generato un'eccezione. |Non è stato possibile elaborare i parametri ricevuti nella richiesta SCIM a una richiesta di ricerca.|Per informazioni dettagliate, vedere la risposta HTTP restituita dalla proprietà' Response ' di questa eccezione.|
|HybridIdentityServiceNoAgentsAssigned|Messaggio di errore: non è possibile trovare un agente attivo per il dominio che si sta tentando di sincronizzare. Controllare se gli agenti sono stati rimossi. In caso affermativo, reinstallare di nuovo l'agente.|Nessun agente in esecuzione. Probabilmente gli agenti sono stati rimossi. Registrare un nuovo agente.|"In questo caso, non verrà visualizzato alcun agente assegnato al dominio nel portale.|
|HybridIdentityServiceNoActiveAgents|Messaggio di errore: non è possibile trovare un agente attivo per il dominio che si sta tentando di sincronizzare. Verificare se l'agente è in esecuzione accedendo al server in cui è installato l'agente e verificare se "Microsoft Azure AD agente di sincronizzazione cloud" in servizi è in esecuzione.|"Gli agenti non sono in ascolto dell'endpoint ServiceBus. [L'agente è dietro un firewall che non consente le connessioni al bus di servizio](../../active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers.md#use-the-outbound-proxy-server)|
|HybridIdentityServiceInvalidResource|Messaggio di errore: non è stato possibile elaborare la richiesta in questo momento. Se il problema persiste, contattare il supporto tecnico e fornire il seguente identificatore di processo: AD2AADProvisioning. 3a2a0d8418f34f54a03da5b70b1f7b0c. d583d090-9cd3-4d0a-aee6-8d666658c3e9. Dettagli aggiuntivi: sembra che si sia verificato un problema con la configurazione della sincronizzazione cloud. Ripetere la registrazione dell'agente di sincronizzazione cloud nel dominio di Active Directory locale e riavviare la configurazione dal portale di Azure.|Il nome della risorsa deve essere impostato in modo che conosca quale agente contattare.|Ripetere la registrazione dell'agente di sincronizzazione cloud nel dominio di Active Directory locale e riavviare la configurazione dal portale di Azure.|
|HybridIdentityServiceAgentSignalingError|Messaggio di errore: non è stato possibile elaborare la richiesta in questo momento. Se il problema persiste, contattare il supporto tecnico e fornire il seguente identificatore di processo: AD2AADProvisioning. 92d2e8750f37407fa2301c9e52ad7e9b. efb835ef-62E8-42e3-B495-18d5272eb3f9. Dettagli aggiuntivi: non è stato possibile elaborare la richiesta in questo momento. Se il problema persiste, contattare il supporto tecnico con ID processo (dal riquadro Stato della configurazione).|Il bus di servizio non è in grado di inviare un messaggio all'agente. Potrebbe essere un'interruzione del bus di servizio oppure l'agente non risponde.|Se il problema persiste, contattare il supporto tecnico con ID processo (dal riquadro Stato della configurazione).|
|AzureDirectoryServiceServerBusy|Messaggio di errore: si è verificato un errore. Codice di errore: 81. Descrizione errore: Azure Active Directory è attualmente occupato. Questa operazione verrà ritentata automaticamente. Se il problema persiste per più di 24 ore, contattare il supporto tecnico. ID di traccia: nome del server 8a4ab3b5-3664-4278-ab64-9cff37fd3f4f:|Azure Active Directory è attualmente occupato.|Se il problema persiste per più di 24 ore, contattare il supporto tecnico.|
|AzureActiveDirectoryInvalidCredential|Messaggio di errore: è stato rilevato un problema con l'account del servizio utilizzato per eseguire Azure AD Connect sincronizzazione cloud. È possibile ripristinare l'account del servizio cloud seguendo le istruzioni riportate [qui](./how-to-troubleshoot.md). Se l'errore si mantiene, contattare il supporto tecnico con ID processo (dal riquadro Stato della configurazione). Dettagli aggiuntivi sull'errore: CredentialsInvalid AADSTS50034: l'account utente {EmailHidden} non esiste nella directory skydrive365.onmicrosoft.com. Per accedere a questa applicazione, l'account deve essere aggiunto alla directory. ID traccia: ID correlazione 14b63033-3bc9-4bd4-b871-5eb4b3500200:57d93ed1-be4d-483C-997C-a3b6f03deb00 timestamp: 2021-01-12 21:08:29Z |Questo errore viene generato quando l'account del servizio di sincronizzazione ADToAADSyncServiceAccount non esiste nel tenant. La causa potrebbe essere l'eliminazione accidentale dell'account.|Usare [Repair-AADCloudSyncToolsAccount](reference-powershell.md#repair-aadcloudsynctoolsaccount) per correggere l'account del servizio.|
|AzureActiveDirectoryExpiredCredentials|Messaggio di errore: non è stato possibile elaborare la richiesta in questo momento. Se il problema persiste, contattare il supporto tecnico con ID processo (dal riquadro Stato della configurazione). Dettagli aggiuntivi sull'errore: CredentialsExpired AADSTS50055: la password è scaduta. ID traccia: ID correlazione 989b1841-dbe5-49c9-ab6c-9aa25f7b0e00:1c69b196-1C3A-4381-9187-c84747807155 timestamp: 2021-01-12 20:59:31Z | Il codice di stato della risposta non indica esito positivo: 401 (non autorizzato).|Le credenziali dell'account del servizio AAD Sync sono scadute.|È possibile ripristinare l'account del servizio cloud seguendo le istruzioni riportate in https://go.microsoft.com/fwlink/?linkid=2150988 . Se l'errore si mantiene, contattare il supporto tecnico con ID processo (dal riquadro Stato della configurazione).  Dettagli aggiuntivi sull'errore: le credenziali di amministratore Azure Active Directory tenant sono state scambiate per un token OAuth che è scaduto da. "|
|AzureActiveDirectoryAuthenticationFailed|Messaggio di errore: non è stato possibile elaborare la richiesta in questo momento. Se il problema persiste, contattare il supporto tecnico e fornire il seguente identificatore di processo: AD2AADProvisioning. 60b943e88f234db2b887f8cb91dee87c. 707be0d2-c6a9-405D-a3b9-de87761dc3ac. Dettagli aggiuntivi: non è stato possibile elaborare la richiesta in questo momento. Se il problema persiste, contattare il supporto tecnico con ID processo (dal riquadro Stato della configurazione). Dettagli aggiuntivi sull'errore: UnexpectedError.|Errore sconosciuto.|Se il problema persiste, contattare il supporto tecnico con ID processo (dal riquadro Stato della configurazione).|

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è Azure AD Connect sincronizzazione cloud?](what-is-cloud-sync.md)