---
title: Creare e gestire gli utenti
description: Creare e gestire gli utenti dei sensori e la console di gestione locale. Agli utenti è possibile assegnare il ruolo di amministratore, analista della sicurezza o utente di sola lettura.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/21/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: c3a9e1c7e96d0392e1f94b71549f612738622dea
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840965"
---
# <a name="about-defender-for-iot-console-users"></a>Informazioni su Defender per gli utenti della console

Questo articolo descrive come creare e gestire gli utenti dei sensori e la console di gestione locale. I ruoli utente includono amministratore, analista della sicurezza o utente di sola lettura. Ogni ruolo è associato a un intervallo di autorizzazioni per gli strumenti per il sensore o la console di gestione locale. I ruoli sono progettati per facilitare l'accesso granulare e sicuro ad Azure Defender per tutti gli altri.

Le funzionalità sono disponibili anche per tenere traccia delle attività degli utenti e abilitare l'accesso Active Directory.

Per impostazione predefinita, ogni sensore e console di gestione locale viene installato con un *CyberX e* un utente del supporto tecnico. Questi utenti hanno accesso agli strumenti avanzati per la risoluzione dei problemi e la configurazione. Gli utenti amministratore devono accedere con queste credenziali utente, creare un utente amministratore e quindi creare utenti aggiuntivi per gli analisti della sicurezza e gli utenti di sola lettura.

## <a name="role-based-permissions"></a>Autorizzazioni basate sui ruoli
Sono disponibili i seguenti ruoli utente:

- **Sola lettura**: gli utenti di sola lettura eseguono attività quali la visualizzazione di avvisi e dispositivi nella mappa del dispositivo. Questi utenti hanno accesso alle opzioni visualizzate in **navigazione**.

- **Analista della sicurezza**: gli analisti della sicurezza hanno autorizzazioni utente di sola lettura. Possono anche eseguire azioni sui dispositivi, confermare gli avvisi e usare gli strumenti di analisi. Questi utenti hanno accesso alle opzioni visualizzate in **navigazione** e **analisi**.

- **Amministratore**: gli amministratori hanno accesso a tutti gli strumenti, inclusa la definizione delle configurazioni di sistema, la creazione e la gestione degli utenti e altro ancora. Questi utenti hanno accesso alle opzioni visualizzate in **navigazione**, **analisi** e **Amministrazione**.

### <a name="role-based-permissions-to-on-premises-management-console-tools"></a>Autorizzazioni basate sui ruoli per gli strumenti della console di gestione locale

In questa sezione vengono descritte le autorizzazioni disponibili per gli amministratori, gli analisti della sicurezza e gli utenti di sola lettura per la console di gestione locale.  

| Autorizzazione | Sola lettura | Analista della sicurezza | Amministratore |
|--|--|--|--|
| Visualizzare e filtrare la mappa dell'organizzazione | ✓ | ✓ | ✓ |
| Creazione di un sito |  |  | ✓ |
| Gestire un sito (aggiungere e modificare zone) |  |  | ✓ |
| Visualizzare e filtrare l'inventario dei dispositivi | ✓ | ✓ | ✓ |
| Visualizzare e gestire gli avvisi: riconoscimento, apprendimento e aggiunta | ✓ | ✓ | ✓ |
| Generare report |  | ✓ | ✓ |
| Visualizzare i report di valutazione dei rischi |  | ✓ | ✓ |
| Imposta esclusioni avvisi |  | ✓ | ✓ |
| Visualizzare o definire gruppi di accesso |  |  | ✓ |
| Gestisci impostazioni di sistema |  |  | ✓ |
| Gestire gli utenti |  |  | ✓ |
| Inviare i dati degli avvisi ai partner |  |  | ✓ |
| Gestire i certificati |  |  | ✓ |
| Timeout della sessione quando gli utenti non sono attivi | 30 minuti | 30 minuti  | 30 minuti  |

#### <a name="assign-users-to-access-groups"></a>Assegnare gli utenti ai gruppi di accesso

Gli amministratori possono migliorare il controllo degli accessi degli utenti in Defender, assegnando gli utenti a *gruppi di accesso* specifici. I gruppi di accesso vengono assegnati a zone, siti, aree e business unit in cui si trova un sensore. Assegnando gli utenti ai gruppi di accesso, gli amministratori ottengono un controllo specifico sulla gestione e l'analisi dei rilevamenti dei dispositivi da parte degli utenti. 

Il funzionamento di questo metodo consente alle organizzazioni di grandi dimensioni in cui le autorizzazioni utente possono essere complesse o determinate da criteri di sicurezza dell'organizzazione globali. Per altre informazioni, vedere [definire il controllo di accesso globale](how-to-define-global-user-access-control.md).

### <a name="role-based-permissions-to-sensor-tools"></a>Autorizzazioni basate sui ruoli per gli strumenti di sensori

