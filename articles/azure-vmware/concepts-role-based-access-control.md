---
title: Concetti-controllo degli accessi in base al ruolo vSphere (vSphere RBAC)
description: Informazioni sulle funzionalità principali del controllo degli accessi in base al ruolo di vSphere per la soluzione VMware di Azure
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: c2d27531f7a0acd36b4047e98aac994668f64a09
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586167"
---
# <a name="vsphere-role-based-access-control-vsphere-rbac-for-azure-vmware-solution"></a>controllo degli accessi in base al ruolo vSphere (vSphere RBAC) per la soluzione VMware di Azure

Nella soluzione VMware di Azure, vCenter dispone di un utente locale predefinito denominato cloudadmin e assegnato al ruolo CloudAdmin predefinito. L'utente cloudadmin locale viene usato per configurare gli utenti in Active Directory. In generale, il ruolo CloudAdmin crea e gestisce i carichi di lavoro nel cloud privato. Nella soluzione VMware di Azure il ruolo CloudAdmin dispone di privilegi vCenter diversi dalle altre soluzioni cloud VMware.     

> [!NOTE]
> La soluzione VMware di Azure offre ruoli personalizzati in vCenter che non sono disponibili nel portale della soluzione VMware di Azure. Per altre informazioni, vedere la sezione [creare ruoli personalizzati in vCenter](#create-custom-roles-on-vcenter) più avanti in questo articolo. 

In una distribuzione locale di vCenter e ESXi, l'amministratore ha accesso all' administrator@vsphere.local account vCenter. Possono anche avere più utenti/gruppi di Active Directory (AD) assegnati. 

In una distribuzione della soluzione VMware di Azure, l'amministratore non ha accesso all'account utente amministratore. Ma possono assegnare utenti e gruppi di Active Directory al ruolo CloudAdmin in vCenter.  

L'utente del cloud privato non ha accesso e non è in grado di configurare componenti di gestione specifici supportati e gestiti da Microsoft. Ad esempio cluster, host, archivi dati e commutatori virtuali distribuiti.

## <a name="azure-vmware-solution-cloudadmin-role-on-vcenter"></a>Ruolo CloudAdmin della soluzione VMware di Azure in vCenter

È possibile visualizzare i privilegi concessi al ruolo CloudAdmin della soluzione VMware di Azure nella soluzione VMware di Azure private cloud vCenter.

1. Accedere a vCenter e andare a **menu**  >  **Administration**.
1. In **controllo di accesso** selezionare **ruoli**.
1. Dall'elenco dei ruoli selezionare **CloudAdmin** e quindi selezionare **Privileges (privilegi**). 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="Come visualizzare i privilegi del ruolo CloudAdmin in vSphere client":::

Il ruolo CloudAdmin nella soluzione VMware di Azure presenta i privilegi seguenti in vCenter. Per una spiegazione dettagliata di ogni privilegio, fare riferimento alla [documentazione del prodotto VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html) .

| Privilege | Descrizione |
| --------- | ----------- |
| **Avvisi** | Conferma avviso<br />Crea allarme<br />Disabilita azione allarme<br />Modifica allarme<br />Rimuovi allarme<br />Imposta stato allarme |
| **Autorizzazioni** | Modificare le autorizzazioni<br />Modifica ruolo |
| **Raccolta contenuto** | Aggiungi elemento di libreria<br />Creare una sottoscrizione per una libreria pubblicata<br />Crea libreria locale<br />Crea libreria sottoscritta<br />Elimina elemento libreria<br />Elimina libreria locale<br />Elimina libreria sottoscritta<br />Elimina sottoscrizione di una libreria pubblicata<br />Scaricare i file<br />Rimuovi elementi della libreria<br />Rimuovi libreria sottoscritta<br />Importa archiviazione<br />Informazioni sulla sottoscrizione Probe<br />Pubblicare un elemento della libreria nei Sottoscrittori<br />Pubblicare una libreria nei propri Sottoscrittori<br />Lettura archiviazione<br />Elemento della libreria di sincronizzazione<br />Libreria sottoscritta Sync<br />Introspezione del tipo<br />Aggiorna impostazioni di configurazione<br />Aggiornare i file<br />Aggiorna libreria<br />Aggiorna elemento libreria<br />Aggiorna libreria locale<br />Aggiorna libreria sottoscritta<br />Aggiornare la sottoscrizione di una libreria pubblicata<br />Visualizza impostazioni di configurazione |
| **Operazioni di crittografia** | Accesso diretto |
| **Datastore** | Alloca spazio<br />Browse Datastore (Sfoglia archivio dati)<br />Configura archivio dati<br />Operazioni sui file di basso livello<br />Rimuovi file<br />Aggiornare i metadati della macchina virtuale |
| **Cartella** | Creazione cartella<br />Elimina cartella<br />Sposta cartella<br />Rinomina cartella |
| **Global** | Annulla attività<br />Tag globale<br />Salute<br />Evento log<br />Gestire attributi personalizzati<br />Service Manager<br />Imposta attributo personalizzato<br />Tag di sistema |
| **Host** | Replica vSphere<br />&#160;&#160;&#160;&#160;gestire la replica |
| **codifica vSphere** | Assegnare e annullare l'assegnazione del tag vSphere<br />Crea tag vSphere<br />Crea categoria di tag vSphere<br />Elimina tag vSphere<br />Elimina categoria di tag vSphere<br />Modifica tag vSphere<br />Modifica categoria di tag vSphere<br />Modificare il campo UsedBy per Category<br />Modificare il campo UsedBy per il tag |
| **Network** | Assign network |
| **Risorsa** | Applica suggerimento<br />Assegnare vApp al pool di risorse<br />Assign virtual machine to resource pool<br />Crea pool di risorse<br />Esegui la migrazione della macchina virtuale spenta<br />Esegui la migrazione della macchina virtuale accesa<br />Modificare il pool di risorse<br />Spostare il pool di risorse<br />Query vMotion<br />Rimuovi pool di risorse<br />Rinominare il pool di risorse |
| **Attività pianificata** | Creare un'attività<br />Modifica attività<br />Rimuovi attività<br />Esegui attività |
| **Sessioni** | Message<br />Convalida sessione |
| **Profilo** | Visualizzazione archiviazione basata su profili |
| **Visualizzazione archiviazione** | Visualizzazione |
| **vApp** | Aggiungi macchina virtuale<br />Assegnare il pool di risorse<br />Assegna vApp<br />Clone<br />Create<br />Elimina<br />Esportazione<br />Importa<br />Sposta<br />Spegnimento<br />Accendere<br />Rinominare<br />Sospendi<br />Unregister <br />Visualizza ambiente OVF<br />configurazione dell'applicazione vApp<br />configurazione dell'istanza di vApp<br />configurazione di vApp managedBy<br />configurazione della risorsa vApp |
| **Macchina virtuale** | Modifica configurazione<br />&#160;&#160;&#160;&#160;acquisire il lease del disco<br />&#160;&#160;&#160;&#160;aggiungere un disco esistente<br />&#160;&#160;&#160;&#160;Aggiungi nuovo disco<br />&#160;&#160;&#160;&#160;aggiungere o rimuovere un dispositivo<br />&#160;&#160;&#160;&#160;configurazione avanzata<br />&#160;&#160;&#160;&#160;modificare il numero di CPU<br />&#160;&#160;&#160;&#160;modificare la memoria<br />Impostazioni di modifica &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;modificare la selezione host file<br />&#160;&#160;&#160;&#160;modificare la risorsa<br />&#160;&#160;&#160;&#160;configurare il dispositivo USB host<br />&#160;&#160;&#160;&#160;configurare il dispositivo RAW<br />&#160;&#160;&#160;&#160;configurare managedBy<br />&#160;&#160;&#160;&#160;visualizzare le impostazioni di connessione<br />&#160;&#160;&#160;&#160;estendere il disco virtuale<br />&#160;&#160;&#160;&#160;modificare le impostazioni del dispositivo<br />Compatibilità della tolleranza di errore &#160;&#160;&#160;&#160;query<br />&#160;&#160;&#160;&#160;i file senza proprietà<br />&#160;&#160;&#160;&#160;ricaricare da percorsi<br />&#160;&#160;&#160;&#160;rimuovere il disco<br />Ridenominazione &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;reimpostare le informazioni Guest<br />Annotazione set di &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;/Nascondi rilevamento modifiche disco<br />Elemento padre del fork di attivazione &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;aggiornare la compatibilità delle macchine virtuali<br />Modifica inventario<br />&#160;&#160;&#160;&#160;crea da esistente<br />&#160;&#160;&#160;&#160;Crea nuovo<br />Spostamento &#160;&#160;&#160;&#160;<br />Registro &#160;&#160;&#160;&#160;<br />Rimozione &#160;&#160;&#160;&#160;<br />Annullamento della registrazione di &#160;&#160;&#160;&#160;<br />Operazioni Guest<br />&#160;&#160;&#160;&#160;la modifica dell'alias dell'operazione Guest<br />&#160;&#160;&#160;&#160;query alias operazione Guest<br />&#160;&#160;&#160;&#160;modifiche delle operazioni Guest<br />Esecuzione del programma &#160;&#160;&#160;&#160;operazione Guest<br />&#160;&#160;&#160;&#160;le query sull'operazione Guest<br />Interazione<br />Domanda di risposta &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;l'operazione di backup nella macchina virtuale<br />&#160;&#160;&#160;&#160;configurare supporti CD<br />&#160;&#160;&#160;&#160;configurare supporti floppy<br />&#160;&#160;&#160;&#160;connettere i dispositivi<br />Interazione &#160;&#160;&#160;&#160;console<br />Schermata di creazione &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;deframmenta tutti i dischi<br />Trascinamento della selezione &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;la gestione del sistema operativo guest per l'API VIX<br />&#160;&#160;&#160;&#160;inserire codici di analisi HID USB<br />&#160;&#160;&#160;&#160;installare gli strumenti VMware<br />Sospendere o sospendere &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;operazioni di cancellazione o compattazione<br />Spegnimento &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;accendere<br />&#160;&#160;&#160;&#160;sessione di registrazione nella macchina virtuale<br />&#160;&#160;&#160;&#160;sessione di riproduzione nella macchina virtuale<br />Sospensione &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;Sospendi tolleranza di errore<br />Failover di test di &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;riavviare il test della macchina virtuale secondaria<br />&#160;&#160;&#160;&#160;disattivare la tolleranza di errore<br />&#160;&#160;&#160;&#160;attivare la tolleranza di errore<br />Provisioning<br />&#160;&#160;&#160;&#160;consentire l'accesso al disco<br />&#160;&#160;&#160;&#160;consentire l'accesso ai file<br />&#160;&#160;&#160;&#160;consentire l'accesso in sola lettura al disco<br />&#160;&#160;&#160;&#160;consentire il download della macchina virtuale<br />Modello di clonazione &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;clonare la macchina virtuale<br />&#160;&#160;&#160;&#160;creare un modello dalla macchina virtuale<br />&#160;&#160;&#160;&#160;personalizzare Guest<br />&#160;&#160;&#160;&#160;distribuire il modello<br />Contrassegno &#160;&#160;&#160;&#160;come modello<br />&#160;&#160;&#160;&#160;modificare la specifica della personalizzazione<br />Innalzamento di livello &#160;&#160;&#160;&#160;dischi<br />&#160;&#160;&#160;&#160;leggere le specifiche di personalizzazione<br />Configurazione del servizio<br />&#160;&#160;&#160;&#160;consentire le notifiche<br />&#160;&#160;&#160;&#160;consentire il polling delle notifiche degli eventi globali<br />&#160;&#160;&#160;&#160;gestire la configurazione del servizio<br />&#160;&#160;&#160;&#160;modificare la configurazione del servizio<br />Configurazioni del servizio query &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;la configurazione del servizio di lettura<br />Gestione snapshot<br />Creazione Snapshot &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;rimuovere lo snapshot<br />&#160;&#160;&#160;&#160;Rinomina snapshot<br />&#160;&#160;&#160;&#160;ripristinare lo snapshot<br />Replica vSphere<br />&#160;&#160;&#160;&#160;configurare la replica<br />&#160;&#160;&#160;&#160;gestire la replica<br />Monitoraggio della replica &#160;&#160;&#160;&#160; |
| **vService** | Crea dipendenza<br />Elimina dipendenza<br />Riconfigurare la configurazione della dipendenza<br />Aggiorna dipendenza |

## <a name="create-custom-roles-on-vcenter"></a>Creare ruoli personalizzati in vCenter

La soluzione VMware di Azure supporta l'uso di ruoli personalizzati con privilegi uguali o inferiori rispetto al ruolo CloudAdmin. 

Il ruolo CloudAdmin può creare, modificare o eliminare ruoli personalizzati con privilegi inferiori o uguali al ruolo corrente. Potrebbe essere possibile creare ruoli con privilegi maggiori di CloudAdmin, ma non sarà possibile assegnare il ruolo a tutti gli utenti o gruppi o eliminare il ruolo.

Per evitare la creazione di ruoli che non possono essere assegnati o eliminati, è consigliabile clonare il ruolo CloudAdmin come base per la creazione di nuovi ruoli personalizzati.

### <a name="create-a-custom-role"></a>Creare un ruolo personalizzato
1. Accedere a vCenter con cloudadmin \@ vSphere. local o un utente con il ruolo cloudadmin.
2. Passare alla sezione configurazione **ruoli** e selezionare **menu**  >  **Amministrazione**  >  **accessi**  >  **ruoli** di controllo.
3. Selezionare il ruolo **CloudAdmin** e selezionare l'icona di **azione clone Role** .

   > [!NOTE] 
   > Non clonare il ruolo **amministratore** . Questo ruolo non può essere usato e non è possibile eliminare il ruolo personalizzato creato da cloudadmin \@ vSphere. local.

4. Specificare il nome desiderato per il ruolo clonato.
5. Aggiungere o rimuovere privilegi per il ruolo e selezionare **OK**. Il ruolo clonato dovrebbe essere ora visibile nell'elenco **ruoli** .


### <a name="use-a-custom-role"></a>Usare un ruolo personalizzato

1. Passare all'oggetto che richiede l'autorizzazione aggiunta. Ad esempio, per applicare l'autorizzazione a una cartella, passare a **menu**  >  **VM e modelli**  >  **cartella nome**
1. Fare clic con il pulsante destro del mouse sull'oggetto e scegliere **Aggiungi autorizzazione**.
1. Nella finestra **Aggiungi autorizzazione** selezionare l'origine identità nell'elenco a discesa **utente** in cui è possibile trovare il gruppo o l'utente.
1. Cercare l'utente o il gruppo dopo aver selezionato l'origine identità nella sezione **utente** . 
1. Selezionare il ruolo che verrà applicato per l'utente o il gruppo.
1. Se necessario, selezionare **propaga a elementi figlio** , quindi fare clic su **OK**.
   L'autorizzazione aggiunta viene visualizzata nella sezione **autorizzazioni** per l'oggetto.

## <a name="next-steps"></a>Passaggi successivi

Ora che sono state illustrate le nozioni di base del controllo degli accessi in base al ruolo di vSphere per la soluzione VMware di Azure, è possibile ottenere informazioni su:

- I dettagli di ciascun privilegio nella [documentazione del prodotto VMware](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).
- [Modalità di monitoraggio e ripristino di cloud privati da una soluzione VMware di Azure](concepts-monitor-repair-private-cloud.md).
- [Come abilitare la risorsa della soluzione VMware di Azure](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

