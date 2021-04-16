---
title: Novità di Desktop virtuale Windows - Azure
description: Nuove funzionalità e aggiornamenti del prodotto per Desktop virtuale Windows.
author: Heidilohr
ms.topic: overview
ms.date: 04/08/2021
ms.author: helohr
ms.reviewer: thhickli; darank
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 242dd2476783a3d1db6b74452d35e87c5cd189b6
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516553"
---
# <a name="whats-new-in-windows-virtual-desktop"></a>Novità di Desktop virtuale Windows

Desktop virtuale Windows viene aggiornato regolarmente. Questo articolo include le informazioni seguenti:

- Aggiornamenti più recenti
- Nuove funzionalità
- Miglioramenti alle funzionalità esistenti
- Correzioni di bug

Questo articolo viene aggiornato ogni mese. Controllare spesso l'articolo per ottenere informazioni sui nuovi aggiornamenti.

## <a name="client-updates"></a>Aggiornamenti client

Per informazioni sugli aggiornamenti per i client per Desktop virtuale Windows e 
Servizi Desktop remoto, vedere questi articoli:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [macOS](/windows-server/remote/remote-desktop-services/clients/mac-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)
- [Android](/windows-server/remote/remote-desktop-services/clients/android-whatsnew)
- [Web](/windows-server/remote/remote-desktop-services/clients/web-client-whatsnew)

## <a name="windows-virtual-desktop-agent-updates"></a>Aggiornamenti dell'agente di Desktop virtuale Windows

L'agente desktop virtuale Windows viene aggiornato almeno una volta al mese.

Ecco le modifiche dell'agente di Desktop virtuale Windows:

- Versione 1.0.2990.800: questo aggiornamento è stato rilasciato il 13 aprile 2021 e presenta le modifiche seguenti:
    - Aggiornamento dei messaggi di errore dell'agente.
    - Aggiunge un'eccezione che impedisce l'installazione di agenti non Windows 7 nelle macchine virtuali Windows 7.
    - È stata aggiornata la logica del servizio heartbeat.
- Versione 1.0.2944.1400: questo aggiornamento è stato rilasciato il 7 aprile 2021 e presenta le modifiche seguenti:
    - Sono stati inseriti collegamenti alla guida alla risoluzione dei problemi dell'agente Desktop virtuale Windows nei log del Visualizzatore eventi per gli errori dell'agente.
    - Aggiunta di un'eccezione aggiuntiva per una migliore gestione degli errori.
    - Aggiunta del WVDAgentUrlTool.exe che consente ai clienti di controllare gli URL necessari a cui possono accedere.
- Versione 1.0.2866.1500: questo aggiornamento è stato rilasciato il 26 marzo 2021 e corregge un problema con il controllo dell'integrità dello stack.
- Versione 1.0.2800.2802: questo aggiornamento è stato rilasciato il 10 marzo 2021 e include miglioramenti generali e correzioni di bug.
- Versione 1.0.2800.2800: questo aggiornamento è stato rilasciato il 2 marzo 2021 e corregge un problema di connessione inversa.
- Versione 1.0.2800.2700: questo aggiornamento è stato rilasciato il 10 febbraio 2021 e include miglioramenti generali e correzioni di bug.
- Versione 1.0.2800.2700: questo aggiornamento è stato rilasciato il 4 febbraio 2021 e corregge un problema di orchestrazione per accesso negato.

## <a name="fslogix-updates"></a>Aggiornamenti di FSLogix

Per informazioni sugli aggiornamenti più recenti per FSLogix, Vedere [Novità in FSLogix.](/fslogix/whats-new)

## <a name="march-2021"></a>Marzo 2021

Ecco cosa è cambiato a marzo 2021.

### <a name="updates-to-the-azure-portal-ui-for-windows-virtual-desktop"></a>Aggiornamenti all'interfaccia portale di Azure per Desktop virtuale Windows

Sono stati apportati i seguenti aggiornamenti a Desktop virtuale Windows per l'portale di Azure:

- Sono stati abilitate nuove opzioni di disponibilità (set di disponibilità e zone) per i flussi di lavoro per creare pool di host e aggiungere macchine virtuali.
- È stato risolto un problema a causa del quale un host con lo stato "Richiede assistenza" risultava non disponibile. A questo punto l'host avrà un'icona di avviso accanto.
- È stato abilitato l'ordinamento per le sessioni attive.
- È ora possibile inviare messaggi a o disconnettere utenti specifici nella scheda dei dettagli dell'host.
- È stato modificato il campo limite massimo di sessioni.
- È stato aggiunto un percorso di convalida dell'unità organizzativa al flusso di lavoro per creare un pool di host.
- È ora possibile usare la versione più recente dell'immagine Windows 10 quando si crea un pool di host personali.

