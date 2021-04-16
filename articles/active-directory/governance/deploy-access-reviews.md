---
title: Pianificare una distribuzione Azure Active Directory verifiche di accesso
description: Guida alla pianificazione per una corretta distribuzione delle verifiche di accesso
services: active-directory
documentationCenter: ''
author: BarbaraSelden
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3af783d7ff8be36c63af871ab4f2d214ca9f9405
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532591"
---
# <a name="planning-azure-active-directory-access-reviews-deployment"></a>Pianificazione della Azure Active Directory delle verifiche di accesso

[Azure Active Directory (Azure AD) Le](access-reviews-overview.md) verifiche di accesso consentono all'organizzazione di mantenere la rete più sicura gestendo il relativo ciclo [di vita di accesso alle risorse.](identity-governance-overview.md) Con le verifiche di accesso è possibile:

* Pianificare revisioni regolari o eseguire revisioni ad hoc per verificare chi può accedere a risorse specifiche, ad esempio applicazioni e gruppi

* Tenere traccia delle revisioni per informazioni dettagliate, conformità o motivi dei criteri

* Delegare le revisioni a amministratori, proprietari aziendali o utenti finali specifici che possono attestare autonomamente la necessità di un accesso continuo

* Usare le informazioni dettagliate per determinare in modo efficiente se gli utenti devono continuare ad accedere

* Automatizzare i risultati della revisione, ad esempio la rimozione dell'accesso degli utenti alle risorse

  ![Diagramma che mostra il flusso delle verifiche di accesso.](./media/deploy-access-review/1-planning-review.png)

