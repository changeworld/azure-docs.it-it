---
title: Che cos'è il rischio? Azure AD Identity Protection
description: Spiegazione dei rischi in Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87516ddcce32ab205b13139c057a2ab999146b74
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376359"
---
# <a name="what-is-risk"></a>Che cos'è il rischio?

I rilevamenti dei rischi Azure AD Identity Protection includono eventuali azioni sospette identificate correlate agli account utente nella directory.

Identity Protection consente alle organizzazioni di accedere a risorse potenti per visualizzare e rispondere rapidamente a queste azioni sospette. 

![Panoramica della sicurezza che mostra gli utenti e gli accesso a rischio](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

> [!NOTE]
> Identity Protection genera rilevamenti dei rischi solo quando vengono usate le credenziali corrette. Se per un accesso vengono usate credenziali non corrette, non rappresenta il rischio di compromissione delle credenziali.

## <a name="risk-types-and-detection"></a>Tipi di rischio e rilevamento

Esistono due tipi di rischio **Utente** e **Accesso** e due tipi di rilevamento o calcolo **in tempo reale** e **Offline.**

I rilevamenti in tempo reale potrebbero non essere visualizzati nei report per cinque-dieci minuti. I rilevamenti offline potrebbero non essere visualizzati nei report per 2-24 ore.

### <a name="user-risk"></a>Rischio utente

Un rischio utente rappresenta la probabilità che una determinata identità o account venga compromessa. 

Questi rischi vengono calcolati offline usando le origini di intelligence per le minacce interne ed esterne di Microsoft, tra cui ricercatori della sicurezza, professionisti delle forze dell'ordine, team di sicurezza di Microsoft e altre fonti attendibili.

| Rilevamento dei rischi | Descrizione |
| --- | --- |
| Credenziali perse | Questo tipo di rilevamento dei rischi indica che le credenziali valide dell'utente sono state perse. Quando i criminali informatici compromettono password valide di utenti legittimi, spesso condividono tali credenziali, Questa condivisione viene in genere eseguita pubblicando pubblicamente sul Web scuro, incollando siti o scambiando e vendendo le credenziali sul mercato nero. Quando il servizio Microsoft leaked credentials acquisisce le credenziali utente dal Web scuro, dai siti incollati o da altre origini Azure AD, viene controllato in base alle credenziali valide correnti degli utenti per trovare corrispondenze valide. Per altre informazioni sulle credenziali perse, vedere [Domande comuni.](#common-questions) |
| Intelligence per le minacce di Azure AD | Questo tipo di rilevamento dei rischi indica un'attività utente insolita per l'utente specificato o coerente con i modelli di attacco noti basati sulle origini di intelligence per le minacce interne ed esterne di Microsoft. |

### <a name="sign-in-risk"></a>Rischio di accesso

Un rischio di accesso rappresenta la probabilità che una determinata richiesta di autenticazione non sia autorizzata dal proprietario dell'identità. 

Questi rischi possono essere calcolati in tempo reale o offline usando le fonti di intelligence per le minacce interne ed esterne di Microsoft, tra cui ricercatori della sicurezza, professionisti delle forze dell'ordine, team di sicurezza microsoft e altre fonti attendibili.

| Rilevamento dei rischi | Tipo di rilevamento | Descrizione |
| --- | --- | --- |
| Indirizzo IP anonimo | Tempo reale | Questo tipo di rilevamento dei rischi indica gli account di accesso da un indirizzo IP anonimo, ad esempio tor browser o VPN anonima. Questi indirizzi IP sono in genere usati da attori che vogliono nascondere i dati di telemetria relativi agli accessi (indirizzo IP, posizione, dispositivo e così via) con intenzioni potenzialmente dannose. |
| Trasferimento atipico | Offline | Questo tipo di rilevamento dei rischi identifica due account di accesso provenienti da posizioni geograficamente distanti, in cui almeno una delle posizioni può essere atipica anche per l'utente, in base a un comportamento passato. Oltre ad altri fattori, l'algoritmo di apprendimento automatico prende in considerazione il tempo che intercorre tra i due accessi e il tempo necessario all'utente per spostarsi dalla prima posizione alla seconda, che indica che un altro utente sta usando le stesse credenziali. <br><br> L'algoritmo ignora i "falsi positivi" evidenti che contribuiscono alle condizioni di impossibilità del trasferimento, ad esempio le VPN e le posizioni usate regolarmente da altri utenti nell'organizzazione. Il sistema ha un periodo di apprendimento iniziale di 14 giorni o 10 accessi, durante i quali apprende il comportamento di accesso di un nuovo utente. |
| Indirizzo IP collegato a malware | Offline | Questo tipo di rilevamento dei rischi indica gli account di accesso da indirizzi IP infetti da malware noto per comunicare attivamente con un server bot. Questo rilevamento è determinato dalla correlazione degli indirizzi IP del dispositivo dell'utente con gli indirizzi IP che erano in contatto con un server bot mentre il server bot era attivo. |
| Proprietà di accesso insolite | Tempo reale | Questo tipo di rilevamento dei rischi considera la cronologia di accesso passata (IP, latitudine/longitudine e ASN) per cercare gli errori di accesso anomali. Il sistema archivia le informazioni sulle posizioni precedenti usate da un utente e considera tali posizioni "familiari". Il rilevamento dei rischi viene attivato quando si verifica l'accesso da una posizione che non è già presente nell'elenco di località familiari. Gli utenti appena creati saranno in "modalità di apprendimento" per un periodo di tempo in cui i rilevamenti dei rischi delle proprietà di accesso non familiari verranno disattivati mentre gli algoritmi apprendono il comportamento dell'utente. La durata della modalità di apprendimento è dinamica e dipende dal tempo necessario all'algoritmo per raccogliere informazioni sufficienti sui modelli di accesso dell'utente. La durata minima è di cinque giorni. Un utente può tornare in modalità di apprendimento dopo un lungo periodo di inattività. Il sistema ignora anche gli accessi da dispositivi noti e le posizioni geograficamente vicine a una posizione nota. <br><br> È anche possibile eseguire questo rilevamento per l'autenticazione di base (o i protocolli legacy). Poiché questi protocolli non hanno proprietà moderne, ad esempio l'ID client, i dati di telemetria per ridurre i falsi positivi sono limitati. È consigliabile passare all'autenticazione moderna. |
| L'amministratore ha confermato che l'utente è compromesso | Offline | Questo rilevamento indica che un amministratore ha selezionato "Conferma utente compromesso" nell'interfaccia utente degli utenti a rischio o usando l'API riskyUsers. Per vedere quale amministratore ha confermato che l'utente è stato compromesso, controllare la cronologia dei rischi dell'utente (tramite l'interfaccia utente o l'API). |
| Indirizzo IP dannoso | Offline | Questo rilevamento indica l'accesso da un indirizzo IP dannoso. Un indirizzo IP viene considerato dannoso in base a frequenze di errore elevate a causa di credenziali non valide ricevute dall'indirizzo IP o da altre origini di reputazione IP. |
| Regole sospette di manipolazione della Posta in arrivo | Offline | Questo rilevamento viene individuato da [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Questo criterio di rilevamento profila l'ambiente e genera avvisi quando nella Posta in arrivo di un utente vengono configurate regole sospette per eliminare o spostare messaggi o cartelle. Questo rilevamento può indicare che l'account dell'utente è compromesso, che i messaggi vengono intenzionalmente nascosti e che la cassetta postale viene usata per distribuire posta indesiderata o malware nell'organizzazione. |
| Password spraying | Offline | Un attacco di tipo password spray è il caso in cui più nomi utente vengono aggrediti usando password comuni in modo di forza bruta unificata per ottenere l'accesso non autorizzato. Questo rilevamento dei rischi viene attivato quando è stato eseguito un attacco di tipo password spray. |
| Comunicazione impossibile | Offline | Questo rilevamento viene individuato da [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#impossible-travel). Questo rilevamento identifica due attività utente (in una singola sessione o in più sessioni) provenienti da località geograficamente distanti e realizzate in un intervallo di tempo più breve rispetto a quello che sarebbe stato necessario all'utente per spostarsi dalla prima alla seconda località. Si tratta pertanto di un altro utente che sta usando le stesse credenziali. |
| Nuovo paese | Offline | Questo rilevamento viene individuato da [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-infrequent-country). Questo rilevamento prende in considerazione le località di attività passate per determinare località nuove e non frequenti. Il motore di rilevamento delle anomalie archivia le informazioni sulle località precedenti usate dagli utenti dell'organizzazione. |
| Attività da indirizzi IP anonimi | Offline | Questo rilevamento viene individuato da [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#activity-from-anonymous-ip-addresses). Questo rilevamento indica che erano presenti utenti attivi da un indirizzo IP identificato come indirizzo IP proxy anonimo. |
| Inoltro sospetto della Posta in arrivo | Offline | Questo rilevamento viene individuato da [Microsoft Cloud App Security (MCAS)](/cloud-app-security/anomaly-detection-policy#suspicious-inbox-forwarding). Questo rilevamento consente di rilevare regole di inoltro della posta elettronica sospette, ad esempio se un utente ha creato una regola per la posta in arrivo che inoltra una copia di tutti i messaggi di posta elettronica a un indirizzo esterno. |

### <a name="other-risk-detections"></a>Altri rilevamenti dei rischi

| Rilevamento dei rischi | Tipo di rilevamento | Descrizione |
| --- | --- | --- |
| Rischio aggiuntivo rilevato | In tempo reale o offline | Questo rilevamento indica che è stato rilevato uno dei rilevamenti Premium precedenti. Poiché i rilevamenti Premium sono visibili solo Azure AD Premium clienti P2, sono intesi come "rischio aggiuntivo rilevato" per i clienti senza Azure AD Premium licenze P2. |

## <a name="common-questions"></a>Domande frequenti

### <a name="risk-levels"></a>Livelli di rischio

Identity Protection classifica il rischio in tre livelli: basso, medio e alto. Quando si [configurano criteri di Protezione delle identità](./concept-identity-protection-policies.md#custom-conditional-access-policy)personalizzati, è anche possibile configurarlo per l'attivazione su Nessun livello **di** rischio. Nessun rischio indica che l'identità dell'utente è stata compromessa.

Microsoft non fornisce dettagli specifici sul modo in cui viene calcolato il rischio, ma ogni livello comporta una maggiore probabilità che l'utente o l'accesso sia compromesso. Ad esempio, un'istanza di proprietà di accesso insolite per un utente potrebbe non essere pericolosa quanto le credenziali perse per un altro utente.

### <a name="password-hash-synchronization"></a>Sincronizzazione dell'hash delle password

I rilevamenti di rischi come le credenziali perse richiedono la presenza di hash delle password per il rilevamento. Per altre informazioni sulla sincronizzazione dell'hash delle password, vedere l'articolo Implementare la sincronizzazione [dell'hash delle password con Azure AD Connect sincronizzazione.](../hybrid/how-to-connect-password-hash-synchronization.md)

### <a name="leaked-credentials"></a>Credenziali perse

#### <a name="where-does-microsoft-find-leaked-credentials"></a>Dove si trovano le credenziali perse da Microsoft?

Microsoft rileva le credenziali perse in un'ampia gamma di posizioni, tra cui:

- Siti di incolla pubblici, ad esempio pastebin.com e paste.ca in cui gli attori malinteniti pubblicano in genere tale materiale. Questa posizione è la prima volta che gli attori malintini si fermano alla ricerca di credenziali rubate.
- Forze.
- Altri gruppi di Microsoft che fanno ricerche sul Dark Web.

#### <a name="why-arent-i-seeing-any-leaked-credentials"></a>Perché non vengono visualizzate credenziali perse?

Credenziali perse vengono elaborati ogni volta che Microsoft trova un nuovo batch disponibile pubblicamente. A causa della natura sensibile, le credenziali perse vengono eliminate poco dopo l'elaborazione. Solo le nuove credenziali perse trovate dopo l'abilitazione della sincronizzazione dell'hash delle password (PHS) verranno elaborate nel tenant. La verifica delle coppie di credenziali individuate in precedenza non viene eseguita. 

#### <a name="i-havent-seen-any-leaked-credential-risk-events-for-quite-some-time"></a>Non ho visto eventi di rischio delle credenziali persi per un certo periodo di tempo?

Se non sono stati visualizzati eventi di rischio delle credenziali persi, il motivo è il seguente:

- PhS non è abilitato per il tenant.
- Microsoft non ha trovato coppie di credenziali perse corrispondenti agli utenti.

#### <a name="how-often-does-microsoft-process-new-credentials"></a>Con quale frequenza Microsoft elabora nuove credenziali?

Le credenziali vengono elaborate immediatamente dopo essere state trovate, in genere in più batch al giorno.

## <a name="next-steps"></a>Passaggi successivi

- [Criteri disponibili per attenuare i rischi](concept-identity-protection-policies.md)
- [Panoramica della sicurezza](concept-identity-protection-security-overview.md)