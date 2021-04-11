---
title: includere file
description: includere file
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a142de62f1ab7046d47c29753863b16864d97536
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075587"
---
Le chiavi DEK del servizio vengono utilizzate per crittografare dati riservati dei clienti, ad esempio credenziali di account di archiviazione, che vengono inviate dal servizio StorSimple Manager al dispositivo StorSimple. Occorre modificare queste chiavi periodicamente, se l'organizzazione IT dispone di un criterio di rotazione delle chiavi nei dispositivi di archiviazione. Il processo di modifica della chiave può essere leggermente diverso a seconda che ci sia una o più dispositivi gestiti dal servizio StorSimple Manager. Per altre informazioni, vedere [Sicurezza e protezione dei dati di StorSimple](../articles/storsimple/storsimple-8000-security.md).

La modifica della chiave DEK del servizio è un processo che prevede 3 fasi:

1. Usando gli script di Windows PowerShell per Azure Resource Manager, autorizzare un dispositivo a modificare la chiave DEK del servizio.
2. Mediante Windows PowerShell per StorSimple, si consente di avviare la modifica della chiave DEK del servizio.
3. Se si dispone di più di un dispositivo StorSimple, è necessario aggiornare la chiave DEK del servizio in altri dispositivi.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Passaggio 1: Usare uno script di Windows PowerShell per autorizzare un dispositivo a modificare la chiave DEK del servizio
In genere, l'amministratore dei dispositivi richiede all'amministratore del servizio di autorizzare un dispositivo a modificare le chiavi DEK del servizio. L'amministratore del servizio autorizza quindi il dispositivo a modificare la chiave.

Questo passaggio viene eseguito usando lo script basato su Azure Resource Manager. L'amministratore del servizio può selezionare un dispositivo idoneo per l'autorizzazione. Il dispositivo viene quindi autorizzato ad avviare il processo di modifica della chiave DEK del servizio. 

Per altre informazioni sull'uso dello script, vedere [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Quali dispositivi possono essere autorizzati a modificare le chiavi DEK del servizio?
Per essere autorizzato ad avviare le modifiche alla chiave DEK del servizio, un dispositivo deve soddisfare i criteri seguenti:

* Il dispositivo deve essere online per essere idoneo per l'autorizzazione di modifica della chiave DEK del servizio.
* È possibile autorizzare di nuovo lo stesso dispositivo dopo 30 minuti se la modifica della chiave non è stata avviata.
* È possibile autorizzare un dispositivo diverso, purché la modifica della chiave non sia stata avviata dal dispositivo autorizzato in precedenza. Dopo che il nuovo dispositivo è stato autorizzato, il dispositivo precedente non può avviare la modifica.
* Non è possibile autorizzare un dispositivo mentre è in corso il rollover della chiave DEK del servizio.
* È possibile autorizzare un dispositivo quando alcuni dei dispositivi registrati con il servizio hanno eseguito il rollover della crittografia mentre altri no. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Passaggio 2: Usare Windows PowerShell per StorSimple per avviare la modifica della chiave DEK del servizio
Questo passaggio viene eseguito nell'interfaccia di Windows PowerShell per StorSimple nel dispositivo StorSimple autorizzato.

> [!NOTE]
> Non è possibile eseguire operazioni nel portale di Azure del servizio StorSimple Manager fino a quando il rollover della chiave non viene completato.


Se si usa la console seriale del dispositivo per la connessione all'interfaccia di Windows PowerShell,seguire questa procedura.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Per avviare la modifica della chiave DEK del servizio
1. Selezionare l'opzione 1 per eseguire l'accesso completo.
2. Al prompt dei comandi digitare:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Dopo che il cmdlet è stato completato, si otterrà una nuova chiave DEK del servizio. Copiare e salvare la chiave per l'uso nel passaggio 3 di questo processo. Questa chiave verrà usata per aggiornare tutti i dispositivi rimanenti registrati con il servizio StorSimple Manager.
   
   > [!NOTE]
   > Questo processo deve essere avviato entro quattro ore dall'autorizzazione di un dispositivo StorSimple.
   > 
   > 
   
   La nuova chiave viene quindi inviata al servizio affinché ne venga effettuato il push a tutti i dispositivi registrati con il servizio. Nel dashboard del servizio verrà visualizzato un avviso. Il servizio disabiliterà tutte le operazioni nei dispositivi registrati e l'amministratore dei dispositivi dovrà quindi aggiornare la chiave DEK del servizio negli altri dispositivi. Tuttavia, i flussi di I/O (invio di dati dagli host al cloud) non verranno interrotti.
   
   Se nel servizio è registrato un unico dispositivo, il processo di rollover è completo ed è possibile ignorare il passaggio successivo. Se nel servizio sono registrati più dispositivi, andare al passaggio 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Passaggio 3: Aggiornare la chiave DEK del servizio in altri dispositivi StorSimple
Questa procedura deve essere eseguita nell'interfaccia di Windows PowerShell del dispositivo StorSimple se vi sono più dispositivi registrati per il servizio StorSimple Manager. La chiave ottenuta nel passaggio 2 deve essere usata per aggiornare tutti i dispositivi StorSimple rimanenti registrati con il servizio StorSimple Manager.

Eseguire i passaggi seguenti per aggiornare la chiave DEK del servizio nel dispositivo.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Per aggiornare la chiave di crittografia dei dati del servizio nei dispositivi fisici
1. Usare Windows PowerShell per StorSimple per connettersi alla console. Selezionare l'opzione 1 per eseguire l'accesso completo.
2. Al prompt dei comandi digitare: `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Specificare la chiave DEK ottenuta nel [Passaggio 2: Usare Windows PowerShell per StorSimple per avviare la modifica della chiave DEK del servizio](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Per aggiornare la chiave di crittografia dei dati del servizio in tutte le appliance cloud 8010/8020
1. Scaricare e configurare lo script di PowerShell [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1). 
2. Aprire PowerShell e al prompt dei comandi digitare: `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Questo script garantisce che la chiave di crittografia dei dati del servizio sia configurata in tutte le appliance cloud 8010/8020 nel servizio Gestione dispositivi.