### <a name="generation-2-images-and-trusted-launch"></a>Immagini di seconda generazione e avvio attendibile

Il Azure Marketplace ora include immagini di seconda generazione per Windows 10 Enterprise e Windows 10 Enterprise multi-sessione. Queste immagini consentiranno di usare macchine virtuali di avvio attendibili. Per altre informazioni sulle macchine virtuali di seconda generazione, vedere [È consigliabile creare](../virtual-machines/generation-2.md)una macchina virtuale di prima o seconda generazione. Per informazioni su come effettuare il provisioning di macchine virtuali di avvio attendibili di Desktop virtuale Windows, vedere [il post di TechCommunity.](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/windows-virtual-desktop-support-for-trusted-launch/m-p/2206170)

### <a name="fslogix-is-now-preinstalled-on-windows-10-enterprise-multi-session-images"></a>FSLogix è ora preinstallato in Windows 10 Enterprise di più sessioni

In base ai commenti e suggerimenti dei clienti, è stata configurata una nuova versione dell'immagine Windows 10 Enterprise multi-sessione in cui è già installata una versione non configurata di FSLogix. Ci auguriamo che in questo modo la distribuzione di Desktop virtuale Windows sia più semplice.

### <a name="azure-monitor-for-windows-virtual-desktop-is-now-in-general-availability"></a>Monitoraggio di Azure desktop virtuale Windows è ora disponibile a disponibilità generale

Monitoraggio di Azure desktop virtuale Windows è ora disponibile a livello generale per il pubblico. Questa funzionalità è un servizio automatizzato che monitora le distribuzioni e consente di visualizzare eventi, integrità e suggerimenti per la risoluzione dei problemi in un'unica posizione. Per altre informazioni, vedere [la documentazione o](azure-monitor.md) consultare il post di [TechCommunity](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-is-generally-available/m-p/2242861).

### <a name="march-2021-updates-for-teams-on-windows-virtual-desktop"></a>Aggiornamenti di marzo 2021 per Teams in Desktop virtuale Windows

Sono stati apportati gli aggiornamenti seguenti per Teams in Desktop virtuale Windows:

- Sono state migliorate le prestazioni della qualità video per le chiamate e la modalità 2x2.
- L'utilizzo della CPU è stato ridotto del 5-10% (a seconda della generazione della CPU) usando l'offload hardware dell'elaborazione video (XVP).
- I computer meno recenti possono ora usare XVP e la decodifica hardware per visualizzare senza problemi più flussi video in ingresso in modalità 2x2.
- Lo stack WebRTC è stato aggiornato da M74 a M88 per migliorare le prestazioni di sincronizzazione av e un minor numero di problemi temporanei.
- Il codificatore H264 software è stato sostituito con OpenH264 (OSS usato in Teams sul Web), che ha aumentato la qualità video della fotocamera in uscita.
- La modalità 2x2 per Teams Server è stata abilitata per il pubblico generale il 30 marzo. La modalità 2x2 mostra fino a quattro flussi video in ingresso contemporaneamente.

### <a name="start-vm-on-connect-public-preview"></a>Avviare la macchina virtuale nell'anteprima pubblica connect

La nuova impostazione del pool di host, Avvia macchina virtuale alla connessione, è ora disponibile in anteprima pubblica. Questa impostazione consente di attivare le macchine virtuali ogni volta che sono necessarie. Per risparmiare sui costi, è necessario deallocare le macchine virtuali configurando le impostazioni Calcolo di Azure servizio. Per altre informazioni, vedere il [post di blog e](https://aka.ms/wvdstartvmonconnect) la [documentazione.](start-virtual-machine-connect.md)

### <a name="windows-virtual-desktop-specialty-certification"></a>Certificazione Windows Virtual Desktop Specialty

È stata rilasciata una versione beta dell'esame AZ-140 che consente di dimostrare le proprie competenze in Desktop virtuale Windows in Azure. Per altre informazioni, vedere il [post di TechCommunity](https://techcommunity.microsoft.com/t5/microsoft-learn-blog/beta-exam-prove-your-expertise-in-windows-virtual-desktop-on/ba-p/2147107).

## <a name="february-2021"></a>Febbraio 2021

Ecco cosa è cambiato a febbraio 2021.

### <a name="portal-experience"></a>Funzionalità del portale

L'esperienza di portale di Azure è stata migliorata nei modi seguenti:

- Modalità svuotamento bulk negli host nella scheda della griglia dell'host della sessione. 
- montaggio app MSIX è ora disponibile per l'anteprima pubblica.
- Correzione delle informazioni generali sul pool di host per la modalità scura.

### <a name="eu-metadata-storage-now-in-public-preview"></a>Archiviazione dei metadati dell'Unione Europea ora in anteprima pubblica

È ora disponibile un'anteprima pubblica dell'area geografica Europa (UE) come opzione di archiviazione per i metadati del servizio in Desktop virtuale Windows. I clienti possono scegliere tra Europa occidentale o Europa settentrionale quando creano gli oggetti servizio. Gli oggetti servizio e i metadati per i pool di host verranno archiviati nell'area geografica di Azure associata a ogni area. Per altre informazioni, leggere [il post di blog che annuncia l'anteprima pubblica.](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/announcing-public-preview-of-windows-virtual-desktop-service/m-p/2143939)

