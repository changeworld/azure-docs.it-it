---
title: 'Azure AD Connect: account del servizio ADSync | Microsoft Docs'
description: Questo argomento descrive l'account del servizio ADSync e fornisce le procedure consigliate per l'account.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/17/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca99a997d621bfd2455e909b36b6802775b20ac2
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106074608"
---
# <a name="adsync-service-account"></a>Account del servizio ADSync
Azure AD Connect installa un servizio locale che orchestra la sincronizzazione tra Active Directory e Azure Active Directory.  Il servizio di sincronizzazione Microsoft Azure AD sincronizzazione (ADSync) viene eseguito in un server nell'ambiente locale.  Le credenziali per il servizio sono impostate per impostazione predefinita nelle installazioni Express, ma possono essere personalizzate per soddisfare i requisiti di sicurezza dell'organizzazione.  Queste credenziali non vengono usate per la connessione alle foreste locali o Azure Active Directory.

La scelta dell'account del servizio ADSync è una decisione importante da prendere prima di installare Azure AD Connect.  Qualsiasi tentativo di modificare le credenziali dopo l'installazione comporterà il mancato avvio del servizio, la perdita dell'accesso al database di sincronizzazione e la mancata autenticazione con le directory connesse (Azure e AD DS).  Non verrà eseguita alcuna sincronizzazione fino al ripristino delle credenziali originali.

Il servizio di sincronizzazione può essere eseguito con account diversi, Può essere eseguito con un account del servizio virtuale (VSA), un account del servizio gestito (gMSA/sMSA) o un account utente normale. Le opzioni supportate sono state modificate con la versione di aprile 2017 e la versione di marzo 2021 di Azure AD Connect quando si esegue una nuova installazione. Se si esegue un aggiornamento da una versione precedente di Azure AD Connect, tali opzioni non sono disponibili. 


|Tipo di account|Opzione di installazione|Descrizione| 
|-----|------|-----|
|account Servizio virtuale|Rapida e personalizzata, aprile 2017 e versioni successive| Un account del servizio virtuale viene utilizzato per tutte le installazioni rapide, ad eccezione delle installazioni in un controller di dominio. Quando si usa l'installazione personalizzata, si tratta dell'opzione predefinita, a meno che non si usi un'altra opzione.| 
|Account del servizio gestito|Personalizzata, aprile 2017 e versioni successive|Se si usa un SQL Server remoto, è consigliabile usare un account del servizio gestito del gruppo. |
|Account del servizio gestito|Express e Custom, 2021 marzo e versioni successive|Un account del servizio gestito autonomo con prefisso ADSyncMSA_ viene creato durante l'installazione per le installazioni rapide quando viene installato in un controller di dominio. Quando si usa l'installazione personalizzata, si tratta dell'opzione predefinita, a meno che non si usi un'altra opzione.|
|Account utente|Express e Custom, 2017 da aprile a 2021 marzo|Un account utente con prefisso AAD_ viene creato durante l'installazione per le installazioni rapide quando viene installato in un controller di dominio. Quando si usa l'installazione personalizzata, si tratta dell'opzione predefinita, a meno che non si usi un'altra opzione.|
|Account utente|Rapida e personalizzata, marzo 2017 e versioni precedenti|Un account utente con prefisso AAD_ viene creato durante l'installazione per le installazioni rapide. Se si usa l'installazione personalizzata, è possibile specificare un altro account.| 

>[!IMPORTANT]
> Se si usa Connect con una build di marzo 2017 o precedente, non reimpostare la password nell'account del servizio poiché Windows elimina le chiavi di crittografia per motivi di sicurezza. Non è possibile modificare l'account impostandone un altro senza reinstallare Azure AD Connect. Se si esegue l'aggiornamento a una build da 2017 aprile o versione successiva, è supportata la modifica della password nell'account del servizio, ma non è possibile modificare l'account usato. 

> [!IMPORTANT]
> È possibile impostare solo l'account del servizio durante la prima installazione. Non è supportata la modifica dell'account del servizio dopo che l'installazione è stata completata. Se è necessario modificare la password dell'account del servizio, questa operazione è supportata e le istruzioni sono disponibili [qui](how-to-connect-sync-change-serviceacct-pass.md).

Di seguito è riportata una tabella delle opzioni predefinite, consigliate e supportate per l'account del servizio di sincronizzazione. 

Legenda: 

- **Bold** indica l'opzione predefinita e, nella maggior parte dei casi, l'opzione consigliata. 
- Il *corsivo* indica l'opzione consigliata quando non è l'opzione predefinita. 
- Non in grassetto: opzione supportata 
- Account locale: account utente locale sul server 
- Account di dominio: account utente di dominio 
- sMSA: [account del servizio gestito autonomo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd548356(v=ws.10))
- gMSA: [account del servizio gestito del gruppo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)) 