In questa sezione vengono descritte le autorizzazioni disponibili per gli amministratori di sensori, gli analisti della sicurezza e gli utenti di sola lettura.  

| Autorizzazione | Sola lettura | Analista della sicurezza | Amministratore |
|--|--|--|--|
| Visualizzare il dashboard | ✓ | ✓ | ✓ |
| Viste zoom della mappa di controllo |  |  | ✓ |
| Visualizzare gli avvisi | ✓ | ✓ | ✓ |
| Gestione degli avvisi: riconoscimento, apprendimento e aggiunta |  | ✓ | ✓ |
| Visualizzare gli eventi in una sequenza temporale |  | ✓ | ✓ |
| Autorizzare dispositivi, dispositivi di analisi noti, dispositivi di programmazione |  | ✓ | ✓ |
| Visualizzare i dati di analisi | ✓ | ✓ | ✓ |
| Gestisci impostazioni di sistema |  |  | ✓ |
| Gestire gli utenti |  |  | ✓ |
| Server DNS per la ricerca inversa |  |  | ✓ |
| Inviare i dati degli avvisi ai partner |  | ✓ | ✓ |
| Crea commenti avviso |  | ✓ | ✓ |
| Visualizza cronologia modifiche programmazione | ✓ | ✓ | ✓ |
| Creare regole di avviso personalizzate |  | ✓ | ✓ |
| Gestione simultanea di più notifiche |  | ✓ | ✓ |
| Gestire i certificati |  |  | ✓ |
| Timeout della sessione quando gli utenti non sono attivi | 30 minuti | 30 minuti | 30 minuti |

## <a name="define-users"></a>Definire gli utenti

Questa sezione descrive come definire gli utenti. Gli utenti di CyberX, support e Administrator possono aggiungere, rimuovere e aggiornare altre definizioni utente.

Per definire un utente:

1. Dal riquadro sinistro per il sensore o la console di gestione locale selezionare **utenti**.
2. Nella finestra **utenti** selezionare **Crea utente**.
3. Nel riquadro **Crea utente** definire i parametri seguenti:

   - **Nome utente**: immettere un nome utente.
   - **Email**: immettere l'indirizzo di posta elettronica dell'utente.
   - **First Name (nome**): immettere il nome dell'utente.
   - **Cognome**: immettere il cognome dell'utente.
   - **Role**: definire il ruolo dell'utente. Vedere [autorizzazioni basate sui ruoli](#role-based-permissions).
   - **Gruppo di accesso**: se si sta creando un utente per la console di gestione locale, definire il gruppo di accesso dell'utente. Vedere [definire il controllo di accesso globale](how-to-define-global-user-access-control.md).
   - **Password**: selezionare il tipo di utente come segue:
     - **Utente locale**: definire una password per l'utente di un sensore o una console di gestione locale. La password deve includere almeno sei caratteri e deve includere lettere e numeri.
     - **Active Directory utente**: è possibile consentire agli utenti di accedere al sensore o alla console di gestione utilizzando Active Directory credenziali. I gruppi di Active Directory definiti possono essere associati a livelli di autorizzazione specifici. Ad esempio, configurare un gruppo di Active Directory specifico e assegnare tutti gli utenti del gruppo al tipo di utente di sola lettura.

:::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/manage-user-views.png" alt-text="Gestire gli utenti.":::

## <a name="user-session-timeout"></a>Timeout sessione utente

Se gli utenti non sono attivi sulla tastiera o sul mouse per un determinato periodo di tempo, verranno disconnessi dalla sessione e dovranno eseguire di nuovo l'accesso.

Quando gli utenti non hanno lavorato con il mouse o la tastiera della console per un periodo di 30 minuti, viene forzata una disconnessione della sessione.

Questa funzionalità è abilitata per impostazione predefinita e può essere disabilitata per l'aggiornamento. Inoltre, è possibile aggiornare i tempi di conteggio delle sessioni. I tempi di sessione sono definiti in secondi. Le definizioni vengono applicate per sensore e console di gestione locale.

Un messaggio di timeout della sessione viene visualizzato nella console di quando è trascorso il timeout di inattività.

### <a name="control-inactivity-sign-out"></a>Controllare la disconnessione di inattività

Gli utenti amministratori possono abilitare e disabilitare la disconnessione di inattività e modificare le soglie di inattività.

Per accedere al comando:

1. Accedere all'interfaccia della riga di comando per il sensore o la console di gestione locale usando Defender per le credenziali amministrative.

2. Immettere `sudo nano /var/cyberx/properties/authentication`.

```azurecli-interactive
    infinity_session_expiration = true
    session_expiration_default_seconds = 0
    # half an hour in seconds (comment)
    session_expiration_admin_seconds = 1800
    # a day in seconds
    session_expiration_security_analyst_seconds = 1800
    # a week in seconds
    session_expiration_read_only_users_seconds = 1800
```

