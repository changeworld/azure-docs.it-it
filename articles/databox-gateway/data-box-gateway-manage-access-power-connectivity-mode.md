---
title: Modalità di accesso, alimentazione e connettività del dispositivo Azure Data Box Gateway
description: Descrive come gestire l'accesso, l'avvio/arresto e la modalità di connessione per il dispositivo Azure Data Box Gateway che permette di trasferire dati in Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: c4e2894d193309c169adbea96491e0754d479a8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98786809"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Gestire l'accesso, l'alimentazione e la modalità di connettività per il Azure Data Box Gateway

Questo articolo descrive come gestire l'accesso, l'avvio/arresto e la modalità di connessione per Azure Data Box Gateway. Queste operazioni vengono eseguite tramite l'interfaccia utente Web locale o il portale di Azure. 

In questo articolo vengono illustrate le operazioni seguenti:

> [!div class="checklist"]
>
> * Gestire l'accesso al dispositivo
> * Gestire la modalità di connessione
> * Gestire l'avvio/arresto

## <a name="manage-device-access"></a>Gestire l'accesso al dispositivo

L'accesso al dispositivo Data Box Gateway è controllato dall'uso di una password del dispositivo. È possibile modificare la password tramite l'interfaccia utente Web locale. È anche possibile reimpostare la password del dispositivo nel portale di Azure.

### <a name="change-device-password"></a>Modificare la password del dispositivo

