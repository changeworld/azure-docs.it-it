---
title: Domande frequenti sulla protezione password di Azure AD locale
description: Esaminare le domande frequenti per Azure AD la protezione delle password in un ambiente Active Directory Domain Services locale
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: f80990854fd0c584d8e6582fdf35108e67d9202b
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625129"
---
# <a name="azure-ad-password-protection-on-premises-frequently-asked-questions"></a>Domande frequenti sulla protezione Azure AD password locale

In questa sezione vengono fornite le risposte a molte domande frequenti sulla protezione Azure AD password.

## <a name="general-questions"></a>Domande generali

**D: quali linee guida è necessario assegnare agli utenti per la selezione di una password sicura?**

Le indicazioni correnti di Microsoft su questo argomento sono disponibili nella pagina Web a cui si accede tramite il collegamento seguente:

[Microsoft Password Guidance](https://www.microsoft.com/research/publication/password-guidance) (Indicazioni di Microsoft sulle password)

**D: la protezione delle password in locale Azure AD supportata in cloud non pubblici?**

La protezione Azure AD password locale è supportata nel cloud pubblico e nel cloud di Arlington. Non è stata annunciata alcuna data per la disponibilità in altri cloud.

Il portale di Azure AD consente di modificare la configurazione di "Password Protection for Windows Server Active Directory" specifica locale anche in cloud non supportati. tali modifiche verranno rese permanente, ma in caso contrario non saranno mai effettive. La registrazione di foreste o agenti proxy locali non è supportata in cloud non supportati e i tentativi di registrazione avranno sempre esito negativo.

**D: in che modo è possibile applicare Azure AD vantaggi della protezione delle password a un sottoinsieme di utenti locali?**

Non supportata. Dopo la distribuzione e l'abilitazione, Password di protezione di Azure AD non prevede alcuna discriminazione: tutti gli utenti ottengono uguali vantaggi per la sicurezza.

**D: qual è la differenza tra una modifica della password e un set di password (o reimpostazione)?**

Una modifica della password si verifica quando un utente sceglie una nuova password dopo avere dimostrato la conoscenza della vecchia password. Ad esempio, una modifica della password è ciò che accade quando un utente accede a Windows e viene quindi richiesto di scegliere una nuova password.

Un set di password (talvolta denominato reimpostazione della password) si verifica quando un amministratore sostituisce la password di un account con una nuova password, ad esempio tramite lo strumento di gestione Active Directory utenti e computer. Questa operazione richiede un livello elevato di privilegi (in genere amministratore di dominio) e la persona che esegue l'operazione in genere non conosce la vecchia password. Gli scenari di supporto tecnico eseguono spesso set di password, ad esempio quando si assiste a un utente che ha dimenticato la password. Gli eventi di impostazione della password vengono visualizzati anche quando viene creato un nuovo account utente per la prima volta con una password.

I criteri di convalida della password si comportano allo stesso modo, indipendentemente dal fatto che sia stata eseguita una modifica o un set di password. Il servizio Azure AD Password Protection Agent esegue il log di eventi diversi per indicare se è stata eseguita una modifica della password o un'operazione di impostazione.  Vedere [Azure ad monitoraggio e registrazione per la protezione delle password](./howto-password-ban-bad-on-premises-monitor.md).

**D: le password esistenti vengono convalidate da Azure AD password per la protezione dopo l'installazione?**

No-Azure AD Password Protection può applicare i criteri password solo per le password non crittografate durante un'operazione di modifica o impostazione della password. Quando una password viene accettata da Active Directory, vengono mantenuti solo gli hash specifici del protocollo di autenticazione di tale password. La password di testo non crittografato non viene mai mantenute, pertanto Azure AD la protezione delle password non può convalidare le password esistenti.

Dopo la distribuzione iniziale di Azure AD la protezione con password, tutti gli utenti e gli account inizieranno a usare una password Azure AD convalidata per la protezione con password, perché le password esistenti scadono normalmente nel tempo. Se lo si desidera, questo processo può essere accelerato da una singola scadenza manuale delle password degli account utente.

Gli account configurati con "Nessuna scadenza password" non verranno mai costretti a modificare la password, a meno che non venga eseguita la scadenza manuale.

**D: perché vengono registrati gli eventi di rifiuto delle password duplicati quando si tenta di impostare una password vulnerabile mediante lo snap-in gestione Active Directory utenti e computer?**

Lo snap-in gestione utenti e computer Active Directory tenterà innanzitutto di impostare la nuova password utilizzando il protocollo Kerberos. In caso di errore, lo snap-in effettuerà un secondo tentativo di impostazione della password utilizzando un protocollo legacy (SAM RPC) (i protocolli specifici utilizzati non sono importanti). Se la nuova password viene considerata debole dalla protezione Azure AD password, questo comportamento dello snap-in comporterà la registrazione di due set di eventi di rifiuto di reimpostazione della password.

**D: perché vengono registrati gli eventi di convalida password Azure AD Password Protection con un nome utente vuoto?**

Active Directory supporta la possibilità di testare una password per verificare se supera i requisiti di complessità della password correnti del dominio, ad esempio usando l'API [NetValidatePasswordPolicy](/windows/win32/api/lmaccess/nf-lmaccess-netvalidatepasswordpolicy) . Quando una password viene convalidata in questo modo, il test include anche la convalida da parte di prodotti basati su dll con filtro password, ad esempio Azure AD la protezione con password, ma i nomi utente passati a una determinata DLL di filtro password saranno vuoti. In questo scenario, Azure AD la protezione con password convaliderà la password usando i criteri della password attualmente in vigore ed emetterà un messaggio del registro eventi per acquisire il risultato, ma il messaggio del registro eventi avrà campi nome utente vuoti.

**D: è supportata l'installazione side-by-side di Azure AD la protezione delle password con altri prodotti basati sul filtro password?**

Sì. Il supporto di più DLL di filtro password registrate è una funzionalità di base di Windows e non è specifico della protezione password di Azure AD. Tutte le DLL di filtro password registrate devono concordare, prima che venga accettata una password.

**D: come è possibile distribuire e configurare Azure AD la protezione delle password nell'ambiente Active Directory senza usare Azure?**

Non supportata. Password di protezione di Azure AD è una funzionalità di Azure che supporta l'estensione in un ambiente Active Directory locale.

**D: come è possibile modificare il contenuto del criterio a livello di Active Directory?**

Non supportata. È possibile amministrare i criteri solo usando il portale di Azure AD. Vedere anche la domanda precedente.

**D: perché è necessario il servizio DFSR per la replica di sysvol?**

Il servizio Replica file (la tecnologia precedente a DFSR) include molti problemi noti e non è completamente supportato nelle versioni più recenti di Windows Server Active Directory. La protezione password di Azure AD non verrà testata nei domini configurati con il servizio Replica file.

Per altre informazioni, vedere gli articoli seguenti:

[The Case for Migrating sysvol replication to DFSR](/archive/blogs/askds/the-case-for-migrating-sysvol-to-dfsr) (Caso della migrazione della replica di sysvol a DFSR)

[The End is Nigh for FRS](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs) (La fine è vicina per il servizio Replica file)

Se il dominio non sta già usando DFSR, è necessario eseguirne la migrazione per usare DFSR prima di installare Azure AD la protezione delle password. Per ulteriori informazioni, vedere il collegamento seguente:

[Guida alla migrazione della replica SYSVOL: Replica da FRS a DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

> [!WARNING]
> Il software dell'agente del controller di dominio Azure AD Password Protection verrà attualmente installato nei controller di dominio in domini che utilizzano ancora FRS per la replica SYSVOL, ma il software non funzionerà correttamente in questo ambiente. Gli effetti collaterali negativi aggiuntivi includono i singoli file che non riescono a eseguire la replica e le procedure di ripristino di SYSVOL sembrano avere esito positivo ma non riescono a replicare tutti i file. È consigliabile eseguire la migrazione del dominio per usare DFSR il prima possibile, sia per i vantaggi intrinseci di DFSR che per sbloccare la distribuzione di Azure AD la protezione delle password. Le versioni future del software verranno disabilitate automaticamente durante l'esecuzione in un dominio che continua a usare il servizio Replica file.

**D: qual è la quantità di spazio su disco necessaria per la funzionalità nella condivisione SYSVOL del dominio?**

La quantità esatta di spazio usato dipende da fattori come il numero e la lunghezza dei token esclusi contenuti nell'elenco globale degli elementi esclusi Microsoft e nell'elenco personalizzato del singolo tenant, nonché dal sovraccarico della crittografia. Il contenuto di questi elenchi probabilmente aumenterà in futuro. Tenendo presente tale situazione, è ragionevole prevedere che per la funzionalità siano necessari almeno cinque (5) megabyte di spazio nella condivisione sysvol del dominio.

**D: perché è necessario un riavvio per installare o aggiornare il software dell'agente del controller di dominio?**

Questo requisito dipende dal comportamento di base di Windows.

**D: è disponibile un modo per configurare un agente del controller di dominio per l'uso di un server proxy specifico?**

No. Poiché il server proxy è senza stato, non ha importanza quale server proxy specifico venga usato.

**D: è possibile distribuire il servizio proxy Azure AD password protection side-by-side con altri servizi, ad esempio Azure AD Connect?**

Sì. Il servizio proxy di Password di protezione di Azure AD e Azure AD Connect non devono essere mai direttamente in conflitto l'uno con l'altro.

Sfortunatamente, è stata rilevata un'incompatibilità tra la versione del servizio Microsoft Azure AD Connect Agent Updater installata dal software proxy di Azure AD Password Protection e la versione del servizio installata dal software [Azure Active Directory Application Proxy](../manage-apps/application-proxy.md) . Questa incompatibilità può comportare la mancata riuscita del servizio Agent Updater per contattare Azure per gli aggiornamenti software. Non è consigliabile installare Azure AD proxy di protezione con password e Azure Active Directory Application Proxy nello stesso computer.

**D: in che ordine devono essere installati e registrati gli agenti e i proxy del controller di dominio?**

Sono supportati gli ordini di installazione dell'agente proxy, l'installazione dell'agente DC, la registrazione della foresta e la registrazione del proxy.

**D: è opportuno preoccuparsi delle prestazioni dei controller di dominio per la distribuzione di questa funzionalità?**

Il servizio agente del controller di dominio di Password di protezione di Azure AD non dovrebbe incidere in modo significativo sulle prestazioni dei controller di dominio in una distribuzione di Active Directory esistente e integra.

Per la maggior parte delle distribuzioni di Active Directory attive, le operazioni di modifica delle password rappresentano una piccola parte del carico di lavoro complessivo in un determinato controller di dominio. Ad esempio, si supponga l'esistenza di un dominio di Active Directory con 10000 account utente e criteri MaxPasswordAge impostati su 30 giorni. In media questo dominio vedrà 10000/30=~333 operazioni di modifica delle password ogni giorno, il che rappresenta un numero trascurabile di operazioni persino per un singolo controller di dominio. Considerare un potenziale scenario di caso peggiore, ovvero supporre che queste ~333 modifiche delle password in un singolo controller di dominio siano state effettuate nel giro di un'ora. Questo scenario, ad esempio, può verificarsi quando diversi dipendenti tornano tutti al lavoro il lunedì mattina. Anche in questo caso, si verificheranno ~333/60 minuti = sei modifiche di password al minuto, il che non costituisce comunque un carico significativo.

Se però i controller di dominio correnti già operano a livelli di prestazioni limitate (ad esempio, perché sono stati superati i limiti massimi di CPU, spazio su disco, I/O su disco e così via), prima di distribuire questa funzionalità è consigliabile aggiungere altri controller di dominio o espandere lo spazio su disco disponibile. Vedere anche la domanda precedente relativa all'uso dello spazio su disco per la condivisione sysvol.

**D: si desidera testare Azure AD la protezione delle password in pochi controller di dominio nel dominio. È possibile forzare le modifiche della password utente per usare i controller di dominio specifici?**

No. È il sistema operativo client Windows a controllare quale controller di dominio viene usato quando un utente modifica la password. Il controller di dominio è selezionato in base a fattori quali Active Directory assegnazioni di siti e subnet, configurazione di rete specifica dell'ambiente e così via. Azure AD Password Protection non controlla questi fattori e non può influenzare il controller di dominio selezionato per modificare la password di un utente.

Un modo per raggiungere parzialmente questo obiettivo consiste nel distribuire Password di protezione di Azure AD in tutti i controller di dominio presenti in un determinato sito di Active Directory. Questo approccio garantirà una copertura ragionevole per i client Windows assegnati al sito e, di conseguenza, anche per gli utenti che accedono a tali client e modificano le loro password.

**D: se si installa il servizio Azure AD Password Protection Agent nel solo controller di dominio primario (PDC), saranno protetti anche tutti gli altri controller di dominio nel dominio?**

No. Quando la password di un utente viene modificata in un determinato controller di dominio non primario, la password non crittografata non viene mai inviata al controller di dominio primario (questa è un'idea errata molto diffusa). Dopo che una nuova password viene accettata in un controller di dominio, quest'ultimo usa tale password per crearne i vari hash specifici del protocollo di autenticazione e quindi rende permanenti gli hash nella directory. La password non crittografata non diventa permanente. Gli hash aggiornati vengono poi replicati nel controller di dominio primario. Le password utente possono in alcune circostanze essere modificate direttamente nel controller di dominio primario, anche in questo caso in base a vari fattori come la topologia di rete e la struttura del sito di Active Directory. Vedere la domanda precedente.

In sintesi, la distribuzione del servizio agente del controller di dominio di Password di protezione di Azure AD nel controller di dominio primario è necessaria per raggiungere una copertura di sicurezza della funzionalità pari al 100% all'interno del dominio. La distribuzione della funzionalità nel solo controller di dominio primario non offre i vantaggi di sicurezza di Password di protezione di Azure AD per gli altri controller di dominio presenti nel dominio.

**D: perché il blocco Smart personalizzato non funziona anche dopo che gli agenti sono stati installati nell'ambiente Active Directory locale?**

Il blocco Smart personalizzato è supportato solo in Azure AD. Le modifiche apportate alle impostazioni di blocco Smart personalizzato nel portale di Azure AD non hanno alcun effetto sull'ambiente Active Directory locale, anche con gli agenti installati.

**D: è disponibile un System Center Operations Manager Management Pack per la protezione Azure AD password?**

No.

**D: perché Azure AD continuano a rifiutare le password vulnerabili anche se i criteri sono stati configurati per la modalità di controllo?**

La modalità di controllo è supportata solo nell'ambiente di Active Directory locale. Azure AD è sempre in modo implicito in modalità "applica" quando valuta le password.

**D: gli utenti visualizzano il messaggio di errore di Windows tradizionale quando una password viene rifiutata da Azure AD la protezione con password. È possibile personalizzare questo messaggio di errore in modo che gli utenti conoscano effettivamente cosa è successo?**

No. Il messaggio di errore visualizzato dagli utenti quando una password viene rifiutata da un controller di dominio viene controllata dal computer client, non dal controller di dominio. Questo comportamento si verifica se una password viene rifiutata dai criteri predefiniti Active Directory password o da una soluzione basata su filtro password, ad esempio Azure AD la protezione delle password.

## <a name="password-testing-procedures"></a>Procedure di test delle password

È possibile eseguire alcuni test di base di varie password per convalidare il corretto funzionamento del software e ottenere una migliore comprensione dell' [algoritmo di valutazione delle password](concept-password-ban-bad.md#how-are-passwords-evaluated). In questa sezione viene descritto un metodo per il testing progettato per produrre risultati ripetibili.

Perché è necessario seguire questi passaggi? Esistono diversi fattori che rendono difficile eseguire test controllati e ripetibili delle password nell'ambiente Active Directory locale:

* I criteri password sono configurati e salvati in modo permanente in Azure e le copie del criterio vengono sincronizzate periodicamente dagli agenti del controller di dominio locale usando un meccanismo di polling. La latenza inerente a questo ciclo di polling può causare confusione. Se, ad esempio, si configurano i criteri in Azure, ma si dimentica di sincronizzarli con l'agente del controller di dominio, i test potrebbero non restituire i risultati previsti. L'intervallo di polling è attualmente hardcoded per essere una volta all'ora, ma l'attesa di un'ora tra le modifiche dei criteri non è ideale per uno scenario di test interattivo.
* Quando un nuovo criterio password viene sincronizzato con un controller di dominio, si verificherà una maggiore latenza durante la replica in altri controller di dominio. Questi ritardi possono provocare risultati imprevisti se si verifica una modifica della password in un controller di dominio che non ha ancora ricevuto la versione più recente del criterio.
* Il test delle modifiche delle password tramite un'interfaccia utente rende difficile la confidenza nei risultati. Ad esempio, è facile digitare erroneamente una password non valida in un'interfaccia utente, soprattutto perché la maggior parte delle interfacce utente di password nasconde l'input dell'utente, ad esempio l'interfaccia utente di Windows Ctrl-Alt-Delete-> modificare la password.
* Non è possibile controllare in modo rigoroso quale controller di dominio viene utilizzato quando si testano le modifiche delle password dai client aggiunti al dominio. Il sistema operativo client Windows seleziona un controller di dominio in base a fattori quali Active Directory assegnazioni di siti e subnet, configurazione di rete specifica dell'ambiente e così via.

Per evitare questi problemi, i passaggi seguenti sono basati sul test della riga di comando per la reimpostazione della password durante l'accesso a un controller di dominio.

> [!WARNING]
> Queste procedure devono essere utilizzate solo in un ambiente di test perché tutte le modifiche e le reimpostazioni della password in entrata verranno accettate senza convalida mentre il servizio agente controller di dominio viene arrestato e anche per evitare i maggiori rischi inerenti all'accesso a un controller di dominio.

Nei passaggi seguenti si presuppone che l'agente del controller di dominio sia stato installato in almeno un controller di dominio, che sia stato installato almeno un proxy e che sia stato registrato sia il proxy che la foresta.

1. Accedere a un controller di dominio usando le credenziali di amministratore di dominio (o altre credenziali che dispongono di privilegi sufficienti per creare account utente di test e reimpostare le password), in cui è installato il software dell'agente controller di dominio ed è stato riavviato.
1. Aprire Visualizzatore eventi e passare al [registro eventi di amministrazione dell'agente del controller](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log)di dominio.
1. Aprire una finestra del prompt dei comandi con privilegi elevati.
1. Creare un account di test per eseguire il test delle password

   Esistono diversi modi per creare un account utente, ma qui viene offerta un'opzione della riga di comando per facilitarne l'uso durante i cicli di test ripetuti:

   ```text
   net.exe user <testuseraccountname> /add <password>
   ```

   Ai fini della discussione, si supponga di aver creato un account di prova denominato "ContosoUser", ad esempio:

   ```text
   net.exe user ContosoUser /add <password>
   ```

1. Aprire un Web browser (potrebbe essere necessario usare un dispositivo separato anziché il controller di dominio), accedere al [portale di Azure](https://portal.azure.com)e passare a Azure Active Directory > sicurezza > metodi di autenticazione > la protezione con password.
1. Modificare i criteri di protezione Azure AD password in base alle esigenze per il test che si desidera eseguire.  Ad esempio, è possibile decidere di configurare la modalità applicata o di controllo oppure è possibile decidere di modificare l'elenco dei termini proibiti nell'elenco delle password escluse.
1. Sincronizzare il nuovo criterio arrestando e riavviando il servizio agente di controller di dominio.

   Questo passaggio può essere eseguito in vari modi. Un modo consiste nell'utilizzare la console di amministrazione di gestione dei servizi facendo clic con il pulsante destro del mouse sul servizio Azure AD Password Protection Agent e scegliendo "Riavvia". Un altro modo può essere eseguito dalla finestra del prompt dei comandi, come indicato di seguito:

   ```text
   net stop AzureADPasswordProtectionDCAgent && net start AzureADPasswordProtectionDCAgent
   ```
    
1. Controllare la Visualizzatore eventi per verificare che sia stato scaricato un nuovo criterio.

   Ogni volta che il servizio agente di controller di dominio viene arrestato e avviato, verranno visualizzati 2 30006 eventi rilasciati in seguito alla successione. Il primo evento 30006 rifletterà i criteri memorizzati nella cache su disco nella condivisione SYSVOL. Il secondo evento 30006, se presente, deve avere una data aggiornata dei criteri del tenant e, in caso affermativo, rifletterà i criteri scaricati da Azure. Il valore di data dei criteri tenant è attualmente codificato per visualizzare il timestamp approssimativo in cui il criterio è stato scaricato da Azure.
   
   Se il secondo evento 30006 non viene visualizzato, è necessario risolvere il problema prima di continuare.
   
   Gli eventi 30006 appariranno simili all'esempio seguente:
 
   ```text
   The service is now enforcing the following Azure password policy.

   Enabled: 1
   AuditOnly: 0
   Global policy date: ‎2018‎-‎05‎-‎15T00:00:00.000000000Z
   Tenant policy date: ‎2018‎-‎06‎-‎10T20:15:24.432457600Z
   Enforce tenant policy: 1
   ```

   Se, ad esempio, si modifica tra la modalità applicata e la modalità di controllo, il flag AuditOnly verrà modificato (il criterio precedente con AuditOnly = 0 è in modalità applicata); le modifiche apportate all'elenco delle password con Banned personalizzato non vengono riflesse direttamente nell'evento 30006 precedente (e non vengono registrate altrove per motivi di sicurezza). Il download del criterio da Azure dopo tale modifica includerà anche l'elenco delle password vietate personalizzate modificate.

1. Eseguire un test tentando di reimpostare una nuova password per l'account utente di test.

   Questo passaggio può essere eseguito dalla finestra del prompt dei comandi in questo modo:

   ```text
   net.exe user ContosoUser <password>
   ```

   Dopo aver eseguito il comando, è possibile ottenere altre informazioni sul risultato del comando cercando nel Visualizzatore eventi. Gli eventi di risultato della convalida della password sono documentati nell'argomento [registro eventi di amministrazione dell'agente controller](howto-password-ban-bad-on-premises-monitor.md#dc-agent-admin-event-log) di dominio; questi eventi verranno usati per convalidare il risultato del test oltre all'output interattivo dei comandi net.exe.

   Si proverà ad esempio: tentativo di impostare una password vietata dall'elenco globale Microsoft. si noti che l'elenco non è [documentato](concept-password-ban-bad.md#global-banned-password-list) , ma è possibile eseguire il test in un periodo di tempo vietato noto. In questo esempio si presuppone che i criteri siano stati configurati in modalità applicata e che siano stati aggiunti zero termini all'elenco delle password escluse.

   ```text
   net.exe user ContosoUser PassWord
   The password does not meet the password policy requirements. Check the minimum password length, password complexity and password history requirements.

   More help is available by typing NET HELPMSG 2245.
   ```

   In base alla documentazione, poiché il test è un'operazione di reimpostazione della password, dovrebbero essere visualizzati un evento 10017 e un evento 30005 per l'utente ContosoUser.

   L'evento 10017 dovrebbe essere simile a questo esempio:

   ```text
   The reset password for the specified user was rejected because it did not comply with the current Azure password policy. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   L'evento 30005 dovrebbe essere simile a questo esempio:

   ```text
   The reset password for the specified user was rejected because it matched at least one of the tokens present in the Microsoft global banned password list of the current Azure password policy.
 
   UserName: ContosoUser
   FullName: 
   ```

   Si tratta di un esempio divertente. Questa volta verrà eseguito un tentativo di impostare una password vietata dall'elenco di file esclusi personalizzati mentre i criteri sono in modalità di controllo. In questo esempio si presuppone che siano stati eseguiti i passaggi seguenti: configurazione dei criteri in modalità di controllo, aggiunta del termine "lacrimoso" all'elenco delle password escluse personalizzate e sincronizzazione dei nuovi criteri risultanti nel controller di dominio tramite il servizio agente controller di dominio come descritto in precedenza.

   Ok, impostare una variante della password esclusa:

   ```text
   net.exe user ContosoUser LaChRymoSE!1
   The command completed successfully.
   ```

   Tenere presente che questa volta è riuscita perché il criterio è in modalità di controllo. Verranno visualizzati un evento 10025 e un evento 30007 per l'utente ContosoUser.

   L'evento 10025 dovrebbe essere simile a questo esempio:
   
   ```text
   The reset password for the specified user would normally have been rejected because it did not comply with the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted. Please see the correlated event log message for more details.
 
   UserName: ContosoUser
   FullName: 
   ```

   L'evento 30007 dovrebbe essere simile a questo esempio:

   ```text
   The reset password for the specified user would normally have been rejected because it matches at least one of the tokens present in the per-tenant banned password list of the current Azure password policy. The current Azure password policy is configured for audit-only mode so the password was accepted.
 
   UserName: ContosoUser
   FullName: 
   ```

1. Continuare a testare le varie password scelte e controllare i risultati nel Visualizzatore eventi usando le procedure descritte nei passaggi precedenti. Se è necessario modificare i criteri nel portale di Azure, non dimenticare di sincronizzare i nuovi criteri con l'agente del controller di dominio, come descritto in precedenza.

Sono state descritte le procedure che consentono di eseguire test controllati del comportamento di convalida delle password di Azure AD password. La reimpostazione delle password utente dalla riga di comando direttamente in un controller di dominio può sembrare un mezzo strano per eseguire tali test, ma come descritto in precedenza è progettata per produrre risultati ripetibili. Quando si esegue il test di varie password, tenere presente l' [algoritmo di valutazione delle password](concept-password-ban-bad.md#how-are-passwords-evaluated) perché potrebbe essere utile per spiegare i risultati che non sono previsti.

> [!WARNING]
> Quando tutti i test vengono completati, non dimenticare di eliminare tutti gli account utente creati a scopo di test.

## <a name="additional-content"></a>Contenuto aggiuntivo

Gli articoli a cui si accede tramite i collegamenti seguenti non fanno parte della documentazione di base relativa a Password di protezione di Azure AD, ma possono essere una fonte utile di informazioni aggiuntive su tale funzionalità.

[Azure AD la protezione con password è ora disponibile a livello generale.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-is-now-generally-available/ba-p/377487)

[Guida alla protezione da phishing tramite posta elettronica-parte 15: implementare il servizio Microsoft Azure AD Password Protection (anche per locale).](http://kmartins.com/2018/10/14/email-phishing-protection-guide-part-15-implement-the-microsoft-azure-ad-password-protection-service-for-on-premises-too/)

[Azure AD Password Protection and Smart Lockout are now in Public Preview!](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-Password-Protection-and-Smart-Lockout-are-now-in-Public/ba-p/245423#M529) (Password di protezione di Azure AD e il blocco intelligente sono ora disponibili in anteprima pubblica)

## <a name="microsoft-premierunified-support-training-available"></a>Disponibilità di training in caso contratti di supporto Microsoft Premier o Unified

Se è interessati ad approfondire la conoscenza di Password di protezione di Azure AD e la distribuzione di tale funzionalità nell'ambiente in uso, è possibile sfruttare i vantaggi di un servizio proattivo Microsoft disponibile per i clienti con un contratto di supporto Premier o Unified. Il servizio viene chiamato Azure Active Directory: Password Protection. Per altre informazioni, contattare il technical account manager.

## <a name="next-steps"></a>Passaggi successivi

In caso di dubbi su Password di protezione di Azure AD locale a cui questo articolo non abbia dato risposta, inviare il proprio feedback tramite la sezione riservata ai commenti di seguito.

[Distribuire la protezione delle password di Azure AD](howto-password-ban-bad-on-premises-deploy.md)