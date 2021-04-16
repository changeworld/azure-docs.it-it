---
title: Azure AD Connect risoluzione dei problemi di sincronizzazione cloud
description: Questo articolo descrive come risolvere i problemi che potrebbero verificarsi con l'agente di provisioning cloud.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 01/19/2021
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 65022d98c7ee7e90d8f1fe5b6854605c841ad05b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530310"
---
# <a name="cloud-sync-troubleshooting"></a>Risoluzione dei problemi di sincronizzazione cloud

La sincronizzazione cloud riguarda numerosi fattori e ha varie dipendenze diverse. L'ampiezza di questo ambito può comportare diversi problemi. Questo articolo consente di risolvere questi problemi. Illustra le aree più comuni su cui concentrarsi, come raccogliere informazioni aggiuntive e le diverse tecniche che si possono usare per tenere traccia dei problemi.


## <a name="common-troubleshooting-areas"></a>Aree comuni per la risoluzione dei problemi

|Nome|Descrizione|
|-----|-----|
|[Problemi dell'agente](#agent-problems)|Verificare che l'agente sia stato installato correttamente e che comunichi con Azure Active Directory (Azure AD).|
|[Problemi di sincronizzazione degli oggetti](#object-synchronization-problems)|Usare i log di provisioning per risolvere i problemi di sincronizzazione degli oggetti.|
|[Problemi di provisioning in quarantena](#provisioning-quarantined-problems)|Informazioni su come risolvere i problemi di quarantena del provisioning.|


## <a name="agent-problems"></a>Problemi dell'agente
Di seguito sono riportati alcuni dei primi elementi da verificare con l'agente:

-  È installato?
-  L'agente è in esecuzione in locale?
-  L'agente è nel portale?
-  L'agente è contrassegnato come integro?

Questi elementi possono essere verificati nel portale di Azure e nel server locale che esegue l'agente.

### <a name="azure-portal-agent-verification"></a>Verifica dell'agente nel portale di Azure

Per verificare che l'agente sia visualizzato da Azure ed è integro, seguire questa procedura.

1. Accedere al portale di Azure.
1. Sulla sinistra, selezionare **Azure Active Directory** > **Azure AD Connect**. Al centro selezionare Gestisci **sincronizzazione**.
1. Nella schermata **Azure AD Connect di sincronizzazione cloud** selezionare Verifica tutti gli **agenti**.

   ![Esaminare tutti gli agenti](media/how-to-install/install-7.png)</br>
 
1. Nella **schermata Agenti di provisioning locali** vengono visualizzati gli agenti installati. Verificare che l'agente in questione sia presente e che sia contrassegnato come *Integro.*

   ![Schermata Agenti di provisioning locali](media/how-to-install/install-8.png)</br>

### <a name="verify-the-port"></a>Verificare la porta

Verificare che Azure sia in ascolto sulla porta 443 e che l'agente possa comunicare con essa. 

Questo test verifica che gli agenti possano comunicare con Azure sulla porta 443. Aprire un browser e passare all'URL precedente dal server in cui è installato l'agente.

![Verifica della raggiungibilità delle porte](media/how-to-install/verify-2.png)

### <a name="on-the-local-server"></a>Nel server locale

Per verificare se l'agente è in esecuzione, seguire questa procedura.

1. Nel server in cui è installato l'agente aprire **Servizi** passando a esso o selezionando **Avvia**  >  **Esecuzione**  >  **servizi.msc.**
1. In **Servizi** assicurarsi che siano presenti i servizi **Microsoft Azure AD Connect Agent Updater** e **Microsoft Azure AD Connect Provisioning Agent** e che il relativo stato sia *In esecuzione*.

   ![Schermata Servizi](media/how-to-troubleshoot/troubleshoot-1.png)

### <a name="common-agent-installation-problems"></a>Problemi comuni di installazione dell'agente

Le sezioni seguenti descrivono alcuni problemi comuni di installazione dell'agente e le soluzioni tipiche.

#### <a name="agent-failed-to-start"></a>Impossibile avviare l'agente

È possibile che venga visualizzato un messaggio di errore che indica:

**Impossibile avviare Microsoft Azure AD'agente di provisioning connect. Verificare di disporre di privilegi sufficienti per avviare i servizi di sistema.** 

Questo problema è in genere causato da criteri di gruppo che impediscono l'applicazione delle autorizzazioni all'account di accesso nt service locale creato dal programma di installazione (NT SERVICE\AADConnectProvisioningAgent). Queste autorizzazioni sono necessarie per avviare il servizio.

Per risolvere il problema, seguire questa procedura.

1. Accedere al server con un account amministratore.
1. Aprire **Servizi** passando all'opzione relativa oppure selezionando **Start** > **Esegui** > **Services.msc**.
1. In **Servizi fare** doppio clic su Microsoft Azure AD Connect Provisioning **Agent**.
1. Nella scheda **Accesso modificare** Questo **account** in amministratore di dominio. Riavviare quindi il servizio. 

   ![Scheda Accesso](media/how-to-troubleshoot/troubleshoot-3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Timeout dell'agente o certificato non valido

Quando si tenta di registrare l'agente, è possibile che venga visualizzato il messaggio di errore seguente.

![Messaggio di errore di timeout](media/how-to-troubleshoot/troubleshoot-4.png)

Questo problema in genere è dovuto al fatto che l'agente non riesce a connettersi al servizio di gestione delle identità ibride e richiede la configurazione di un proxy HTTP. Per risolvere il problema, configurare un proxy in uscita. 

L'agente di provisioning supporta l'uso di un proxy in uscita. È possibile configurarlo modificando il file di configurazione dell'agente *C:\Programmi\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*. Aggiungere al file le righe seguenti, verso la fine del file subito prima del tag di `</configuration>` chiusura.
Sostituire le variabili `[proxy-server]` e con il nome del server proxy e i valori delle `[proxy-port]` porte.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>La registrazione dell'agente ha esito negativo con errore di sicurezza

Quando si installa l'agente di provisioning cloud, è possibile che venga visualizzato un messaggio di errore.

Questo problema è in genere causato dall'impossibilità dell'agente di eseguire gli script di registrazione di PowerShell a causa dei criteri di esecuzione di PowerShell locali.

Per risolvere questo problema, modificare i criteri di esecuzione di PowerShell nel server. È necessario impostare Criteri computer e utente *come Non definito* o *RemoteSigned.* Se sono impostate come *Senza restrizioni,* verrà visualizzato questo errore. Per altre informazioni, vedere Criteri [di esecuzione di PowerShell.](/powershell/module/microsoft.powershell.core/about/about_execution_policies) 

### <a name="log-files"></a>File di registro

Per impostazione predefinita, l'agente genera un numero ridotto di messaggi di errore e informazioni minime di analisi dello stack. Questi log di traccia sono presenti nella cartella **C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace**.

Per raccogliere dettagli aggiuntivi per la risoluzione dei problemi relativi agli agenti, seguire questa procedura.

1.  Installare il modulo PowerShell AADCloudSyncTools come descritto [qui.](reference-powershell.md#install-the-aadcloudsynctools-powershell-module)
2. Usare il `Export-AADCloudSyncToolsLogs` cmdlet di PowerShell per acquisire le informazioni.  È possibile usare le opzioni seguenti per ottimizzare la raccolta dei dati.
      - SkipVerboseTrace per esportare solo i log correnti senza acquisire log dettagliati (impostazione predefinita = false)
      - TracingDurationMins per specificare una durata di acquisizione diversa (impostazione predefinita = 3 minuti)
      - OutputPath per specificare un percorso di output diverso (impostazione predefinita = Documenti dell'utente)


## <a name="object-synchronization-problems"></a>Problemi di sincronizzazione degli oggetti

La sezione seguente contiene informazioni sulla risoluzione dei problemi di sincronizzazione degli oggetti.

### <a name="provisioning-logs"></a>Log di provisioning

Nell'portale di Azure, i log di provisioning possono essere usati per individuare e risolvere i problemi di sincronizzazione degli oggetti. Per visualizzare i log, selezionare **Log**.

![Pulsante Log](media/how-to-troubleshoot/log-1.png)

I log di provisioning forniscono numerose informazioni sullo stato degli oggetti da sincronizzare tra l'ambiente Active Directory locale e Azure.

![Schermata Log di provisioning](media/how-to-troubleshoot/log-2.png)

È possibile usare le caselle a discesa nella parte superiore della pagina per filtrare la visualizzazione in base a zero in in base a problemi specifici, ad esempio le date. Fare doppio clic su un singolo evento per visualizzare informazioni aggiuntive.

![Informazioni sulla casella di riepilogo a discesa Log di provisioning](media/how-to-troubleshoot/log-3.png)

Queste informazioni forniscono i passaggi dettagliati e la posizione in cui si verifica il problema di sincronizzazione. In questo modo, è possibile individuare il punto esatto del problema.


## <a name="provisioning-quarantined-problems"></a>Problemi di provisioning in quarantena

La sincronizzazione cloud monitora l'integrità della configurazione e posiziona gli oggetti non integri in uno stato di quarantena. Se la maggior parte o tutte le chiamate effettuate sul sistema di destinazione hanno costantemente esito negativo a causa di un errore, ad esempio credenziali di amministratore non valide, il processo di sincronizzazione viene contrassegnato come in quarantena.

![Stato di quarantena](media/how-to-troubleshoot/quarantine-1.png)

Selezionando lo stato, è possibile visualizzare informazioni aggiuntive sulla quarantena. È anche possibile ottenere il codice di errore e il messaggio.

![Screenshot che mostra informazioni aggiuntive sulla quarantena.](media/how-to-troubleshoot/quarantine-2.png)

Facendo clic con il pulsante destro del mouse sullo stato verranno visualizzare opzioni aggiuntive:
    
   - visualizzare i log di provisioning
   - visualizzare l'agente
   - cancellare la quarantena

![Screenshot che mostra le opzioni del menu di scelta rapida.](media/how-to-troubleshoot/quarantine-4.png)


### <a name="resolve-a-quarantine"></a>Risolvere una quarantena
Esistono due modi diversi per risolvere una quarantena.  ovvero:

  - clear quarantine : cancella il limite ed esegue una sincronizzazione differenziale
  - riavviare il processo di provisioning: cancella il limite ed esegue una sincronizzazione iniziale

#### <a name="clear-quarantine"></a>Cancellare la quarantena
Per cancellare il limite ed eseguire una sincronizzazione differenziale nel processo di provisioning dopo la verifica, è sufficiente fare clic con il pulsante destro del mouse sullo stato e scegliere **Cancella quarantena.**

Verrà visualizzato un avviso che indica che la quarantena è in stato di cancellazione.

![Screenshot che mostra l'avviso che la quarantena è in stato di cancellazione.](media/how-to-troubleshoot/quarantine-5.png)

Lo stato dell'agente dovrebbe quindi essere integro.

![Informazioni sullo stato della quarantena](media/how-to-troubleshoot/quarantine-6.png)

#### <a name="restart-the-provisioning-job"></a>Riavviare il processo di provisioning
Usare il portale di Azure per riavviare il processo di provisioning. Nella pagina di configurazione dell'agente selezionare **Riavvia provisioning**.

  ![Riavviare il provisioning](media/how-to-troubleshoot/quarantine-3.png)

- Usare Microsoft Graph per [riavviare il processo di provisioning.](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true) Si ha il controllo completo su ciò che si riavvia. È possibile scegliere di cancellare:
  - Escrows, per riavviare il contatore di deposito accumulato verso lo stato di quarantena.
  - Quarantena, per rimuovere l'applicazione dalla quarantena.
  - Filigrane. 
  
  Usare la richiesta seguente:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="repairing-the-the-cloud-sync-service-account"></a>Ripristino dell'account del servizio di sincronizzazione cloud
Se è necessario ripristinare l'account del servizio di sincronizzazione cloud, è possibile usare `Repair-AADCloudSyncToolsAccount` .  


   1.  Usare i passaggi di installazione [descritti qui](reference-powershell.md#install-the-aadcloudsynctools-powershell-module) per iniziare e quindi continuare con i passaggi rimanenti.
   2.  Da una Windows PowerShell sessione con privilegi amministrativi, digitare o copiare e incollare quanto segue: 
    ```
    Connect-AADCloudSyncTools
    ```  
   3. Immettere le credenziali Azure AD amministratore globale
   4. Digitare o copiare e incollare quanto segue: 
    ```
    Repair-AADCloudSyncToolsAccount
    ```  
   5. Al termine, si dovrebbe dire che l'account è stato ripristinato correttamente.

## <a name="next-steps"></a>Passaggi successivi 

- [Limitazioni note](how-to-prerequisites.md#known-limitations)
- [Codici di errore](reference-error-codes.md)