Per modificare la password del dispositivo, attenersi alla seguente procedura nell'interfaccia utente locale.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Modifica password**.
2. Immettere la password corrente e quindi quella nuova. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Confermare la nuova password.

    ![Cambia password](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Fare clic su **Cambia password**.
 
### <a name="reset-device-password"></a>Reimposta la password del dispositivo

Il flusso di lavoro di reimpostazione non richiede che l'utente ricordi la vecchia password ed è utile in caso di smarrimento della password. Questo flusso di lavoro viene eseguito nel portale di Azure.

1. Nel portale di Azure passare a **Panoramica > Reimposta password amministratore**.

    ![Reimposta password](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Immettere la nuova password e quindi confermarla. La password specificata deve essere costituita da 8-16 caratteri. La password deve contenere almeno tre di questi caratteri: lettera maiuscola, lettera minuscola, numero e caratteri speciali. Fare clic su **Reimposta**.

    ![Reimposta password 2](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Gestire l'accesso alle risorse

Per creare la Azure Data Box Gateway, l'hub Internet e la risorsa di archiviazione di Azure, è necessario disporre delle autorizzazioni come collaboratore o superiore a livello di gruppo di risorse. È anche necessario registrare i provider di risorse corrispondenti. Per tutte le operazioni che coinvolgono la chiave di attivazione e le credenziali, sono necessarie anche le autorizzazioni per Azure Active Directory API Graph. Questi elementi sono descritti nelle sezioni seguenti.

### <a name="manage-microsoft-graph-api-permissions"></a>Gestisci autorizzazioni API Microsoft Graph

Quando si genera la chiave di attivazione per il dispositivo o si eseguono operazioni che richiedono credenziali, è necessario disporre delle autorizzazioni per Microsoft Graph API. Le operazioni che richiedono credenziali potrebbero essere:

-  Creazione di una condivisione con un account di archiviazione associato.
-  Creazione di un utente che può accedere alle condivisioni nel dispositivo.

È necessario avere `User` accesso al tenant di Active Directory, in modo che sia possibile `Read all directory objects` . Un utente Guest non dispone delle autorizzazioni per `Read all directory objects` . Se si è un Guest, le operazioni come la generazione di una chiave di attivazione, la creazione di una condivisione nel dispositivo e la creazione di un utente avranno esito negativo.

Per altre informazioni su come fornire l'accesso agli utenti per Microsoft Graph API, vedere [Microsoft Graph le autorizzazioni di riferimento](/graph/permissions-reference).

### <a name="register-resource-providers"></a>Registrare i provider di risorse

Per eseguire il provisioning di una risorsa in Azure (nel modello di Azure Resource Manager), è necessario un provider di risorse che supporti la creazione di tale risorsa. Per eseguire il provisioning di una macchina virtuale, ad esempio, è necessario che nella sottoscrizione sia disponibile un provider di risorse "Microsoft. Compute".
 
I provider di risorse sono registrati a livello di sottoscrizione. Per impostazione predefinita, qualsiasi nuova sottoscrizione di Azure è già registrata con un elenco di provider di risorse usate comunemente. Il provider di risorse per ' Microsoft. DataBoxEdge ' non è incluso nell'elenco.

Non è necessario concedere le autorizzazioni di accesso a livello di sottoscrizione per consentire agli utenti di creare risorse come ' Microsoft. DataBoxEdge ' all'interno di gruppi di risorse di cui dispongono dei diritti di proprietario, purché i provider di risorse per queste risorse siano già registrati.

Prima di provare a creare una risorsa, verificare che il provider di risorse sia registrato nella sottoscrizione. Se il provider di risorse non è registrato, è necessario assicurarsi che l'utente che crea la nuova risorsa disponga di diritti sufficienti per registrare il provider di risorse richiesto a livello di sottoscrizione. Se questa operazione non è stata eseguita correttamente, verrà visualizzato l'errore seguente:

*La sottoscrizione \<Subscription name> non ha le autorizzazioni per registrare i provider di risorse: Microsoft. DataBoxEdge.*


Per ottenere un elenco dei provider di risorse registrati nella sottoscrizione corrente, eseguire il comando seguente:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Per un dispositivo di Data Box Gateway, `Microsoft.DataBoxEdge` deve essere registrato. Per `Microsoft.DataBoxEdge` eseguire la registrazione, l'amministratore della sottoscrizione deve eseguire il comando seguente:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Per altre informazioni su come registrare un provider di risorse, vedere [risolvere gli errori per la registrazione del provider di risorse](../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="manage-connectivity-mode"></a>Gestire la modalità di connessione

Oltre alla modalità normale predefinita, il dispositivo può essere eseguito anche in modalità parzialmente disconnessa o disconnessa:

- **Parzialmente disconnesso** : in questa modalità, il dispositivo non è in grado di caricare i dati nelle condivisioni. Tuttavia, può essere gestito tramite il portale di Azure.

    Questa modalità viene in genere usata per ridurre al minimo l'utilizzo della larghezza di banda di rete in una rete satellite a consumo. Può comunque avvenire un uso di rete minimo per le operazioni di monitoraggio del dispositivo.

- **Disconnesso** : in questa modalità il dispositivo è completamente disconnesso dal cloud e i download e i download del cloud sono disabilitati. Il dispositivo può essere gestito solo tramite l'interfaccia utente Web locale.

    Questa modalità viene usata in genere quando si vuole portare offline il dispositivo.

Per modificare la modalità del dispositivo, attenersi alla seguente procedura:

1. Nell'interfaccia utente Web locale del dispositivo passare a **Configurazione > Impostazioni del cloud**.
2. Disabilitare **Cloud upload and download** (Caricamento e download nel cloud).
3. Per eseguire il dispositivo in modalità parzialmente disconnessa, attivare **Azure portal management** (Gestione portale di Azure).

    ![Modalità di connessione](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Per eseguire il dispositivo in modalità parzialmente disconnessa, disabilitare **Azure portal management** (Gestione portale di Azure). A questo punto, il dispositivo può essere gestito solo tramite l'interfaccia utente Web locale.

    ![Modalità di connettività 2](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Gestire l'avvio/arresto

È possibile arrestare o riavviare il dispositivo virtuale tramite l'interfaccia utente Web locale. Prima di riavviare il dispositivo, è consigliabile portare offline le condivisioni nell'host e quindi nel dispositivo. Questa operazione riduce al minimo il rischio di danneggiamento dei dati.

1. Nell'interfaccia utente Web locale passare a **Manutenzione > Power settings** (Impostazioni di alimentazione).
2. Fare clic su **Arresta** o **Riavvia** a seconda di ciò che si desidera eseguire.

    ![Impostazioni di risparmio energia](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Quando viene richiesta una conferma, fare clic su **Sì** per continuare.

> [!NOTE]
> Se si arresta il dispositivo virtuale, è necessario avviare il dispositivo tramite la gestione dell'hypervisor.