### <a name="teams-on-windows-virtual-desktop-plugin-updates"></a>Aggiornamenti del plug-in di Desktop virtuale Windows di Teams

La qualità delle chiamate video nel plug-in Desktop virtuale Windows è stata migliorata grazie alla risoluzione dei problemi segnalati più di frequente, ad esempio quando lo schermo diventa improvvisamente scuro o il video e l'audio vengono desincronizzazioneti. Questi miglioramenti dovrebbero migliorare le prestazioni della visualizzazione di un singolo video con il cambio di voce attivo. È stato anche risolto un problema a causa del quale i dispositivi hardware con caratteri speciali non erano disponibili in Teams.

## <a name="january-2021"></a>Gennaio 2021

Ecco cosa è cambiato a gennaio 2021:

### <a name="new-windows-virtual-desktop-offer"></a>Nuova offerta di Desktop virtuale Windows

I nuovi clienti risparmiano il 30% sui costi di elaborazione di Desktop virtuale Windows per le macchine virtuali serie D e Bs per un massimo di 90 giorni quando si usa la soluzione Microsoft nativa. È possibile riscattare l'offerta portale di Azure prima del 31 marzo 2021. Per altre informazioni, vedere la [pagina dell'offerta desktop virtuale Windows.](https://azure.microsoft.com/services/virtual-desktop/offer/)

### <a name="networksecuritygrouprules-value-change"></a>Modifica del valore di networkSecurityGroupRules 

Nel modello Azure Resource Manager annidato è stato modificato il valore predefinito per networkSecurityGroupRules da un oggetto a una matrice. In questo modo si evitano errori se si managedDisks-customimagevm.jssu senza specificare un valore per networkSecurityGroupRules. Questa non è stata una modifica di rilievo ed è compatibile con le versioni precedenti.

### <a name="fslogix-hotfix-update"></a>FsLogix hotfix update

È stato rilasciato FSLogix, versione 2009 HF_01 (2.9.7654.46150) per risolvere i problemi della versione precedente (2.9.7621.30127). È consigliabile interrompere l'uso della versione precedente e aggiornare FSLogix appena possibile.

Per altre informazioni, vedere le note sulla versione in [Novità di FSLogix.](/fslogix/whats-new#fslogix-apps-2009-hf_01-29765446150)

### <a name="azure-portal-experience-improvements"></a>portale di Azure'esperienza

Sono stati apportati i miglioramenti seguenti all'esperienza portale di Azure funzionalità:

- È ora possibile aggiungere direttamente le credenziali di amministratore della macchina virtuale locale invece di dover aggiungere un account locale creato con le credenziali dell'account di aggiunta al dominio di Active Directory.
- Gli utenti possono ora elencare le assegnazioni di singoli utenti e gruppi in schede separate per singoli utenti e gruppi.
- Il numero di versione dell'agente di Desktop virtuale Windows è ora visibile nella panoramica della macchina virtuale per i pool di host.
- Aggiunta dell'eliminazione in blocco per i pool di host e i gruppi di applicazioni.
- È ora possibile abilitare o disabilitare la modalità svuotamento per più host di sessione in un pool di host.
- È stato rimosso il campo ip pubblico dalla pagina dei dettagli della macchina virtuale.

### <a name="windows-virtual-desktop-agent-troubleshooting"></a>Risoluzione dei problemi dell'agente di Desktop virtuale Windows

Di recente è stata impostata la [guida alla risoluzione dei](troubleshoot-agent.md) problemi dell'agente di Desktop virtuale Windows per aiutare i clienti che hanno riscontrato problemi comuni.

### <a name="microsoft-defender-for-endpoint-integration"></a>Integrazione di Microsoft Defender per endpoint

L'integrazione di Microsoft Defender per endpoint è ora disponibile a livello generale. Questa funzionalità offre alle macchine virtuali Desktop virtuale Windows la stessa esperienza di analisi di un computer Windows 10 locale. Se si usa Windows 10 Enterprise più sessioni, Microsoft Defender per Endpoint supporterà fino Windows 10 Enterprise 50 connessioni utente simultanee, con un risparmio sui costi di Windows 10 Enterprise più sessioni e la fiducia di Microsoft Defender per l'endpoint. Per altre informazioni, vedere il [post di blog](https://techcommunity.microsoft.com/t5/microsoft-defender-for-endpoint/windows-virtual-desktop-support-is-now-generally-available/ba-p/2103712).

### <a name="azure-security-baseline-for-windows-virtual-desktop"></a>Baseline di sicurezza di Azure per Desktop virtuale Windows

Di recente è stato pubblicato [un articolo sulla baseline](security-baseline.md) di sicurezza di Azure per Desktop virtuale Windows a cui si desidera richiamare l'attenzione dell'utente. Queste linee guida includono informazioni su come applicare Azure Security Benchmark versione 2.0 a Desktop virtuale Windows. Azure Security Benchmark descrive le impostazioni e le procedure consigliate per proteggere le soluzioni cloud in Azure.

## <a name="december-2020"></a>Dicembre 2020

Ecco cosa è cambiato a dicembre 2020: 

### <a name="azure-monitor-for-windows-virtual-desktop"></a>Monitoraggio di Azure per Desktop virtuale Windows

È ora disponibile l'anteprima pubblica di Monitoraggio di Azure per Desktop virtuale Windows. Questa nuova funzionalità include un dashboard affidabile basato sulle cartelle di lavoro di Monitoraggio di Azure per offrire ai professionisti IT informazioni sugli ambienti di Desktop virtuale Windows. Per altri dettagli, vedere l'[annuncio nel blog](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/azure-monitor-for-windows-virtual-desktop-public-preview/m-p/1946587). 

### <a name="azure-resource-manager-template-change"></a>Modifica dei modelli di Azure Resource Manager 

Nell'ultimo aggiornamento sono stati rimossi tutti i parametri di indirizzi IP pubblici dal modello di Resource Manager per la creazione e il provisioning di pool di host. Per garantire la sicurezza della distribuzione, è consigliabile evitare di usare indirizzi IP pubblici per Desktop virtuale Windows. Se la distribuzione è basata su indirizzi IP pubblici, è necessario riconfigurarla per l'uso di indirizzi IP privati. In caso contrario, la distribuzione non funzionerà correttamente.

### <a name="msix-app-attach-public-preview"></a>Anteprima pubblica di MSIX app attach 

MSIX app attach è un altro servizio per cui è stata avviata l'anteprima pubblica questo mese. Si tratta di un servizio che presenta dinamicamente le applicazioni MSIX nelle VM host di sessione di Desktop virtuale Windows. Per altri dettagli, vedere l'[annuncio nel blog](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/msix-app-attach-azure-portal-integration-public-preview/m-p/1986231). 

### <a name="screen-capture-protection"></a>Protezione dall'acquisizione schermo 

Questo mese è stata anche avviata l'anteprima pubblica della protezione dall'acquisizione schermo. È possibile usare questa funzionalità per impedire l'acquisizione di informazioni riservate sugli endpoint client. Per provare questa funzionalità, passare a [questa pagina](https://aka.ms/WVDScreenCaptureProtection).  

### <a name="built-in-roles"></a>Ruoli predefiniti

Sono stati aggiunti nuovi ruoli predefiniti per le autorizzazioni di amministratore per Desktop virtuale Windows. Per altre informazioni, vedere [Ruoli predefiniti per Desktop virtuale Windows](rbac.md). 

### <a name="application-group-limit-increase"></a>Aumento del limite di gruppi di applicazioni

Il limite predefinito di gruppi di applicazioni per ogni tenant di Azure Active Directory è stato aumentato a 200.

### <a name="client-updates-for-december-2020"></a>Aggiornamenti dei client di dicembre 2020

Sono state rilasciate nuove versioni dei client seguenti: 

- Android
- macOS
- Windows

Per altre informazioni, vedere [Aggiornamenti dei client](whats-new.md#client-updates).

## <a name="november-2020"></a>Novembre 2020

### <a name="azure-portal-experience"></a>Esperienza del portale di Azure

Sono stati corretti due bug nell'esperienza utente del portale di Azure:

- Il nome descrittivo dell'applicazione desktop non viene più sovrascritto nel flusso di lavoro "Aggiungi macchina virtuale".
- La scheda Host di sessione viene ora caricata se gli host di sessione fanno parte di set di scalabilità.

### <a name="fslogix-client-version-2009"></a>Client FSLogix, versione 2009 

È stata rilasciata una nuova versione del client FSLogix con numerosi miglioramenti e correzioni. Per altre informazioni, vedere il [post di blog](https://social.msdn.microsoft.com/Forums/en-US/defe5828-fba4-4715-a68c-0e4d83eefa6b/release-notes-for-fslogix-apps-release-2009-29762130127?forum=FSLogix).

### <a name="rdp-shortpath-public-preview"></a>Anteprima pubblica di RDP Shortpath

RDP Shortpath introduce la connessione diretta all'host di sessione di Desktop virtuale Windows tramite VPN da punto a sito e da sito a sito e tramite ExpressRoute. Introduce anche il protocollo di trasporto URCP. RDP Shortpath è progettato per ridurre la latenza e gli hop di rete al fine di migliorare l'esperienza utente. Per altre informazioni, vedere [RDP Shortpath di Desktop virtuale Windows](shortpath.md).

### <a name="azdesktopvirtualization-version-201"></a>Az.DesktopVirtualization, versione 2.0.1

È stata rilasciata la versione 2.0.1 dei cmdlet di Desktop virtuale Windows. Questo aggiornamento include cmdlet che consentono di gestire il montaggio app MSIX. È possibile scaricare la nuova versione da [PowerShell Gallery](https://www.powershellgallery.com/packages/Az.DesktopVirtualization/2.0.1).

### <a name="azure-advisor-updates"></a>Aggiornamenti di Azure Advisor

Azure Advisor ha una nuova raccomandazione per le linee guida sulla prossimità in Desktop virtuale Windows e una nuova raccomandazione per ottimizzare le prestazioni nei pool di host con bilanciamento del carico in profondità. Pe altre informazioni, visitare il [sito Web di Azure](https://azure.microsoft.com/updates/new-recommendations-from-azure-advisor/).

## <a name="october-2020"></a>Ottobre 2020

Ecco cosa è cambiato in ottobre 2020:

### <a name="improved-performance"></a>prestazioni migliorate

- Sono state ottimizzate le prestazioni riducendo la latenza di connessione nelle aree geografiche di Azure seguenti:
    - Svizzera
    - Canada

È ora possibile usare lo [strumento di valutazione dell'esperienza](https://azure.microsoft.com/services/virtual-desktop/assessment/) per stimare la qualità dell'esperienza utente in queste aree.

### <a name="azure-government-cloud-availability"></a>Disponibilità del Cloud Azure per enti pubblici

Il Cloud Azure per enti pubblici è ora disponibile a livello generale. Per altre informazioni, vedere il [post di blog](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-generally-available-in-the-azure-government-cloud/).

### <a name="windows-virtual-desktop-azure-portal-updates"></a>Aggiornamenti a Desktop virtuale Windows nel portale di Azure

Sono stati apportati alcuni aggiornamenti a Desktop virtuale Windows nel portale di Azure:

- È stato corretto un errore resourceID che impediva agli utenti di aprire la scheda "Sessioni".
- L'interfaccia utente della scheda "Host di sessione" è stata semplificata.
- Sono state corrette le impostazioni "Valori predefiniti," "Usabilità"e "Ripristina impostazioni predefinite" nelle proprietà RDP.
- Le funzioni "Rimuovi" ed "Elimina" sono state rese coerenti in tutte le schede.
- Il portale ora convalida i nomi delle app nel flusso di lavoro "Aggiungi un'app".
- È stato risolto un problema di disallineamento dei dati esportati dagli host di sessione nelle colonne.
- È stato risolto un problema a causa del quale non era possibile recuperare le sessioni utente nel portale.
- È stato risolto un problema di recupero dell'host di sessione che si verificava quando la macchina virtuale veniva creata in un gruppo di risorse diverso.
- È stata aggiornata la scheda "Host di sessione" in modo da elencare sia le sessioni attive che quelle disconnesse.
- La scheda "Applicazioni" ora ha più pagine.
- È stato risolto un problema a causa del quale il testo "richiede la riga di comando" non veniva visualizzato correttamente nella scheda "Elenco applicazioni".
- È stato risolto un problema a causa del quale non era possibile distribuire pool di host o macchine virtuali tramite il portale usando la versione in lingua tedesca della Raccolta immagini condivise.

### <a name="client-updates-for-october-2020"></a>Aggiornamenti client di ottobre 2020

Sono state rilasciate nuove versioni dei client. Per altre informazioni, vedere questi articoli:

- [Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop-whatsnew)
- [iOS](/windows-server/remote/remote-desktop-services/clients/ios-whatsnew)

Per altre informazioni sugli altri client, vedere [Aggiornamenti client](#client-updates).

## <a name="september-2020"></a>Settembre 2020

Ecco cosa è cambiato a settembre 2020:

- Sono state ottimizzate le prestazioni riducendo la latenza di connessione nelle aree geografiche di Azure seguenti:
    - Germania
    - Sudafrica (solo per gli ambienti di convalida)

È ora possibile usare lo [strumento di valutazione dell'esperienza](https://azure.microsoft.com/services/virtual-desktop/assessment/) per stimare la qualità dell'esperienza utente in queste aree.

- È stata rilasciata la versione 1.2.1364 del client Windows Desktop per Desktop virtuale Windows. In questo aggiornamento sono state apportate le modifiche seguenti:
    - Risoluzione di un problema a causa del quale il processo Single Sign-On (SSO) non funzionava in Windows 7.
    - È stato risolto un problema che causava la disconnessione del client quando un utente che aveva abilitato l'ottimizzazione del contenuto multimediale per Teams tentava di chiamare o di partecipare a una riunione di Teams mentre un'altra app aveva un flusso audio aperto in modalità esclusiva.
    - È stato risolto un problema a causa del quale Teams non enumerava i dispositivi audio o video quando era abilitata l'ottimizzazione del contenuto multimediale per Teams.
    - Aggiunta di un collegamento per ottenere assistenza con le impostazioni nella pagina delle impostazioni desktop.
    - Risoluzione di un problema relativo al pulsante "Sottoscrivi" che si verificava quando si usavano temi scuri a contrasto elevato.
    
- Grazie all'enorme aiuto degli utenti, sono stati risolti due problemi critici per il client Desktop remoto di Microsoft Store. Microsoft continuerà a esaminare il feedback e a risolvere i problemi man mano che il rilascio graduale del client viene esteso a un numero sempre maggiore di utenti in tutto il mondo.
    
- È stata aggiunta una nuova funzionalità che consente di modificare la posizione, l'immagine, il gruppo di risorse, il nome del prefisso e la configurazione di rete di una macchina virtuale nell'ambito del flusso di lavoro per l'aggiunta di una VM alla distribuzione nel portale di Azure.

- I professionisti IT possono ora gestire le VM Windows 10 Enterprise aggiunte ad Azure Active Directory ibrido tramite Microsoft Endpoint Manager. Per altre informazioni, vedere il [post di blog](https://techcommunity.microsoft.com/t5/microsoft-endpoint-manager-blog/microsoft-endpoint-manager-announces-support-for-windows-virtual/ba-p/1681048).

## <a name="august-2020"></a>Agosto 2020

Ecco cosa è cambiato in agosto 2020:

- Sono state migliorate le prestazioni per ridurre la latenza di connessione nelle aree di Azure seguenti: 

    - Regno Unito
    - Francia
    - Norvegia
    - Corea del Sud

   È possibile usare lo [strumento di valutazione dell'esperienza](https://azure.microsoft.com/services/virtual-desktop/assessment/) per farsi un'idea generale dell'impatto di questi cambiamenti sugli utenti.

- Il Client Desktop remoto di Microsoft Store (v10.2.1522+) è ora disponibile a livello generale. Questa versione del client è compatibile con Desktop virtuale Windows. Sono stati introdotti anche flussi di interfaccia utente aggiornati per migliorare l'esperienza utente. Questo aggiornamento include Fluent Design, le modalità chiara e scura e molti altri cambiamenti interessanti. Il client è stato inoltre riscritto in modo da usare lo stesso motore RDP (Remote Desktop Protocol) sottostante dei client iOS, macOS e Android. Questo velocizza la distribuzione delle nuove funzionalità su tutte le piattaforme. [Scaricare il client](https://www.microsoft.com/p/microsoft-remote-desktop/9wzdncrfj3ps?rtc=1&activetab=pivot:overviewtab) per provarlo.

- È stato risolto un problema nel client di Teams Desktop (versione 1.3.00.21759) a causa del quale il client mostrava solo il fuso orario UTC nella chat, nei canali e nel calendario. Il client aggiornato ora mostra il fuso orario della sessione remota.

- Azure Advisor fa ora parte di Desktop virtuale Windows. Quando si accede a Desktop virtuale Windows tramite il portale di Azure, è possibile visualizzare i suggerimenti per l'ottimizzazione dell'ambiente di Desktop virtuale Windows. Altre informazioni su [Azure Advisor](azure-advisor.md).

- L'interfaccia della riga di comando di Azure ora supporta Desktop virtuale Windows (`az desktopvirtualization`) per facilitare l'automazione delle distribuzioni di Desktop virtuale Windows. Per un elenco dei comandi dell'estensione, vedere [desktopvirtualization](/cli/azure/ext/desktopvirtualization/).

- I modelli di distribuzione sono stati aggiornati per renderli completamente compatibili con le interfacce di Azure Resource Manager e Desktop virtuale Windows. I modelli sono disponibili su [GitHub](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates).

- Desktop virtuale Windows è ora disponibile in anteprima pubblica nelle aree US Gov insieme all'integrazione con il portale di Azure. Per altre informazioni, vedere questo [annuncio](https://azure.microsoft.com/updates/windows-virtual-desktop-is-now-available-in-the-azure-government-cloud-in-preview/).

## <a name="july-2020"></a>Luglio 2020  

Nel mese di luglio è diventato disponibile a livello generale Desktop virtuale Windows con l'integrazione con Azure Resource Manager.

Ecco le modifiche apportate in questa nuova versione: 

- La "versione Autunno 2019" è ora denominata "Desktop virtuale Windows (versione classica)", mentre la "versione Primavera 2020" è ora semplicemente "Desktop virtuale Windows". Per altre informazioni, vedere [questo post di blog](https://azure.microsoft.com/blog/new-windows-virtual-desktop-capabilities-now-generally-available/). 

Per altre informazioni sulle nuove funzionalità, vedere [questo post di blog](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245). 

### <a name="autoscaling-tool-update"></a>Aggiornamento dello strumento di scalabilità automatica

L'ultima versione dello strumento di scalabilità automatica, precedentemente in anteprima, è ora disponibile a livello generale. Questo strumento usa un account di automazione di Azure e un'app per la logica di Azure per arrestare e riavviare automaticamente le macchine virtuali (VM) host della sessione all'interno di un pool di host, riducendo i costi dell'infrastruttura. Per altre informazioni, vedere [Ridimensionare gli host di sessione con Automazione di Azure](set-up-scaling-script.md).

### <a name="azure-portal"></a>Portale di Azure

È ora possibile eseguire le operazioni seguenti con il portale di Azure in Desktop virtuale Windows: 

- Assegnare direttamente gli utenti agli host di sessione del desktop personale  
- Modificare l'impostazione dell'ambiente di convalida per i pool di host 

### <a name="diagnostics"></a>Diagnostica

Sono state rilasciate alcune nuove query predefinite per l'area di lavoro Log Analytics. Per accedere alle query, passare a **Log** e selezionare **Desktop virtuale Windows** in **Categoria**. Per altre informazioni, vedere [Usare Log Analytics per la funzionalità di diagnostica](diagnostics-log-analytics.md).

### <a name="update-for-remote-desktop-client-for-android"></a>Aggiornamento per il client Desktop remoto per Android

Il [client Desktop remoto per Android](https://play.google.com/store/apps/details?id=com.microsoft.rdc.androidx) supporta ora le connessioni a Desktop virtuale Windows. A partire dalla versione 10.0.7, il client Android offre una nuova interfaccia utente per una migliore esperienza utente. Il client si integra anche con Microsoft Authenticator nei dispositivi Android per abilitare l'accesso condizionale per la sottoscrizione di aree di lavoro Desktop virtuale Windows.  

La versione precedente del client Desktop remoto è ora denominata "Desktop remoto 8". Tutte le connessioni esistenti nella versione precedente del client verranno trasferite senza problemi al nuovo client. Il nuovo client è stato riscritto per lo stesso motore di base RDP sottostante dei client iOS e macOS, per velocizzare il rilascio delle nuove funzionalità in tutte le piattaforme. 

### <a name="teams-update"></a>Aggiornamento di Teams

Sono stati apportati alcuni miglioramenti a Microsoft Teams per Desktop virtuale Windows. Desktop virtuale Windows supporta ora soprattutto l'ottimizzazione audio e video per il client desktop Windows. Il reindirizzamento migliora la latenza creando percorsi diretti tra gli utenti quando effettuano chiamate e riunioni con audio o video. Una distanza minore comporta meno hop, quindi la chiamata ha un aspetto e un audio migliori. Per altre informazioni, vedere [Usare Teams in Desktop virtuale Windows](teams-on-wvd.md).

## <a name="june-2020"></a>Giugno 2020

Il mese scorso è stato presentato Desktop virtuale Windows con l'integrazione con Azure Resource Manager in anteprima. Questo aggiornamento offre molte nuove funzionalità straordinarie. Ecco le novità per questa versione di Desktop virtuale Windows.

### <a name="windows-virtual-desktop-is-now-integrated-with-azure-resource-manager"></a>Integrazione di Desktop virtuale Windows con Azure Resource Manager

Desktop virtuale Windows è ora integrato in Azure Resource Manager. Nell'aggiornamento più recente tutti gli oggetti di Desktop virtuale Windows sono ora risorse di Azure Resource Manager. Questo aggiornamento è integrato anche con il controllo degli accessi in base al ruolo di Azure. Per altre informazioni, vedere [Informazioni su Azure Resource Manager](../azure-resource-manager/management/overview.md).

Questa modifica ha gli effetti seguenti:

- Desktop virtuale Windows è ora integrato con il portale di Azure. È quindi possibile gestire tutto direttamente nel portale, senza che sia necessario usare PowerShell, app Web o strumenti di terze parti. Per iniziare, vedere l'esercitazione [Creare un pool di host con il portale di Azure](create-host-pools-azure-marketplace.md).

- Prima dell'aggiornamento era possibile pubblicare RemoteApps e desktop solo per singoli utenti. Grazie ad Azure Resource Manager, è ora possibile pubblicare risorse nei gruppi di Azure Active Directory.

- La versione precedente di Desktop virtuale Windows include quattro ruoli di amministratore predefiniti che è possibile assegnare a un tenant o a un pool di host. Questi ruoli sono ora disponibili nel [controllo degli accessi in base al ruolo di Azure](../role-based-access-control/overview.md). È possibile applicare questi ruoli a ogni oggetto di Azure Resource Manager di Desktop virtuale Windows, in modo da ottenere un modello di delega completo e avanzato.

- In questo aggiornamento non è più necessario eseguire Azure Marketplace o il modello di GitHub ripetutamente per espandere un pool di host. Per espandere un pool di host è sufficiente passare al pool di host nel portale di Azure e selezionare **+ Aggiungi** per distribuire host di sessione aggiuntivi.

- La distribuzione di pool di host è ora completamente integrata con la [Raccolta immagini condivise di Azure](../virtual-machines/shared-image-galleries.md). La Raccolta immagini condivise è un servizio separato di Azure che consente di archiviare definizioni delle immagini di macchine virtuali, incluso il controllo delle versioni delle immagini. È anche possibile usare la replica globale per copiare e inviare immagini ad altre aree di Azure per la distribuzione locale.

- Le funzioni di monitoraggio eseguite in precedenza tramite PowerShell o l'app Web Servizio diagnostica sono ora state spostate in Log Analytics nel portale di Azure. Sono inoltre ora disponibili due opzioni per visualizzare i report. È possibile eseguire query di Kusto e usare Cartelle di lavoro per creare report visivi.

- Non è più necessario completare il consenso di Azure Active Directory (Azure AD) per usare Desktop virtuale Windows. In questo aggiornamento, il tenant di Azure AD nella sottoscrizione di Azure esegue l'autenticazione degli utenti e fornisce i controlli degli accessi in base al ruolo di Azure per gli amministratori.

### <a name="powershell-support"></a>Supporto PowerShell

Sono stati aggiunti nuovi cmdlet di AzWvd al modulo Az di Azure PowerShell con questo aggiornamento. Questo nuovo modulo è supportato in PowerShell Core, in esecuzione in .NET Core.

Per installare il modulo, seguire le istruzioni disponibili in [Configurare il modulo PowerShell per Desktop virtuale Windows](powershell-module.md).

È anche possibile visualizzare un elenco dei comandi disponibili nelle [informazioni di riferimento su AzWvd PowerShell](/powershell/module/az.desktopvirtualization/#desktopvirtualization).

Per altre informazioni sulle nuove funzionalità, vedere il [post di blog](https://techcommunity.microsoft.com/t5/itops-talk-blog/windows-virtual-desktop-spring-update-enters-public-preview/ba-p/1340245).

### <a name="additional-gateways"></a>Gateway aggiuntivi

È stato aggiunto un nuovo cluster di gateway in Sudafrica per ridurre la latenza della connessione.

### <a name="microsoft-teams-on-windows-virtual-desktop-preview"></a>Microsoft Teams in Desktop virtuale Windows (anteprima)

Sono stati apportati alcuni miglioramenti a Microsoft Teams per Desktop virtuale Windows. Desktop virtuale Windows supporta ora soprattutto il reindirizzamento audio e visivo per le chiamate. Il reindirizzamento migliora la latenza mediante la creazione di percorsi diretti tra gli utenti quando effettuano una chiamata con audio o video. Una distanza minore comporta meno hop, quindi la chiamata ha un aspetto e un audio migliori.

Per altre informazioni, vedere il [post di blog](https://azure.microsoft.com/updates/windows-virtual-desktop-media-optimization-for-microsoft-teams-is-now-available-in-public-preview/).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui piani futuri, vedere la [roadmap di Desktop virtuale Windows in Microsoft 365](https://www.microsoft.com/microsoft-365/roadmap?filters=Windows%20Virtual%20Desktop).
