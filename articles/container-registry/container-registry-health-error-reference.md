---
title: Informazioni di riferimento sull'errore per i controlli di integrità del Registro di sistema
description: Codici di errore e possibili soluzioni ai problemi rilevati eseguendo il comando az acr check-health diagnostic in Registro Azure Container
ms.topic: article
ms.date: 01/25/2021
ms.openlocfilehash: f9716c29093ae58518bc86ec06af40522d49047c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773442"
---
# <a name="health-check-error-reference"></a>Informazioni di riferimento sull'errore di controllo integrità

Di seguito sono riportati i dettagli sui codici di errore restituiti dal [comando az acr check-health.][az-acr-check-health] Per ogni errore vengono elencate le possibili soluzioni.

Per informazioni sull'esecuzione `az acr check-healh` di , vedere Controllare [l'integrità di un Registro Azure Container.](container-registry-check-health.md)

## <a name="docker_command_error"></a>DOCKER_COMMAND_ERROR

Questo errore indica che non è stato possibile trovare il client Docker per l'interfaccia della riga di comando. Di conseguenza, non vengono eseguiti i controlli aggiuntivi seguenti: individuazione della versione di Docker, valutazione dello stato del daemon Docker ed esecuzione di un comando docker pull.

*Potenziali soluzioni:* installare il client Docker; aggiungere il percorso di Docker alle variabili di sistema.

## <a name="docker_daemon_error"></a>DOCKER_DAEMON_ERROR

Questo errore indica che lo stato del daemon Docker non è disponibile o che non è stato possibile raggiungerlo tramite l'interfaccia della riga di comando. Di conseguenza, le operazioni Docker (ad esempio e ) non `docker login` `docker pull` sono disponibili tramite l'interfaccia della riga di comando.

*Potenziali soluzioni:* riavviare il daemon Docker o verificare che sia installato correttamente.

## <a name="docker_version_error"></a>DOCKER_VERSION_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di eseguire il comando `docker --version` .

*Potenziali soluzioni:* provare a eseguire il comando manualmente, assicurarsi di avere la versione più recente dell'interfaccia della riga di comando ed esaminare il messaggio di errore.

## <a name="docker_pull_error"></a>DOCKER_PULL_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di eseguire il pull di un'immagine di esempio nell'ambiente.

*Potenziali soluzioni:* verificare che tutti i componenti necessari per eseguire il pull di un'immagine siano in esecuzione correttamente.

## <a name="helm_command_error"></a>HELM_COMMAND_ERROR

Questo errore indica che il client Helm non è stato trovato dall'interfaccia della riga di comando, che preclude altre operazioni Helm.

*Potenziali soluzioni:* verificare che il client Helm sia installato e che il relativo percorso sia aggiunto alle variabili di ambiente di sistema.

## <a name="helm_version_error"></a>HELM_VERSION_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di determinare la versione di Helm installata. Ciò può verificarsi se la versione dell'interfaccia della riga di comando di Azure (o se la versione helm) in uso è obsoleta.

*Potenziali soluzioni:* eseguire l'aggiornamento alla versione più recente dell'interfaccia della riga di comando di Azure o alla versione consigliata di Helm. eseguire il comando manualmente ed esaminare il messaggio di errore.

## <a name="cmk_error"></a>CMK_ERROR

Questo errore indica che il Registro di sistema non può accedere all'identità gestita assegnata dall'utente o sysem usata per configurare la crittografia del Registro di sistema con una chiave gestita dal cliente. L'identità gestita potrebbe essere stata eliminata.  

*Soluzione potenziale:* per risolvere il problema e ruotare la chiave usando un'identità gestita diversa, vedere la procedura per risolvere i problemi relativi [all'identità assegnata dall'utente.](container-registry-customer-managed-keys.md#troubleshoot)

## <a name="connectivity_dns_error"></a>CONNECTIVITY_DNS_ERROR

Questo errore indica che il dns per il server di accesso del Registro di sistema specificato è stato pinged ma non ha risposto, il che significa che non è disponibile. Ciò può indicare alcuni problemi di connettività. In alternativa, il Registro di sistema potrebbe non esistere, l'utente potrebbe non avere le autorizzazioni per il Registro di sistema (per recuperare correttamente il server di accesso) o il registro di destinazione si trova in un cloud diverso da quello usato nell'interfaccia della riga di comando di Azure.

*Soluzioni potenziali:* convalidare la connettività; verificare l'ortografia del Registro di sistema e tale registro esiste; verificare che l'utente abbia le autorizzazioni appropriate e che il cloud del Registro di sistema sia lo stesso usato nell'interfaccia della riga di comando di Azure.

## <a name="connectivity_forbidden_error"></a>CONNECTIVITY_FORBIDDEN_ERROR