Le verifiche di accesso sono [Azure AD Identity Governance](identity-governance-overview.md) funzionalità. Le altre funzionalità sono [Entitlement Management](entitlement-management-overview.md), [Privileged Identity Management](../privileged-identity-management/pim-configure.md) e Condizioni [per l'utilizzo](../conditional-access/terms-of-use.md). Insieme, aiutano le organizzazioni a risolvere queste quattro domande:

* Quali utenti hanno accesso a quali risorse?

* In che modo questi utenti usano l'accesso?

* Esiste un controllo aziendale efficace per la gestione dell'accesso?

* I revisori possono verificare l'operatività dei controlli?

La pianificazione della distribuzione delle verifiche di accesso è essenziale per assicurarsi di ottenere la strategia di governance desiderata per gli utenti dell'organizzazione.

### <a name="key-benefits"></a>Vantaggi principali

I vantaggi principali dell'abilitazione delle verifiche di accesso sono:

* **Controllare la collaborazione.** Le verifiche di accesso consentono alle organizzazioni di gestire l'accesso a tutte le risorse necessarie agli utenti. Quando gli utenti condividono e collaborano, le organizzazioni possono essere certi che le informazioni siano solo tra gli utenti autorizzati.

* **Gestire il rischio**. Le verifiche di accesso consentono alle organizzazioni di esaminare l'accesso ai dati e alle applicazioni, abbassando il rischio di perdita di dati e di fuoriuscita di dati. Sono incluse le funzionalità per esaminare regolarmente l'accesso di partner esterni alle risorse aziendali. 

* **Risolvere i problemi di conformità e governance.** Con le verifiche di accesso è possibile gestire e ricertificare il ciclo di vita di accesso a gruppi, app e siti. È possibile controllare le revisioni per la conformità o le applicazioni sensibili ai rischi specifiche per l'organizzazione. 

* **Ridurre il costo**. Le verifiche di accesso sono integrate nel cloud e funzionano in modo nativo con le risorse cloud, ad esempio gruppi, applicazioni e pacchetti di accesso. L'uso delle verifiche di accesso è meno onerario rispetto alla creazione di strumenti personalizzati o all'aggiornamento del set di strumenti locale.

### <a name="training-resources"></a>Risorse di formazione

I video seguenti possono essere utili quando si apprendono le verifiche di accesso:

* [Che cosa sono le verifiche di accesso Azure AD?](https://youtu.be/kDRjQQ22Wkk)

* [Come creare verifiche di accesso in Azure AD](https://youtu.be/6KB3TZ8Wi40)

* [Come abilitare le verifiche di accesso in Azure AD](https://youtu.be/X1SL2uubx9M)

* [Come esaminare l'accesso usando Accesso personale](https://youtu.be/tIKdQhdHLXU)

### <a name="licenses"></a>Licenze

È necessaria una licenza Azure AD Premium (P2) valida per ogni persona, diversa da Amministratori globali o Amministratori utenti, che creerà o eseguirà verifiche di accesso. Per altre informazioni, vedere [Requisiti di licenza di Verifiche di accesso](access-reviews-overview.md).

Potrebbero essere necessarie anche altre funzionalità di governance delle identità, ad esempio [Entitlement Lifecycle Management](entitlement-management-overview.md) o Privileged Identity Management. In tal caso, potrebbero essere necessarie anche licenze correlate. Per altre informazioni, vedere [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="plan-the-access-reviews-deployment-project"></a>Pianificare il progetto di distribuzione Verifiche di accesso

Considerare le esigenze dell'organizzazione per determinare la strategia per la distribuzione delle verifiche di accesso nell'ambiente.

### <a name="engage-the-right-stakeholders"></a>Coinvolgere gli stakeholder appropriati

Quando i progetti tecnologici hanno esito negativo, in genere la causa sono le diverse aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi inconvenienti, [assicurarsi di coinvolgere gli stakeholder appropriati](../fundamentals/active-directory-deployment-plans.md) e che i ruoli del progetto siano chiari.

Per le verifiche di accesso, è probabile che si includano i rappresentanti dei team seguenti all'interno dell'organizzazione:

* **L'amministrazione IT** gestisce l'infrastruttura IT e gli investimenti nel cloud e le app SaaS (Software as a Service). Questo team:

   * Esaminare l'accesso con privilegi all'infrastruttura e alle app, inclusi Microsoft 365 e Azure AD.

   * Pianificare ed eseguire verifiche di accesso su gruppi usati per gestire elenchi di eccezioni o progetti pilota IT, per mantenere gli elenchi di accesso aggiornati.

   * Assicurarsi che l'accesso a livello di codice (con script) alle risorse tramite le entità servizio sia regolato e esaminato.

* **I team di** sviluppo compilano e gestiscono applicazioni per l'organizzazione. Questo team:

   * Controllare chi può accedere e gestire i componenti nelle risorse SaaS, PaaS e IaaS che costituiscono le soluzioni sviluppate.

   * Gestire i gruppi che possono accedere ad applicazioni e strumenti per lo sviluppo di applicazioni interne.

   * Richiedere identità con privilegi che hanno accesso al software di produzione o alle soluzioni ospitate per i clienti

* **Le business unit** gestiscono i progetti e le proprie applicazioni. Questo team: 

   * Esaminare e approvare o negare l'accesso a gruppi e applicazioni per utenti interni ed esterni.

   * Pianificare ed eseguire revisioni per attestare l'accesso continuo per i dipendenti e le identità esterne, ad esempio i partner commerciali.

* **La governance** aziendale garantisce che l'organizzazione rispetti i criteri interni e rispetti le normative. Questo team:

   * Richiedere o pianificare nuove verifiche di accesso.

   * Valutare i processi e le procedure per la verifica dell'accesso, inclusa la documentazione e il mantenimento dei record per la conformità.

   * Esaminare i risultati delle revisioni precedenti per le risorse più critiche.

> [!NOTE]
> Per le revisioni che richiedono valutazioni manuali, assicurarsi di pianificare revisori adeguati e cicli di revisione che soddisfino i criteri e le esigenze di conformità. Se i cicli di revisione sono troppo frequenti o sono presenti troppi revisori, la qualità potrebbe andarsi persa e troppe o troppe persone potrebbero avere accesso. 

### <a name="plan-communications"></a>Pianificare le comunicazioni

La comunicazione è fondamentale per il successo di qualsiasi nuovo processo aziendale. Comunicare in modo proattivo agli utenti come e quando cambierà l'esperienza e come ottenere supporto in caso di problemi. 

#### <a name="communicate-changes-in-accountability"></a>Comunicare le modifiche nella responsabilità

Le verifiche di accesso supportano lo spostamento della responsabilità di rivedere e agire in base all'accesso continuo ai proprietari aziendali. La separazione delle decisioni di accesso dall'IT determina decisioni di accesso più accurate. Si tratta di un cambiamento culturale nella responsabilità e nella responsabilità del proprietario delle risorse. Comunicare in modo proattivo questa modifica e assicurarsi che i proprietari delle risorse siano addestrati e in grado di usare le informazioni dettagliate per prendere decisioni efficaci.

Chiaramente, l'IT vuole mantenere il controllo per tutte le decisioni di accesso correlate all'infrastruttura e le assegnazioni di ruolo con privilegi. 

#### <a name="customize-email-communication"></a>Personalizzare le comunicazioni tramite posta elettronica

Quando si pianifica una revisione, si designano gli utenti che eseguiranno questa verifica. Questi revisori ricevono quindi una notifica tramite posta elettronica delle nuove revisioni assegnate, nonché promemoria prima della scadenza di una revisione assegnata.

Gli amministratori possono scegliere di inviare questa notifica a metà strada prima della scadenza della revisione o un giorno prima della scadenza. 

Il messaggio di posta elettronica inviato ai revisori può essere personalizzato in modo da includere un breve messaggio personalizzato che li incoraggi ad agire sulla revisione. È consigliabile usare il testo aggiuntivo per:

* Includere un messaggio personale ai revisori, in modo che comprenda che viene inviato dal reparto CONFORMITÀ o IT.

* Includere un collegamento ipertestuale o un riferimento a informazioni interne sulle aspettative della revisione e materiale di riferimento o di training aggiuntivo.

* Includere un collegamento alle istruzioni [su come eseguire una verifica autonoma dell'accesso.](review-your-access.md) 

  ![Messaggio di posta elettronica del revisore](./media/deploy-access-review/2-plan-reviewer-email.png)

Quando si seleziona Avvia revisione, i revisori verranno indirizzati al [portale myAccess per](https://myapplications.microsoft.com/) le verifiche di accesso del gruppo e dell'applicazione. Il portale offre una panoramica di tutti gli utenti che hanno accesso alla risorsa che stanno esaminando e consigli di sistema in base alle ultime informazioni di accesso e accesso.

### <a name="plan-a-pilot"></a>Pianificare un progetto pilota

Si consiglia ai clienti di pilotare inizialmente le verifiche di accesso con un gruppo di piccole dimensioni e di avere come destinazione risorse non critiche. La distribuzione pilota consente di modificare i processi e le comunicazioni in base alle esigenze e di aumentare la capacità degli utenti e dei revisori di soddisfare i requisiti di sicurezza e conformità.

Nel progetto pilota è consigliabile:

* Iniziare con le revisioni in cui i risultati non vengono applicati automaticamente ed è possibile controllare le implicazioni.

* Assicurarsi che tutti gli utenti abbia indirizzi di posta elettronica validi elencati in Azure AD e che ricevano le comunicazioni tramite posta elettronica per intraprendere l'azione appropriata. 

* Documentare eventuali accessi rimossi come parte del progetto pilota nel caso in cui sia necessario ripristinarlo rapidamente.

* Monitorare i log di controllo per assicurarsi che tutti gli eventi siano controllati correttamente.

Per altre informazioni, vedere [le procedure consigliate per un progetto pilota.](../fundamentals/active-directory-deployment-plans.md)

## <a name="introduction-to-access-reviews"></a>Introduzione alle verifiche di accesso

Questa sezione presenta i concetti di verifica di accesso che è necessario conoscere prima di pianificare le verifiche.

### <a name="what-resource-types-can-be-reviewed"></a>Quali tipi di risorse è possibile esaminare?

Dopo aver integrato le risorse dell'organizzazione con Azure AD, ad esempio utenti, applicazioni e gruppi, queste possono essere gestite e esaminate. 

Gli obiettivi tipici per la revisione includono:

* [Applicazioni integrate con Azure AD per Single Sign-On](../manage-apps/what-is-application-management.md) (ad esempio SaaS, line-of-business).

* Appartenenza [ai](../fundamentals/active-directory-manage-groups.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context) gruppi (sincronizzata con Azure AD o creata in Azure AD o Microsoft 365, incluso Microsoft Teams).

* [Accedere a Pacchetto](./entitlement-management-overview.md) che raggruppa le risorse (gruppi, app e siti) in un unico pacchetto per gestire l'accesso.

* [Azure AD e i ruoli delle risorse di Azure](../privileged-identity-management/pim-resource-roles-assign-roles.md) come definito in Privileged Identity Management.

### <a name="who-will-create-and-manage-access-reviews"></a>Chi creerà e gestirà le verifiche di accesso?

Il ruolo amministrativo necessario per creare, gestire o leggere una verifica di accesso dipende dal tipo di risorsa da esaminare. La tabella seguente indica i ruoli necessari per ogni tipo di risorsa:

| Tipo di risorsa| Creare e gestire verifiche di accesso (Creators)| Leggere i risultati della verifica di accesso |
| - | - | -|
| Gruppo o applicazione| Amministratore globale <p>Amministratore utenti| Creators e amministratore della sicurezza |
| Ruoli con privilegi in Azure AD| Amministratore globale <p>Amministratore dei ruoli con privilegi| Autori <p>Ruolo con autorizzazioni di lettura per la sicurezza<p>Amministratore della protezione |
| Ruoli con privilegi in Azure (risorse)| Amministratore globale<p>Amministratore utenti<p>Proprietario della risorsa| Autori |
| Pacchetto di accesso| Amministratore globale<p>Creator of Access Package| Solo amministratore globale |


Per altre informazioni, vedere [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../roles/permissions-reference.md).

### <a name="who-will-review-the-access-to-the-resource"></a>Chi rivedrà l'accesso alla risorsa?

L'autore della verifica di accesso decide al momento della creazione chi eseguirà la verifica. Questa impostazione non può essere modificata dopo l'avvio della revisione. I revisori sono rappresentati da tre utenti tipo:

* Proprietari di risorse, che sono i proprietari aziendali della risorsa.

* Set di delegati selezionati singolarmente, come selezionato dall'amministratore delle verifiche di accesso.

* Gli utenti finali che si autotesteranno sulla necessità di un accesso continuo.

Quando si crea una verifica di accesso, gli amministratori possono scegliere uno o più revisori. Tutti i revisori possono avviare ed eseguire una verifica, scegliendo gli utenti per l'accesso continuo a una risorsa o rimuovendoli. 

### <a name="components-of-an-access-review"></a>Componenti di una verifica di accesso

Prima di implementare le verifiche di accesso, è consigliabile pianificare i tipi di verifiche rilevanti per l'organizzazione. A tale scopo, è necessario prendere decisioni aziendali su ciò che si vuole esaminare e sulle azioni da intraprendere in base a tali revisioni.

Per creare un criterio di verifica di accesso, è necessario disporre delle informazioni seguenti.

* Quali sono le risorse da esaminare?

* Di cui viene esaminato l'accesso.

* Con quale frequenza deve verificarsi la revisione?

* Chi eseguirà la revisione?

   * In che modo riceveranno una notifica per la revisione?

   * Quali sono le sequenze temporali da applicare per la revisione?

* Quali azioni automatiche devono essere applicate in base alla revisione?

   * Cosa accade se il revisore non risponde in tempo?

* Quali azioni manuali verranno eseguite in base alla revisione?

* Quali comunicazioni devono essere inviate in base alle azioni eseguite?


**Piano di verifica di accesso di esempio**

| Componente| Valore |
| - | - |
| **Risorse da esaminare**| Accesso a Microsoft Dynamics |
| **Frequenza di revisione**| Mensile |
| **Chi esegue la revisione**| Responsabili dei programmi del gruppo di business Dynamics |
| **Notifica**| 24 ore prima della revisione all'alias Dynamics-Pms<p>Includere un messaggio personalizzato incoraggiante ai revisori per proteggere il proprio buy-in |
| **Sequenza temporale**| 48 ore dalla notifica |
|**Azioni automatiche**| Rimuovere l'accesso da qualsiasi account senza accesso interattivo entro 90 giorni, rimuovendo l'utente dal gruppo di sicurezza dynamics-access. <p>*Eseguire azioni se non vengono esaminate all'interno della sequenza temporale.* |
| **Azioni manuali**| I revisori possono eseguire l'approvazione delle rimozioni prima dell'azione automatizzata, se necessario. |
| **Comunicazioni**| Inviare utenti interni (membri) che hanno rimosso un messaggio di posta elettronica spiegando che sono stati rimossi e come ottenere nuovamente l'accesso. |


 

### <a name="automate-actions-based-on-access-reviews"></a>Automatizzare le azioni in base alle verifiche di accesso

È possibile scegliere di automatizzare la rimozione dell'accesso impostando l'opzione Applica automaticamente i risultati alla risorsa su Abilita.

  ![Pianificazione delle verifiche di accesso](./media/deploy-access-review/3-automate-actions-settings.png)

Al termine della revisione, gli utenti che non sono stati approvati dal revisore verranno automaticamente rimossi dalla risorsa o mantenuti con accesso continuo. Ciò potrebbe significare rimuovere l'appartenenza al gruppo, l'assegnazione dell'applicazione o revocare il diritto di elevare a un ruolo con privilegi.

Prendere raccomandazioni

Le raccomandazioni vengono visualizzate ai revisori come parte dell'esperienza del revisore e indicano l'ultimo accesso di una persona al tenant o l'ultimo accesso a un'applicazione. Queste informazioni consentono ai revisori di prendere la decisione di accesso più giusta. Se si seleziona Accetta raccomandazioni, verranno seguite le raccomandazioni di Verifica di accesso. Al termine di una verifica di accesso, il sistema applica automaticamente queste raccomandazioni agli utenti per cui i revisori non hanno risposto.

Le raccomandazioni si basano sui criteri nella verifica di accesso. Ad esempio, se si configura la verifica per rimuovere l'accesso senza accesso interattivo per 90 giorni, è consigliabile rimuovere tutti gli utenti che soddisfano tali criteri. Microsoft continua a lavorare per migliorare le raccomandazioni. 

### <a name="review-guest-user-access"></a>Verificare l'accesso degli utenti guest

Usare le verifiche di accesso per esaminare e pulire le identità dei partner di collaborazione da organizzazioni esterne. La configurazione di una revisione per partner può soddisfare i requisiti di conformità.

Alle identità esterne è possibile concedere l'accesso alle risorse aziendali tramite una delle azioni seguenti:

* Aggiunta a un gruppo 

* Invitati a Teams 

* Assegnato a un'applicazione aziendale o a un pacchetto di accesso

* Assegnato un ruolo con privilegi in Azure AD o in una sottoscrizione di Azure

Vedere [lo script di esempio](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse). Lo script mostrerà dove vengono usate le identità esterne invitate nel tenant. È possibile visualizzare l'appartenenza a gruppi dell'utente esterno, le assegnazioni di ruolo e le assegnazioni di applicazioni Azure AD. Lo script non mostrerà alcuna assegnazione al di fuori Azure AD, ad esempio l'assegnazione diretta dei diritti alle risorse di SharePoint, senza l'uso di gruppi.

Quando si crea una verifica di accesso per gruppi o applicazioni, è possibile scegliere di consentire al revisore di concentrarsi su Tutti gli utenti con accesso o Solo utenti guest. Selezionando Solo utenti guest, ai revisori viene assegnato un elenco mirato di identità esterne Azure AD B2B che hanno accesso alla risorsa.

 ![Esaminare gli utenti guest](./media/deploy-access-review/4-review-guest-users-admin-ui.png)

> [!IMPORTANT]
> NON includerà membri esterni con userType di member. Ciò non includerà anche gli utenti invitati all'esterno di Azure AD collaborazione B2B, ad esempio quelli che hanno accesso al contenuto condiviso direttamente tramite SharePoint.

## <a name="plan-access-reviews-for-access-packages"></a>Pianificare le verifiche di accesso per i pacchetti di accesso

[I pacchetti di accesso](entitlement-management-overview.md) possono semplificare notevolmente la governance e la strategia di verifica di accesso. Un pacchetto di accesso è un bundle di tutte le risorse con accesso necessario a un utente per lavorare a un progetto o eseguire la propria attività. Ad esempio, è possibile creare un pacchetto di accesso che includa tutte le applicazioni necessarie per gli sviluppatori dell'organizzazione o tutte le applicazioni a cui gli utenti esterni devono avere accesso. Un amministratore o un delegato di Gestione pacchetti di accesso raggruppa quindi le risorse (gruppi o app) e i ruoli necessari agli utenti per tali risorse.

Quando si crea un pacchetto di [accesso,](entitlement-management-access-package-create.md)è possibile creare uno o più criteri di accesso che impostano le condizioni per cui gli utenti possono richiedere un pacchetto di accesso, l'aspetto del processo di approvazione e la frequenza con cui una persona deve richiedere nuovamente l'accesso. Le verifiche di accesso vengono configurate durante la creazione o la modifica di un criterio del pacchetto di accesso.

Aprire la scheda Ciclo di vita per scorrere verso il basso fino a Verifiche di accesso.

 ![Screenshot che mostra "Modifica criterio" nella scheda "Ciclo di vita".](./media/deploy-access-review/5-plan-access-packages-admin-ui.png)

## <a name="plan-access-reviews-for-groups"></a>Pianificare le verifiche di accesso per i gruppi

Oltre ai pacchetti di accesso, la verifica dell'appartenenza ai gruppi è il modo più efficace per gestire l'accesso. È consigliabile assegnare l'accesso alle risorse tramite gruppi di sicurezza [o gruppi Microsoft 365](../fundamentals/active-directory-manage-groups.md)e che gli utenti siano aggiunti a tali gruppi per ottenere l'accesso.

A un singolo gruppo è possibile concedere l'accesso a tutte le risorse appropriate. È possibile assegnare al gruppo l'accesso a singole risorse o a un pacchetto di accesso che raggruppa applicazioni e altre risorse. Con questo metodo è possibile verificare l'accesso al gruppo anziché l'accesso di un utente a ogni applicazione. 

L'appartenenza al gruppo può essere verificata da: 

* Administrators

* Proprietari del gruppo

* Utenti selezionati, funzionalità di revisione delegata quando viene creata la revisione

* Membri del gruppo, che si attestano per se stessi

### <a name="group-ownership"></a>Proprietà di gruppo

È consigliabile che i proprietari dei gruppi rivedano l'appartenenza, in quanto si trovano meglio per sapere chi deve accedere. La proprietà dei gruppi è diversa dal tipo di gruppo:

I gruppi creati in Microsoft 365 e Azure AD hanno uno o più proprietari ben definiti. Nella maggior parte dei casi, questi proprietari fanno revisori ideali per i propri gruppi, in quanto sanno chi deve avere accesso. 

Ad esempio, Microsoft Teams usa Gruppi di Microsoft 365 come modello di autorizzazione sottostante per concedere agli utenti l'accesso alle risorse presenti in SharePoint, Exchange, OneNote o altri Microsoft 365 servizi. L'autore del team diventa automaticamente un proprietario e deve essere responsabile dell'attestazione dell'appartenenza del gruppo. 

I gruppi creati manualmente nel portale Azure AD o tramite script tramite Microsoft Graph possono non necessariamente avere proprietari definiti. È consigliabile definirli tramite il portale Azure AD nella sezione "Proprietari" del gruppo o tramite Graph.

I gruppi sincronizzati da Active Directory locale non possono avere un proprietario in Azure AD. Quando si crea una verifica di accesso, è consigliabile selezionare le persone più adatte per decidere l'appartenenza.

> [!NOTE]
> È consigliabile definire criteri di business che definiscono la modalità di creazione dei gruppi per garantire la proprietà e la responsabilità del gruppo per una revisione regolare dell'appartenenza. 

### <a name="review-membership-of-exclusion-groups-in-conditional-access-policies"></a>Esaminare l'appartenenza ai gruppi di esclusione nei criteri di accesso condizionale 

In alcuni casi i criteri di accesso condizionale progettati per proteggere la rete non devono essere applicati a tutti gli utenti. Ad esempio, un criterio di accesso condizionale che consente solo agli utenti di accedere mentre si è connessi alla rete aziendale potrebbe non essere applicabile al team sales, che viaggia a lungo. In tal caso, i membri del team Vendite verrebbero inseriti in un gruppo e tale gruppo verrebbe escluso dai criteri di accesso condizionale. 

Esaminare regolarmente l'appartenenza a un gruppo di questo tipo, in quanto l'esclusione rappresenta un potenziale rischio se i membri erre sono esclusi dal requisito.

È possibile [usare le Azure AD di accesso per gestire gli utenti esclusi dai criteri di accesso condizionale.](conditional-access-exclusion.md)

### <a name="review-external-users-group-memberships"></a>Esaminare le appartenenze ai gruppi dell'utente esterno

Per ridurre al minimo il lavoro manuale e i potenziali errori associati, è consigliabile usare [i gruppi](../enterprise-users/groups-create-rule.md) dinamici per assegnare l'appartenenza ai gruppi in base agli attributi di un utente. È possibile creare uno o più gruppi dinamici per gli utenti esterni. Lo sponsor interno può fungere da revisore per l'appartenenza al gruppo. 

Nota: gli utenti esterni che vengono rimossi da un gruppo come risultato di una verifica di accesso non vengono eliminati dal tenant. 

Possono essere eliminati da un tenant manualmente o tramite uno script.

### <a name="review-access-to-on-premises-groups"></a>Verificare l'accesso ai gruppi locali

Le verifiche di accesso non possono modificare l'appartenenza ai gruppi sincronizzati dall'ambiente locale [con Azure AD Connect](../hybrid/whatis-azure-ad-connect.md). Ciò è dovuto al fatto che l'origine dell'autorità è locale.

È comunque possibile usare le verifiche di accesso per pianificare e gestire revisioni regolari dei gruppi locali. I revisori eseranno quindi un'azione nel gruppo locale. Questa strategia mantiene le verifiche di accesso come strumento per tutte le verifiche.

È possibile usare i risultati di una verifica di accesso nei gruppi locali ed elaborarli ulteriormente, tramite:

* Download del report CSV dalla verifica di accesso ed esecuzione manuale di un'azione.

* L'Microsoft Graph per accedere a livello di codice ai risultati e alle decisioni nelle verifiche di accesso completate.

Ad esempio, per accedere ai risultati per un gruppo gestito da Windows AD, usare questo [script di esempio di PowerShell](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises). Lo script descrive le chiamate Graph necessarie ed esporta i comandi di Windows AD-PowerShell per eseguire le modifiche.

## <a name="plan-access-reviews-for-applications"></a>Pianificare le verifiche di accesso per le applicazioni 

Quando si verifica l'accesso a un'applicazione, si verifica l'accesso per i dipendenti e le identità esterne alle informazioni e ai dati all'interno dell'applicazione. Scegliere di esaminare un'applicazione quando è necessario sapere chi può accedere a un'applicazione specifica, anziché un pacchetto di accesso o un gruppo. 

È consigliabile pianificare le revisioni per le applicazioni negli scenari seguenti:

* Agli utenti viene concesso l'accesso diretto all'applicazione (all'esterno di un gruppo o di un pacchetto di accesso).

* L'applicazione espone informazioni critiche o riservate.

* L'applicazione ha requisiti di conformità specifici a cui è necessario attestare.

* Si sospetta un accesso inappropriato.

Per creare verifiche di accesso per un'applicazione, impostare Assegnazione utente obbligatoria? proprietà su Sì. Se impostato su No, tutti gli utenti nella directory, incluse le identità esterne, possono accedere all'applicazione e non è possibile esaminare l'accesso all'applicazione. 

 ![pianificare le assegnazioni di app](./media/deploy-access-review/6-plan-applications-assignment-required.png)

È quindi necessario [assegnare gli utenti e i](../manage-apps/assign-user-or-group-access-portal.md) gruppi a cui si vuole accedere. 

### <a name="reviewers-for-an-application"></a>Revisori per un'applicazione

Le verifiche di accesso possono essere relative ai membri di un gruppo o agli utenti assegnati a un'applicazione. Le applicazioni Azure AD non hanno necessariamente un proprietario, motivo per cui l'opzione per la selezione del proprietario dell'applicazione come revisore non è possibile. È possibile impostare ulteriormente l'ambito di una revisione per esaminare solo gli utenti guest assegnati all'applicazione, anziché esaminare tutti gli accessi.

## <a name="plan-review-of-azure-ad-and-azure-resource-roles"></a>Revisione del piano dei ruoli Azure AD e delle risorse di Azure

[Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) semplifica il modo in cui le aziende gestiscono l'accesso con privilegi alle risorse in Azure AD. In questo modo l'elenco dei ruoli con privilegi, sia [in](../roles/permissions-reference.md) Azure AD che nelle risorse di [Azure,](../../role-based-access-control/built-in-roles.md) è molto più piccolo e aumenta la sicurezza complessiva della directory.

Le verifiche di accesso consentono ai revisori di verificare se gli utenti devono ancora avere un ruolo. Proprio come le verifiche di accesso per i pacchetti di accesso, le revisioni Azure AD ruoli e le risorse di Azure sono integrate nell'esperienza utente amministratore PIM. È consigliabile esaminare regolarmente le assegnazioni di ruolo seguenti:

* Amministratore globale

* Amministratore utenti

* Amministratore autenticazione con privilegi

* Amministratore accesso condizionale

* Amministratore della protezione

* Tutti Microsoft 365 e i ruoli di amministrazione del servizio Dynamics

I ruoli selezionati qui includono il ruolo permanente e idoneo. 

Nella sezione Revisori selezionare una o più persone per la verifica di tutti gli utenti. In alternativa è possibile fare in modo che i membri verifichino il proprio accesso.

 ![Modificare i criteri](./media/deploy-access-review/7-plan-azure-resources-reviewers-selection.png)

## <a name="deploy-access-reviews"></a>Distribuire le verifiche di accesso

Dopo aver preparato una strategia e un piano per esaminare l'accesso per le risorse integrate con Azure AD, distribuire e gestire le revisioni usando le risorse seguenti.

### <a name="review-access-packages"></a>Esaminare i pacchetti di accesso

Per ridurre il rischio di accesso non obsoleto, gli amministratori possono abilitare revisioni periodiche degli utenti che hanno assegnazioni attive a un pacchetto di accesso. Seguire le istruzioni nel collegamento seguente:

| Procedure| Descrizione |
| - | - |
| [Creare verifiche di accesso](entitlement-management-access-reviews-create.md)| Abilitare le revisioni del pacchetto di accesso. |
| [Eseguire verifiche di accesso](entitlement-management-access-reviews-review-access.md)| Eseguire verifiche di accesso per altri utenti assegnati a un pacchetto di accesso. |
| [Pacchetti di accesso assegnati con revisione self-review](entitlement-management-access-reviews-self-review.md)| Auto-revisione dei pacchetti di accesso assegnati |


> [!NOTE]
> Gli utenti finali che si auto-verificano e affermano di non aver più bisogno dell'accesso non vengono rimossi immediatamente dal pacchetto di accesso. Vengono rimossi dal pacchetto di accesso al termine della revisione o se un amministratore arresta la revisione.

### <a name="review-groups-and-apps"></a>Esaminare gruppi e app

È probabile che l'accesso a gruppi e applicazioni per dipendenti e guest cambi nel tempo. Per ridurre il rischio associato alle assegnazioni di accesso non recenti, gli amministratori possono creare verifiche di accesso per i membri del gruppo o l'accesso alle applicazioni. Seguire le istruzioni nel collegamento seguente:

| Procedure| Descrizione |
| - | - |
| [Creare verifiche di accesso](create-access-review.md)| Creare una o più verifiche di accesso per i membri del gruppo o l'accesso all'applicazione. |
| [Eseguire verifiche di accesso](perform-access-review.md)| Eseguire la verifica di accesso per i membri di un gruppo o gli utenti con accesso a un'applicazione. |
| [Verifica self-review dell'accesso](review-your-access.md)| I membri verificano il proprio accesso a un gruppo o a un'applicazione |
| [Completare la verifica di accesso](complete-access-review.md)| Visualizzare una verifica di accesso e applicare i risultati |
| [Eseguire azioni per i gruppi locali](https://github.com/microsoft/access-reviews-samples/tree/master/AzureADAccessReviewsOnPremises)| Script di PowerShell di esempio per agire sulle verifiche di accesso per i gruppi locali. |


### <a name="review-azure-ad-roles"></a>Esaminare Azure AD ruoli

Per ridurre il rischio associato alle assegnazioni di ruolo non aggiornato, è consigliabile esaminare regolarmente l'accesso dei ruoli Azure AD privilegi.

![Screenshot che mostra l'elenco "Verifica appartenenza" dei ruoli di Azure D.](./media/deploy-access-review/8-review-azure-ad-roles-picker.png)

Seguire le istruzioni disponibili nei collegamenti seguenti:

| Procedure | Descrizione |
| - | - |
 [Creare verifiche di accesso](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Creare verifiche di accesso per i ruoli Azure AD privilegi in PIM |
| [Verifica self-review dell'accesso](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Se si è assegnati a un ruolo amministrativo, approvare o negare l'accesso al ruolo |
| [Completare una verifica di accesso](../privileged-identity-management/pim-how-to-complete-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Visualizzare una verifica di accesso e applicare i risultati |


### <a name="review-azure-resource-roles"></a>Esaminare i ruoli delle risorse di Azure

Per ridurre il rischio associato alle assegnazioni di ruolo non aggiornato, è consigliabile esaminare regolarmente l'accesso dei ruoli delle risorse di Azure con privilegi. 

![esaminare i ruoli di Azure AD](./media/deploy-access-review/9-review-azure-roles-picker.png)

Seguire le istruzioni disponibili nei collegamenti seguenti:

| Procedure| Descrizione |
| - | -|
| [Creare verifiche di accesso](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Creare verifiche di accesso per i ruoli delle risorse di Azure con privilegi in PIM |
| [Verifica self-review dell'accesso](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Se si è assegnati a un ruolo amministrativo, approvare o negare l'accesso al ruolo |
| [Completare una verifica di accesso](../privileged-identity-management/pim-resource-roles-complete-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)| Visualizzare una verifica di accesso e applicare i risultati |


## <a name="use-the-access-reviews-api"></a>Usare l'API verifiche di accesso

Vedere i [metodi dell'API Graph](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) [e i controlli di autorizzazione di](/graph/api/resources/accessreviewsv2-root?view=graph-rest-beta&preserve-view=true) ruolo e applicazione per interagire con e gestire le risorse verificabili. I metodi delle verifiche di accesso nell'API Microsoft Graph sono disponibili sia per i contesti applicazione che per i contesti utente. Quando si eseguono script nel contesto dell'applicazione, all'account usato per eseguire l'API (principio di servizio) deve essere concessa l'autorizzazione "AccessReview.Read.All" per eseguire query sulle informazioni delle verifiche di accesso.

Le attività più comuni delle verifiche di accesso per automatizzare l'API Graph per le verifiche di accesso sono:

* Creare e avviare una verifica di accesso

* Terminare manualmente una verifica di accesso prima della fine pianificata

* Elencare tutte le verifiche di accesso in esecuzione e il relativo stato

* Visualizzare la cronologia di una serie di revisioni e le decisioni e le azioni intraprese in ogni revisione.

* Raccogliere decisioni da una verifica di accesso

* Raccogliere le decisioni da revisioni completate in cui il revisore ha preso una decisione diversa da quella consigliata dal sistema.

Quando si creano nuove API Graph per l'automazione, è consigliabile usare [Graph Explorer.](https://developer.microsoft.com/en-us/graph/graph-explorer) È possibile compilare ed esplorare le query graph prima di inserire le query in script e codice. Ciò consente di eseguire rapidamente l'iterazione della query in modo da ottenere esattamente i risultati desiderati, senza modificare il codice dello script.

## <a name="monitor-access-reviews"></a>Monitorare le verifiche di accesso

Le attività delle verifiche di accesso vengono registrate e disponibili [Azure AD log di controllo dell'utente.](../reports-monitoring/concept-audit-logs.md) È possibile filtrare i dati di controllo in base alla categoria, al tipo di attività e all'intervallo di date. Ecco una query di esempio:

| Category| Criteri |
| - | - |
| Tipo di attività| Creare una verifica di accesso |
| | Aggiornare la verifica di accesso |
| | Verifica di accesso terminata |
| | Eliminare la verifica di accesso |
| | Approvare la decisione |
| | Decisione di negazione |
| | Decisione di reimpostazione |
| | Applicare la decisione |
| Intervallo di date| 7 giorni |


Per query e analisi più avanzate delle verifiche di accesso e per tenere traccia delle modifiche e del completamento delle verifiche, è consigliabile esportare i log di controllo di Azure AD in [Azure Log Analytics](../reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) o nell'hub eventi di Azure. Quando vengono archiviati in Azure Log Analytics, è possibile usare il potente linguaggio di [analisi](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md) e creare dashboard personalizzati.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sulle tecnologie correlate seguenti.

* [Che cos'Azure AD Entitlement Management?](entitlement-management-overview.md)

* [Che cos'è Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)