|Tipo di computer |**Database locale </br> Express**|**Database locale/LocalSQL </br> personalizzato**|**SQL </br> personalizzato remoto**|
|-----|-----|-----|-----|
|**computer aggiunto a un dominio**|**VSA**|**VSA**</br> *sMSA*</br> *gMSA*</br> Account locale</br> Account di dominio| *gMSA* </br>Account di dominio|
|Controller di dominio| **sMSA**|**sMSA** </br>*gMSA*</br> Account di dominio|*gMSA*</br>Account di dominio| 

## <a name="virtual-service-account"></a>account Servizio virtuale 

Un account del servizio virtuale è un tipo speciale di account locale gestito che non dispone di una password e viene gestito automaticamente da Windows. 

 ![Account del servizio virtuale](media/concept-adsync-service-account/account-1.png)

L'account del servizio virtuale deve essere utilizzato con scenari in cui il motore di sincronizzazione e SQL si trovano nello stesso server. Se si usa SQL remoto, è consigliabile usare invece un account del servizio gestito del gruppo. 

Impossibile utilizzare l'account del servizio virtuale in un controller di dominio a causa di problemi di [Windows Data Protection API (DPAPI)](https://msdn.microsoft.com/library/ms995355.aspx) . 

## <a name="managed-service-account"></a>Account del servizio gestito 

Se si usa un SQL Server remoto, è consigliabile usare un account del servizio gestito del gruppo. Per ulteriori informazioni su come preparare la Active Directory per l'account del servizio gestito del gruppo, vedere [Panoramica degli account del servizio gestiti del gruppo](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831782(v=ws.11)). 

Per usare questa opzione, nella pagina [Installazione dei componenti necessari](how-to-connect-install-custom.md#install-required-components) selezionare **Usa un account del servizio esistente** e quindi **Account del servizio gestito**. 

 ![account del servizio gestito](media/concept-adsync-service-account/account-2.png)

L'opzione è supportata anche per l'uso di un account del servizio gestito autonomo. Tuttavia, questi possono essere usati solo sul computer locale e non vi è alcun vantaggio di usarli sull'account del servizio virtuale predefinito. 

### <a name="auto-generated-standalone-managed-service-account"></a>Account del servizio gestito autonomo generato automaticamente 

Se si installa Azure AD Connect in un controller di dominio, tramite l'installazione guidata viene creato un account del servizio gestito autonomo (a meno che non si specifichi l'account da usare nelle impostazioni personalizzate). L'account è preceduto **ADSyncMSA_** e utilizzato per l'esecuzione del servizio di sincronizzazione effettivo. 

Questo account è un account di dominio gestito che non dispone di una password e viene gestito automaticamente da Windows. 

Questo account deve essere usato con scenari in cui il motore di sincronizzazione e SQL si trovano nel controller di dominio. 

## <a name="user-account"></a>Account utente 

Mediante l'installazione guidata viene creato un account di servizio locale (a meno che non si specifichi l'account da usare nelle impostazioni personalizzate). L'account, preceduto da AAD_ , viene usato per l'esecuzione del servizio di sincronizzazione effettivo. Se si installa Azure AD Connect in un Controller di dominio, l'account viene creato nel dominio. L'account del servizio AAD_ deve essere presente nel dominio se: 
- si usa un server remoto che esegue SQL Server 
- si usa un proxy che richiede l'autenticazione 

 ![account utente](media/concept-adsync-service-account/account-3.png)

L'account viene creato con una password lunga e complessa priva di scadenza. 

Questo account viene usato per archiviare in modo sicuro le password per gli altri account. Le password di questi altri account vengono archiviate crittografate nel database. Le chiavi private per le chiavi di crittografia sono protette tramite la crittografia a chiave segreta dei servizi di crittografia con Data Protection API (DPAPI) di Windows. 

Se si usa una versione completa di SQL Server, l'account del servizio corrisponde al DBO del database creato per il motore di sincronizzazione. Il servizio non funzionerà come previsto con qualsiasi altra autorizzazione. Viene inoltre creato l'accesso a SQL. 

All'account viene inoltre concessa l'autorizzazione per file, chiavi del registro di sistema e altri oggetti correlati al motore di sincronizzazione. 


## <a name="next-steps"></a>Passaggi successivi
Altre informazioni su [Integrazione delle identità locali con Azure Active Directory](whatis-hybrid-identity.md).
