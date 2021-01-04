---
title: 'Azure AD Connect agente di provisioning cloud: gestire le opzioni del registro di sistema | Microsoft Docs'
description: Questo articolo descrive come gestire le opzioni del registro di sistema nell'agente di provisioning Azure AD Connect Cloud.
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/11/2020
ms.subservice: hybrid
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: edb602e3d55ae07f49d5448283ae0d2b6da4b0cb
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2020
ms.locfileid: "97694157"
---
# <a name="manage-agent-registry-options"></a>Opzioni di gestione del registro di sistema dell'agente

In questa sezione vengono descritte le opzioni del registro di sistema che è possibile impostare per controllare il comportamento di elaborazione di runtime dell'agente di provisioning di Azure AD Connect. 

## <a name="configure-ldap-connection-timeout"></a>Configurare il timeout della connessione LDAP
Quando si eseguono operazioni LDAP su controller di dominio configurati Active Directory, per impostazione predefinita l'agente di provisioning usa il valore di timeout di connessione predefinito di 30 secondi. Se il controller di dominio impiega più tempo per rispondere, è possibile che venga visualizzato il messaggio di errore seguente nel file di log dell'agente: 

`
System.DirectoryServices.Protocols.LdapException: The operation was aborted because the client side timeout limit was exceeded.
`

Le operazioni di ricerca LDAP possono richiedere più tempo se l'attributo Search non è indicizzato. Come primo passaggio, se si riceve l'errore precedente, verificare prima di tutto se l'attributo Search/Lookup è [indicizzato](https://docs.microsoft.com/windows/win32/ad/indexed-attributes). Se gli attributi di ricerca sono indicizzati e l'errore viene mantenuto, è possibile aumentare il timeout della connessione LDAP attenendosi alla procedura seguente: 

1. Accedere come amministratore nel server Windows che esegue l'agente di provisioning di Azure AD Connect.
1. Utilizzare la voce di menu *Esegui* per aprire l'editor del registro di sistema (regedit.exe) 
1. Individuare la cartella della chiave **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Fare clic con il pulsante destro del mouse e scegliere "nuovo-> valore stringa"
1. Specificare il nome: `LdapConnectionTimeoutInMilliseconds`
1. Fare doppio clic sul **nome del valore** e immettere i dati del valore in `60000` millisecondi.
    > [!div class="mx-imgBorder"]
    > ![Timeout connessione LDAP](media/how-to-manage-registry-options/ldap-connection-timeout.png)
1. Riavviare il servizio di provisioning Azure AD Connect dalla console *dei servizi* .
1. Se sono stati distribuiti più agenti di provisioning, applicare questa modifica al registro di sistema a tutti gli agenti per verificarne la coerenza. 

## <a name="configure-referral-chasing"></a>Configurare l'inseguimento del riferimento
Per impostazione predefinita, l'agente di provisioning di Azure AD Connect non insegue i [riferimenti](https://docs.microsoft.com/windows/win32/ad/referrals). Potrebbe essere necessario abilitare la ricerca dei riferimenti per supportare determinati scenari di provisioning in ingresso HR, ad esempio: 
* Verifica dell'univocità dell'UPN tra più domini
* Risoluzione dei riferimenti a gestione tra domini

Usare la procedura seguente per attivare la ricerca dei riferimenti:

1. Accedere come amministratore nel server Windows che esegue l'agente di provisioning di Azure AD Connect.
1. Utilizzare la voce di menu *Esegui* per aprire l'editor del registro di sistema (regedit.exe) 
1. Individuare la cartella della chiave **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Fare clic con il pulsante destro del mouse e scegliere "nuovo-> valore stringa"
1. Specificare il nome: `ReferralChasingOptions`
1. Fare doppio clic sul **nome del valore** e immettere i dati del valore come `96` . Questo valore corrisponde al valore costante per `ReferralChasingOptions.All` e specifica che il sottoalbero e i riferimenti a livello di base saranno seguiti dall'agente. 
    > [!div class="mx-imgBorder"]
    > ![Chasing di riferimento](media/how-to-manage-registry-options/referral-chasing.png)
1. Riavviare il servizio di provisioning Azure AD Connect dalla console *dei servizi* .
1. Se sono stati distribuiti più agenti di provisioning, applicare questa modifica al registro di sistema a tutti gli agenti per verificarne la coerenza.

## <a name="skip-gmsa-configuration"></a>Ignora la configurazione di GMSA
Con Agent Version 1.1.281.0 +, per impostazione predefinita, quando si esegue la configurazione guidata agente, viene richiesto di configurare l' [account del servizio gestito del gruppo (gMSA)](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview). Il programma di installazione di GMSA tramite la procedura guidata viene usato in fase di esecuzione per tutte le operazioni di sincronizzazione e provisioning. 

Se si esegue l'aggiornamento da una versione precedente dell'agente ed è stato configurato un account del servizio personalizzato con autorizzazioni a livello di unità organizzativa delegate specifiche per la topologia di Active Directory, è possibile ignorare o posticipare la configurazione di GMSA e pianificare questa modifica. 

> [!NOTE]
> Queste linee guida si applicano in modo specifico ai clienti che hanno configurato il provisioning in ingresso HR (giorno lavorativo/SuccessFactors) con le versioni dell'agente precedenti a 1.1.281.0 e hanno configurato un account del servizio personalizzato per le operazioni dell'agente. A lungo termine, è consigliabile passare a GMSA come procedura consigliata.  

In questo scenario è comunque possibile aggiornare i file binari dell'agente e ignorare la configurazione di GMSA attenendosi alla procedura seguente: 

1. Accedere come amministratore nel server Windows che esegue l'agente di provisioning di Azure AD Connect.
1. Eseguire il programma di installazione dell'agente per installare i nuovi file binari dell'agente. Chiudere la configurazione guidata agente che viene aperta automaticamente al termine dell'installazione. 
1. Utilizzare la voce di menu *Esegui* per aprire l'editor del registro di sistema (regedit.exe) 
1. Individuare la cartella della chiave **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure AD Connect Agents\Azure AD Connect Provisioning Agent**
1. Fare clic con il pulsante destro del mouse e scegliere "nuovo-> valore DWORD"
1. Specificare il nome: `UseCredentials`
1. Fare doppio clic sul **nome del valore** e immettere i dati del valore come `1` .  
    > [!div class="mx-imgBorder"]
    > ![Usa credenziali](media/how-to-manage-registry-options/use-credentials.png)
1. Riavviare il servizio di provisioning Azure AD Connect dalla console *dei servizi* .
1. Se sono stati distribuiti più agenti di provisioning, applicare questa modifica al registro di sistema a tutti gli agenti per verificarne la coerenza.
1. Dal desktop breve eseguire la configurazione guidata agente. La configurazione GMSA verrà ignorata dalla procedura guidata. 


> [!NOTE]
> È possibile verificare che le opzioni del registro di sistema siano state impostate abilitando la [registrazione dettagliata](how-to-troubleshoot.md#log-files). I log generati durante l'avvio dell'agente visualizzeranno i valori di configurazione scelti dal registro di sistema. 

## <a name="next-steps"></a>Passaggi successivi 

- [Cos'è il provisioning?](what-is-provisioning.md)
- [Che cos'è il provisioning cloud di Azure AD Connect?](what-is-cloud-provisioning.md)

