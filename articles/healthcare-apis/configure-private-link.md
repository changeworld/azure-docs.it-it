---
title: Collegamento privato per l'API di Azure per FHIR
description: Questo articolo descrive come configurare un endpoint privato per l'API di Azure per i servizi FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/03/2021
ms.author: zxue
ms.openlocfilehash: 5e24c7666fd2ece7d284b7705bc481866d7604de
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097041"
---
# <a name="configure-private-link"></a>Configura collegamento privato

Il collegamento privato consente di accedere all'API di Azure per FHIR tramite un endpoint privato, un'interfaccia di rete che si connette privatamente e in modo sicuro usando un indirizzo IP privato della rete virtuale. Con il collegamento privato, è possibile accedere in modo sicuro ai servizi dalla VNET come servizio di prima entità senza dover passare attraverso un DNS pubblico. Questo articolo illustra come creare, testare e gestire l'endpoint privato per l'API di Azure per FHIR.

>[!Note]
>Non è possibile spostare il collegamento privato né l'API di Azure per FHIR da un gruppo di risorse o una sottoscrizione a un altro dopo aver abilitato il collegamento privato. Per spostare, eliminare prima il collegamento privato, quindi spostare l'API di Azure per FHIR e creare un nuovo collegamento privato al termine dello spostamento. Valutare le potenziali ramificazioni di sicurezza prima di eliminare il collegamento privato.
>
>Se è abilitata l'esportazione dei log di controllo e/o della metrica per l'API di Azure per FHIR, aggiornare l'impostazione di esportazione tramite le impostazioni di diagnostica dal portale.

## <a name="prerequisites"></a>Prerequisiti

Prima di creare un endpoint privato, è necessario creare prima alcune risorse di Azure:

- Gruppo di risorse: il gruppo di risorse di Azure che conterrà la rete virtuale e l'endpoint privato.
- API di Azure per FHIR: la risorsa FHIR che si vuole inserire dietro un endpoint privato.
- Rete virtuale: VNet a cui verranno connessi i servizi client e l'endpoint privato.

Per ulteriori informazioni, consultare la [documentazione del collegamento privato](../private-link/index.yml).

## <a name="disable-public-network-access"></a>Disabilitare l'accesso alla rete pubblica

La creazione di un endpoint privato per la risorsa FHIR non disabilita automaticamente il traffico pubblico. A tale scopo, sarà necessario aggiornare la risorsa FHIR per impostare una nuova proprietà "accesso pubblico" da "Enabled" a "disabled". Prestare attenzione quando si disabilita l'accesso alla rete pubblica perché tutte le richieste al servizio FHIR che non provengono da un endpoint privato configurato correttamente verranno negate. Sarà consentito solo il traffico proveniente dagli endpoint privati.

![Disabilitare l'accesso alla rete pubblica](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>Creare un endpoint privato

Per creare un endpoint privato, uno sviluppatore con autorizzazioni RBAC sulla risorsa FHIR può usare portale di Azure, [Azure PowerShell](../private-link/create-private-endpoint-powershell.md)o l'interfaccia della riga di comando di [Azure](../private-link/create-private-endpoint-cli.md). Questo articolo illustra i passaggi per l'uso di portale di Azure. Per automatizzare la creazione e la configurazione della zona di DNS privato, è consigliabile usare portale di Azure. Per ulteriori informazioni, è possibile fare riferimento al [collegamento privato avvio rapido guide](../private-link/create-private-endpoint-portal.md) .

Esistono due modi per creare un endpoint privato. Il flusso di approvazione automatica consente a un utente che dispone di autorizzazioni RBAC sulla risorsa FHIR di creare un endpoint privato senza dover approvare. Il flusso di approvazione manuale consente a un utente senza autorizzazioni sulla risorsa FHIR di richiedere l'approvazione di un endpoint privato da parte dei proprietari della risorsa FHIR.

### <a name="auto-approval"></a>Approvazione automatica

Assicurarsi che l'area per il nuovo endpoint privato sia uguale a quella per la rete virtuale. L'area per la risorsa FHIR può essere diversa.

![Scheda nozioni di base portale di Azure](media/private-link/private-link-portal2.png)

Per tipo di risorsa, cercare e selezionare "Microsoft. HealthcareApis/Services". Per Resource selezionare la risorsa FHIR. Per risorsa secondaria di destinazione selezionare "Fhir".

![Scheda risorsa portale di Azure](media/private-link/private-link-portal1.png)

Se non è stata configurata una zona di DNS privato esistente, selezionare "(nuovo) privatelink. azurehealthcareapis. com". Se è già stata configurata la zona di DNS privato, è possibile selezionarla dall'elenco. Deve essere nel formato "privatelink.azurehealthcareapis.com".

![Scheda configurazione portale di Azure](media/private-link/private-link-portal3.png)

Al termine della distribuzione, è possibile tornare alla scheda "connessioni a endpoint privati", in cui verrà visualizzato "approvato" come stato della connessione.

### <a name="manual-approval"></a>Approvazione manuale

Per l'approvazione manuale, selezionare la seconda opzione in risorsa, "Connetti a una risorsa di Azure in base all'ID risorsa o alias". Per sottorisorsa di destinazione, immettere "Fhir" come in approvazione automatica.

![Approvazione manuale](media/private-link/private-link-manual.png)

Al termine della distribuzione, è possibile tornare alla scheda "connessioni a endpoint privati", in cui è possibile approvare, rifiutare o rimuovere la connessione.

![Opzioni](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>Testare un endpoint privato

Per assicurarsi che il server FHIR non riceva traffico pubblico dopo avere disabilitato l'accesso alla rete pubblica, provare a raggiungere l'endpoint/metadata per il server dal computer. Si dovrebbe ricevere un 403 non consentito. Si noti che possono essere necessari fino a 5 minuti dopo l'aggiornamento del flag di accesso alla rete pubblica prima che il traffico pubblico venga bloccato.

Per assicurarsi che l'endpoint privato possa inviare il traffico al server:

1. Creare una VM connessa alla rete virtuale e alla subnet in cui è configurato l'endpoint privato. Per assicurarsi che il traffico dalla macchina virtuale usi solo la rete privata, è possibile disabilitare il traffico Internet in uscita tramite la regola NSG.
2. RDP nella macchina virtuale.
3. Provare a raggiungere l'endpoint/metadata del server FHIR dalla macchina virtuale. si dovrebbe ricevere l'istruzione Capability come risposta.

## <a name="manage-private-endpoint"></a>Endpoint privato gestito

### <a name="view"></a>Visualizzazione

Gli endpoint privati e la scheda di interfaccia di rete associata sono visibili in portale di Azure dal gruppo di risorse in cui sono stati creati.

![Visualizza nelle risorse](media/private-link/private-link-view.png)

### <a name="delete"></a>Elimina

Gli endpoint privati possono essere eliminati solo da portale di Azure tramite il pannello panoramica (come indicato di seguito) o tramite l'opzione Elimina in rete (anteprima) la scheda "connessioni a endpoint privati". Se si fa clic sul pulsante Elimina, l'endpoint privato e la scheda di interfaccia di rete associata vengono eliminati. Se si eliminano tutti gli endpoint privati nella risorsa FHIR e l'accesso alla rete pubblica è disabilitato, nessuna richiesta lo farà al server FHIR.

![Elimina endpoint privato](media/private-link/private-link-delete.png)