Questo errore indica che l'endpoint di verifica per il registro specificato ha risposto con uno stato HTTP 403 Accesso negato. Questo errore indica che gli utenti non hanno accesso al Registro di sistema, molto probabilmente a causa di una configurazione di rete virtuale o perché l'accesso all'endpoint pubblico del Registro di sistema non è consentito. Per visualizzare le regole del firewall attualmente configurate, eseguire `az acr show --query networkRuleSet --name <registry>` .

*Potenziali soluzioni:* rimuovere le regole di rete virtuale o aggiungere l'indirizzo IP del client corrente all'elenco di indirizzi consentiti.

## <a name="connectivity_challenge_error"></a>CONNECTIVITY_CHALLENGE_ERROR

Questo errore indica che l'endpoint di verifica del registro di destinazione non ha generato una richiesta di verifica.

*Potenziali soluzioni:* riprovare dopo qualche tempo. Se l'errore persiste, aprire un problema in https://aka.ms/acr/issues .

## <a name="connectivity_aad_login_error"></a>CONNECTIVITY_AAD_LOGIN_ERROR

Questo errore indica che l'endpoint di verifica del registro di destinazione ha emesso una richiesta di verifica, ma il Registro di sistema non supporta l Azure Active Directory autenticazione.

*Potenziali soluzioni:* provare a eseguire l'autenticazione in modo diverso, ad esempio con le credenziali di amministratore. Se gli utenti devono eseguire l'autenticazione Azure Active Directory, aprire un problema all'indirizzo https://aka.ms/acr/issues .

## <a name="connectivity_refresh_token_error"></a>CONNECTIVITY_REFRESH_TOKEN_ERROR

Questo errore indica che il server di accesso del Registro di sistema non ha risposto con un token di aggiornamento, quindi l'accesso al Registro di sistema di destinazione è stato negato. Questo errore può verificarsi se l'utente non dispone delle autorizzazioni appropriate per il Registro di sistema o se le credenziali dell'utente per l'interfaccia della riga di comando di Azure non sono corrette.

*Potenziali soluzioni:* verificare se l'utente dispone delle autorizzazioni appropriate per il Registro di sistema. eseguire `az login` per aggiornare le autorizzazioni, i token e le credenziali.

## <a name="connectivity_access_token_error"></a>CONNECTIVITY_ACCESS_TOKEN_ERROR

Questo errore indica che il server di accesso del Registro di sistema non ha risposto con un token di accesso, in modo che l'accesso al Registro di sistema di destinazione sia stato negato. Questo errore può verificarsi se l'utente non dispone delle autorizzazioni appropriate per il Registro di sistema o se le credenziali dell'utente per l'interfaccia della riga di comando di Azure non sono corrette.

*Potenziali soluzioni:* verificare se l'utente dispone delle autorizzazioni appropriate per il Registro di sistema. eseguire `az login` per aggiornare le autorizzazioni, i token e le credenziali.

## <a name="connectivity_ssl_error"></a>CONNECTIVITY_SSL_ERROR

Questo errore indica che il client non è riuscito a stabilire una connessione sicura al registro contenitori. Questo errore si verifica in genere se si esegue o si usa un server proxy.

*Potenziali soluzioni:* altre informazioni sull'uso di un proxy sono [disponibili qui.](/cli/azure/use-cli-effectively)

## <a name="login_server_error"></a>LOGIN_SERVER_ERROR

Questo errore indica che l'interfaccia della riga di comando non è stata in grado di trovare il server di accesso del registro specificato e non è stato trovato alcun suffisso predefinito per il cloud corrente. Questo errore può verificarsi se il Registro di sistema non esiste, se l'utente non dispone delle autorizzazioni appropriate per il Registro di sistema, se il cloud del Registro di sistema e il cloud corrente dell'interfaccia della riga di comando di Azure non corrispondono o se la versione dell'interfaccia della riga di comando di Azure è obsoleta.

*Soluzioni potenziali:* verificare che l'ortografia sia corretta e che il Registro di sistema esista; verificare che l'utente abbia le autorizzazioni appropriate per il Registro di sistema e che i cloud del Registro di sistema e dell'ambiente dell'interfaccia della riga di comando corrispondano; aggiornare l'interfaccia della riga di comando di Azure alla versione più recente.

## <a name="notary_version_error"></a>NOTARY_VERSION_ERROR

Questo errore indica che l'interfaccia della riga di comando non è compatibile con la versione attualmente installata di Docker/Notary. Provare a eseguire il downgrade della versione notary.exe a una versione precedente alla 0.6.0 sostituendo manualmente il client Notary dell'installazione di Docker per risolvere il problema.

## <a name="next-steps"></a>Passaggi successivi

Per le opzioni per controllare l'integrità di un registro, vedere [Controllare l'integrità di un Registro Azure Container.](container-registry-check-health.md)

Vedere le [domande frequenti](container-registry-faq.md) per le domande frequenti e altri problemi noti relativi Registro Azure Container.





<!-- LINKS - internal -->
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
