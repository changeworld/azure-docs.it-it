---
title: Connettere alsid per Active Directory ad Azure Sentinel | Microsoft Docs
description: Informazioni su come usare alsid per Active Directory Connector per eseguire il pull dei log alsid in Sentinel di Azure. Visualizzare i dati alsid nelle cartelle di lavoro, creare avvisi e migliorare l'analisi.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566794"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>Connettere alsid per Active Directory (AD) ad Azure Sentinel

> [!IMPORTANT]
> Alsid per Active Directory Connector è attualmente in fase di **Anteprima**. Vedere le [condizioni per l'utilizzo supplementari per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) per le note legali aggiuntive che si applicano alle funzionalità di Azure disponibili in versione beta, in anteprima o non ancora rilasciate a livello generale.

Questo articolo illustra come connettere la soluzione alsid per Active Directory ad Azure Sentinel. Il connettore alsid per Active Directory Data Connector consente di connettere facilmente il alsid per i log AD con Azure Sentinel, in modo che sia possibile visualizzare i dati nelle cartelle di lavoro, eseguirvi query per creare avvisi personalizzati e incorporarli per migliorare l'analisi. L'integrazione tra alsid per AD e Azure Sentinel usa un server syslog con l'agente di Log Analytics installato. USA anche un parser di log personalizzato basato su una funzione kusto.

> [!NOTE]
> I dati verranno archiviati nella posizione geografica dell'area di lavoro in cui viene eseguito Azure Sentinel.

## <a name="prerequisites"></a>Prerequisiti

- È necessario disporre dell'autorizzazione di scrittura per l'area di lavoro di Azure Sentinel.

- La soluzione alsid per Active Directory deve essere configurata per esportare i log tramite syslog.

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>Inviare alsid per i log di Active Directory ad Azure Sentinel tramite l'agente syslog

Configurare alsid per AD per l'invio dei messaggi syslog all'area di lavoro di Azure Sentinel tramite l'agente syslog.

1. Nel menu di navigazione di Azure Sentinel selezionare **connettori dati**.

1. Dalla raccolta di **connettori dati** selezionare il connettore **alsid per Active Directory (anteprima)** , quindi aprire la **pagina del connettore**.

1. Seguire le istruzioni riportate nella pagina **alsid for Active Directory** Connector:

    1. Configurare un server syslog

        1. Se non ne è già presente uno, creare un server syslog Linux per alsid per l'invio dei log a da parte di Active Directory. Azure Sentinel supporta i daemon **rsyslog** e **syslog-ng** . 

        1. Configurare il server syslog per l'output di alsid per i log di Active Directory in un file separato.

    1. Configurare alsid per AD per inviare i log al server syslog

        1. Nel portale di **alsid per Active Directory** passare a *sistema*, *configurazione* e quindi *syslog*. Da qui è possibile creare un nuovo avviso syslog per il server syslog. Per il server remoto, usare l'indirizzo IP del computer Linux in cui è stato installato l'agente Linux.

        1. Verificare che i log vengano raccolti correttamente nel server in un file separato. a tale scopo, è possibile usare il pulsante **test Configurazione** nella configurazione degli avvisi *syslog* in alsid per Active Directory.

    1. Installare e caricare l'agente di Log Analytics per Linux

        - Scegliere una VM Linux di Azure o un computer Linux non Azure (fisico o virtuale). Seguire i collegamenti e le istruzioni sullo schermo. Per ulteriori informazioni, vedere [configurare il computer o l'appliance Linux](connect-syslog.md#configure-your-linux-machine-or-appliance) .

    1. Configurare i log che devono essere raccolti dall'agente di Log Analytics

        - Selezionare le funzionalità e i livelli di gravità nella configurazione impostazioni avanzate dell'area di lavoro.

            1. Fare clic sul collegamento **aprire la configurazione impostazioni avanzate dell'area di lavoro >** nella pagina connettore.

            1. Nella schermata **Impostazioni avanzate** selezionare **dati** e quindi **log personalizzati**.

            1. Contrassegnare la casella **di controllo applica la configurazione seguente alle macchine virtuali Linux** e fare clic su **Aggiungi**.

            1. Fare clic su **Choose file (Scegli file** ) per caricare un file alsid di esempio per il file syslog del computer Linux che esegue il server syslog e fare clic su **Next**.

            1. Verificare che **imposta delimitatore di record** sia impostato su **nuova riga** e fare clic su **Avanti**.

            1. Selezionare **Linux** e immettere il percorso del file syslog, quindi fare clic su **+** Next ( **Avanti**).

            1. Nel campo nome digitare *AlsidForADLog* prima del suffisso _CL, quindi fare clic su **fine**.
    
Potrebbero essere necessari fino a 20 minuti prima che i log inizino a essere visualizzati in Log Analytics.

## <a name="find-your-data"></a>Trovare i dati

Una volta stabilita la connessione, i dati vengono visualizzati nei **log**, in **log personalizzati** nella tabella *AlsidForADLog_CL* .

Questo connettore dati dipende da un parser basato su una funzione kusto per funzionare come previsto. Usare la procedura seguente per configurare la funzione **afad_parser** kusto da usare nelle query e nelle cartelle di lavoro di.

1. Nel menu di navigazione di Azure Sentinel selezionare **logs**.

1. Copiare la query seguente e incollarla nella finestra query.
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. Fare clic sull'elenco a discesa **Salva** , quindi fare clic su **Salva**. Nel pannello **Salva** ,

    1. In **nome** immettere **afad_parser**.

    1. In **Salva con nome** scegliere **funzione**.

    1. In **alias funzione** immettere **afad_parser**.

    1. In **Category** immettere **Functions**.

    1. Fare clic su **Salva**.

    Per l'attivazione delle app per le funzioni sono in genere necessari da 10 a 15 minuti.

A questo punto si è pronti per eseguire una query su alsid per i dati di Active Directory, immettendo `afad_parser` nella prima riga della finestra query.

Per altri esempi di query, vedere la scheda **passaggi successivi** nella pagina del connettore.

## <a name="next-steps"></a>Passaggi successivi

In questo documento si è appreso come connettere alsid per ad ad Azure Sentinel. Per altre informazioni su Azure Sentinel, vedere gli articoli seguenti:

- Informazioni su come [ottenere visibilità sui dati e sulle potenziali minacce](quickstart-get-visibility.md).
- Iniziare a [rilevare minacce con Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Usare le cartelle di lavoro](tutorial-monitor-your-data.md) per monitorare i dati.