Per disabilitare la funzionalità, modificare `infinity_session_expiration = true` in `infinity_session_expiration = false` .

Per aggiornare i periodi di conteggio di disconnessione, impostare il `= <number>` valore sul tempo richiesto.


## <a name="track-user-activity"></a>Tenere traccia dell'attività dell'utente 

È possibile tenere traccia dell'attività dell'utente nella sequenza temporale degli eventi in ogni sensore. La sequenza temporale Visualizza l'evento o il dispositivo interessato e l'ora e la data in cui l'utente ha eseguito l'attività.

Per visualizzare l'attività dell'utente:

1. Accedere al sensore.
1. Nella sequenza temporale dell'evento abilitare l'opzione **operazioni utente** . 

    :::image type="content" source="media/how-to-create-azure-for-defender-users-and-roles/User-login-attempts.png" alt-text="Visualizzare l'attività di un utente.":::

## <a name="integrate-with-active-directory-servers"></a>Integrazione con server Active Directory 

Configurare il sensore o la console di gestione locale per l'uso con Active Directory. Ciò consente agli utenti di Active Directory di accedere al Defender per le console di Internet delle cose usando le credenziali Active Directory.

Sono supportati due tipi di autenticazione basata su LDAP:

- **Autenticazione completa**: i dettagli dell'utente vengono recuperati dal server LDAP. Gli esempi sono il nome, il cognome, l'indirizzo di posta elettronica e le autorizzazioni utente.

- **Utente attendibile**: viene recuperata solo la password utente. Altri dettagli utente recuperati sono basati su utenti definiti nel sensore.

### <a name="active-directory-and-defender-for-iot-permissions"></a>Active Directory e Defender per le autorizzazioni per le cose

È possibile associare i gruppi di Active Directory definiti con livelli di autorizzazione specifici. Ad esempio, configurare un gruppo di Active Directory specifico e assegnare le autorizzazioni RO a tutti gli utenti del gruppo. Per informazioni dettagliate, vedere [creare e gestire gli utenti](how-to-create-and-manage-users.md) .

Per configurare Active Directory:

1. Nel riquadro sinistro selezionare impostazioni di **sistema**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-system-settings-v2.png" alt-text="Visualizzare le impostazioni del sistema di Active Directory.":::

2. Nel riquadro **Impostazioni sistema** selezionare **Active Directory**.

    :::image type="content" source="media/how-to-setup-active-directory/ad-configurations-v2.png" alt-text="Modificare le configurazioni del Active Directory.":::

3. Nella finestra di dialogo **modifica configurazione Active Directory** selezionare **Active Directory integrazione abilitata**  >  **Salva**. La finestra di dialogo **modifica configurazione Active Directory** si espande e ora è possibile immettere i parametri per configurare Active Directory.

    :::image type="content" source="media/how-to-setup-active-directory/ad-integration-enabled-v2.png" alt-text="Immettere i parametri per configurare Active Directory.":::

    > [!NOTE]
    > - È necessario definire i parametri LDAP esattamente come sono visualizzati in Active Directory.
    > - Per tutti i parametri di Active Directory, utilizzare solo caratteri minuscoli. Utilizzare caratteri minuscoli anche quando le configurazioni in Active Directory utilizzano caratteri maiuscoli.
    > - Non è possibile configurare LDAP e LDAP per lo stesso dominio. È tuttavia possibile utilizzare entrambi per domini diversi contemporaneamente.

4. Impostare i parametri del server Active Directory, come indicato di seguito:

   | Parametro Server | Descrizione |
   |--|--|
   | FQDN controller di dominio | Impostare il nome di dominio completo (FQDN) esattamente come appare nel server LDAP. Immettere ad esempio `host1.subdomain.domain.com`. |
   | Porta del controller di dominio | Definire la porta in cui è configurato LDAP. |
   | Dominio primario | Impostare il nome di dominio (ad esempio, `subdomain.domain.com` ) e il tipo di connessione in base alla configurazione LDAP. |
   | Gruppi di Active Directory | Immettere i nomi dei gruppi definiti nella configurazione del Active Directory nel server LDAP. |
   | Domini trusted | Per aggiungere un dominio trusted, aggiungere il nome di dominio e il tipo di connessione di un dominio trusted. <br />È possibile configurare domini trusted solo per gli utenti definiti in utenti. |

5. Selezionare **Salva**.

6. Per aggiungere un server attendibile, selezionare **Aggiungi server** e configurare un altro server.

## <a name="see-also"></a>Vedere anche

[Attivare e configurare il sensore](how-to-activate-and-set-up-your-sensor.md) 
 [Attivare e configurare la console](how-to-activate-and-set-up-your-on-premises-management-console.md) 
 di gestione locale [Rilevare le attività dei sensori](how-to-track-sensor-activity.md)
