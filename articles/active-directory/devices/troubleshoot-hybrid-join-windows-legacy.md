---
title: Risolvere i problemi relativi ai dispositivi ibridi legacy Azure Active Directory aggiunti
description: Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbf936370b401a76479c7679499103d083e0752a
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551748"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Risoluzione dei problemi relativi a dispositivi di livello inferiore aggiunti all'identità ibrida di Azure Active Directory 

Questo articolo è applicabile solo ai dispositivi seguenti: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 

Per Windows 10 o Windows Server 2016, vedere [Risoluzione dei problemi relativi a dispositivi Windows 10 e Windows Server 2016 aggiunti all'identità ibrida di Azure Active Directory](troubleshoot-hybrid-join-windows-current.md).

Questo articolo presuppone che siano stati [configurati dispositivi aggiunti all'identità ibrida di Azure Active Directory](hybrid-azuread-join-plan.md) per supportare gli scenari seguenti:

- Accesso condizionale basato sul dispositivo

Questo articolo fornisce indicazioni sulla risoluzione di potenziali problemi.  

**Cosa è necessario sapere:** 

- L'aggiunta ad Azure AD ibrido per i dispositivi Windows di livello inferiore funziona in modo leggermente diverso rispetto a Windows 10. Molti clienti non si rendono conto di dover configurare AD FS (per i domini federati) o Seamless SSO (per i domini gestiti).
- L'accesso SSO facile non funziona in modalità di esplorazione privata nei browser Firefox e Microsoft Edge. Inoltre, non funziona in Internet Explorer se il browser è in esecuzione in modalità protetta avanzata o se è abilitata la configurazione della sicurezza avanzata.
- Per i clienti con domini federati, se il punto di connessione del servizio (SCP) è stato configurato in modo da puntare al nome del dominio gestito (ad esempio, contoso.onmicrosoft.com invece di contoso.com), l'aggiunta ad Azure AD ibrido per i dispositivi Windows di livello inferiore non funzionerà.
- Lo stesso dispositivo fisico viene visualizzato più volte in Azure AD quando più utenti di dominio accedono ai dispositivi di livello inferiore aggiunti ad Azure AD ibrido.  Ad esempio: se *jdoe* e *jharnett* accedono a questo dispositivo, viene creata una registrazione separata (DeviceID) per ciascuno di questi utenti nella scheda di informazioni **UTENTE**. 
- È anche possibile ottenere più voci per un dispositivo nella scheda delle informazioni sull'utente in seguito a una reinstallazione del sistema operativo o a una nuova registrazione manuale.
- La registrazione/aggiunta iniziale dei dispositivi è configurata in modo da eseguire un tentativo di accesso o blocco/sblocco. Potrebbero esserci 5 minuti di ritardo causati da un'attività dell'utilità di pianificazione. 
- Nel caso di Windows 7 SP1 o Windows Server 2008 R2 SP1 verificare che l'aggiornamento [KB4284842](https://support.microsoft.com/help/4284842) sia installato. Questo aggiornamento impedisce che si verifichino errori di autenticazione futuri a causa della perdita di accesso del cliente alle chiavi protette dopo la modifica della password.
- Azure AD ibrido join potrebbe non riuscire dopo che un utente ha modificato l'UPN, suddividendo il processo di autenticazione SSO semplice. Durante il processo di join si può notare che sta ancora inviando l'UPN precedente a Azure AD, a meno che i cookie della sessione del browser non siano cancellati o l'utente non esegua esplicitamente la disattivazione e rimuove il vecchio UPN.

## <a name="step-1-retrieve-the-registration-status"></a>Passaggio 1: Recuperare lo stato della registrazione 

**Per verificare lo stato della registrazione**  

1. Accedere con l'account utente che ha eseguito l'aggiunta all'identità ibrida di Azure AD.
1. Aprire il prompt dei comandi 
1. Digitare `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Questo comando consente di visualizzare una finestra di dialogo che fornisce dettagli relativi allo stato delle aggiunte.

:::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/01.png" alt-text="Screenshot della finestra di dialogo Workplace Join for Windows. Il testo che include un indirizzo di posta elettronica indica che un determinato dispositivo è stato aggiunto a un'area di lavoro." border="false":::

## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Passaggio 2: Valutare lo stato delle aggiunte all'identità ibrida di Azure AD 

Se il dispositivo non è stato aggiunto a Azure AD in modalità ibrida, è possibile provare a eseguire l'aggiunta ad Azure AD ibrido facendo clic sul pulsante "Join". Se il tentativo di eseguire operazioni di aggiunta ad Azure AD ibrido non riesce, verranno visualizzati i dettagli sull'errore.

**I problemi più comuni sono:**

- Una configurazione errata di AD FS o Azure AD o problemi di rete

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/02.png" alt-text="Screenshot della finestra di dialogo Workplace Join for Windows. Testo indica che si è verificato un errore durante l'autenticazione dell'account." border="false":::
    
   - Autoworkplace.exe non è in grado di eseguire automaticamente l'autenticazione con Azure AD o AD FS. Ciò potrebbe essere causato dai servizi AD FS mancanti o non configurati correttamente (per i domini federati) o dall'accesso Single Sign-On facile di Azure AD mancante o non configurato correttamente (per i domini gestiti) o da problemi di rete. 
   - È possibile che multi-factor authentication sia abilitato/configurato per l'utente e WIAORMULTIAUTHN alla non sia configurato nel server AD FS. 
   - È anche possibile che la pagina di individuazione dell'area di autenticazione principale sia in attesa dell'interazione dell'utente, impedendo ad **autoworkplace.exe** di richiedere automaticamente un token.
   - Gli URL di AD FS e di Azure AD potrebbero non essere presenti nell'area intranet di Internet Explorer sul client.
   - Problemi di connettività di rete potrebbero impedire ad **autoworkplace.exe** di raggiungere gli URL di AD FS o di Azure AD. 
   - **Autoworkplace.exe** richiede che il client abbia una linea di controllo diretta dal client al controller di dominio ad locale dell'organizzazione, il che significa che il join Azure ad ibrido riesce solo quando il client è connesso alla rete Intranet dell'organizzazione.
   - L'organizzazione usa il Single Sign-On di Azure AD, `https://autologon.microsoftazuread-sso.com` o `https://aadg.windows.net.nsatc.net` non sono presenti nelle impostazioni Intranet di IE del dispositivo e l'opzione **Consenti aggiornamenti alla barra di stato tramite script** non è abilitata per l'area Intranet.
- Non si è connessi come utente di dominio

   :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/03.png" alt-text="Screenshot della finestra di dialogo Workplace Join for Windows. Testo indica che si è verificato un errore durante la verifica dell'account." border="false":::

   Questo problema può verificarsi per diversi motivi:

   - L'utente che ha eseguito l'accesso non è un utente di dominio, ad esempio è un utente locale. L'aggiunta all'identità ibrida di Azure AD su dispositivi di livello inferiore è supportata solo per utenti di dominio.
   - Il client non riesce a connettersi a un controller di dominio.    
- È stata raggiunta una quota

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/04.png" alt-text="Screenshot della finestra di dialogo Workplace Join for Windows. Il testo segnala un errore perché l'utente ha raggiunto il numero massimo di dispositivi aggiunti." border="false":::

- Il servizio non risponde 

    :::image type="content" source="./media/troubleshoot-hybrid-join-windows-legacy/05.png" alt-text="Screenshot della finestra di dialogo Workplace Join for Windows. Testo indica che si è verificato un errore perché il server non ha risposto." border="false":::

È inoltre possibile trovare le informazioni sullo stato nel registro eventi in **Registri applicazioni e servizi\Microsoft-Workplace Join**
  
**Le cause più comuni di un'aggiunta all'identità ibrida di Azure AD non riuscita sono:** 

- Il computer non è connesso alla rete interna dell'organizzazione, né a una VPN con connessione al controller di dominio AD locale.
- Si è connessi al computer con un account computer locale. 
- Problemi di configurazione del servizio: 
   - Il server AD FS non è stato configurato per supportare **WIAORMULTIAUTHN**. 
   - Nella foresta del computer non è presente alcun oggetto Punto di connessione del servizio che punti al nome di dominio verificato in Azure AD 
   - Se invece il dominio è gestito, Seamless SSO non è stato configurato o non funziona.
   - Un utente ha raggiunto il limite di dispositivi. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere le [domande frequenti sulla gestione dei dispositivi](faq.yml)  
