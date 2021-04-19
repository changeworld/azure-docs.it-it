---
title: Abilitare il ripristino di emergenza da zona a zona per macchine virtuali di Azure
description: Questo articolo descrive quando e come usare il ripristino di emergenza da zona a zona per le macchine virtuali di Azure.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: article
ms.date: 04/28/2020
ms.author: sideeksh
ms.openlocfilehash: 786d877328b1ab3d0f03a75604b7345dba14aa9d
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713409"
---
# <a name="enable-azure-vm-disaster-recovery-between-availability-zones"></a>Abilitare il ripristino di emergenza di macchine virtuali di Azure tra zone di disponibilità

Questo articolo descrive come replicare, eseguire il failover e il failback delle macchine virtuali di Azure da una zona di disponibilità a un'altra, all'interno della stessa area di Azure.

>[!NOTE]
>
>- Il supporto per il ripristino di emergenza da zona a zona è attualmente limitato alle aree seguenti: Asia sud-orientale, Giappone orientale, Australia orientale, JIO India occidentale, Regno Unito meridionale, Europa occidentale, Europa settentrionale, Stati Uniti centrali, Stati Uniti orientali, Stati Uniti orientali 2 e Stati Uniti occidentali 2.  
>- Site Recovery non sposta o archivia i dati dei clienti dall'area in cui vengono distribuiti quando il cliente usa il ripristino di emergenza da zona a zona. I clienti possono selezionare un insieme di credenziali di Servizi di ripristino da un'area diversa, se lo desiderano. L'insieme di credenziali di Servizi di ripristino contiene metadati ma non dati effettivi del cliente.

Site Recovery servizio contribuisce alla strategia di continuità aziendale e ripristino di emergenza mantenendo le app aziendali in esecuzione, durante le interruzioni pianificate e non pianificate. È consigliabile eseguire il ripristino di emergenza per mantenere le applicazioni in esecuzione in caso di interruzioni a livello di regione.

Le zone di disponibilità sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona ha uno o più data center. 

Se si vogliono spostare macchine virtuali in una zona di disponibilità in un'area diversa, [vedere questo articolo.](../resource-mover/move-region-availability-zone.md)

## <a name="using-availability-zones-for-disaster-recovery"></a>Uso di zone di disponibilità per il ripristino di emergenza 

In genere, zone di disponibilità vengono usate per distribuire macchine virtuali in una configurazione a disponibilità elevata. Potrebbero essere troppo vicine per fungere da soluzione di ripristino di emergenza in caso di emergenza naturale.

Tuttavia, in alcuni scenari è possibile zone di disponibilità per il ripristino di emergenza:

- Molti clienti che hanno una strategia di ripristino di emergenza metro durante l'hosting di applicazioni in locale talvolta sembrano simulare questa strategia dopo la migrazione delle applicazioni ad Azure. Questi clienti riconoscono che la strategia di ripristino di emergenza della metro potrebbe non funzionare in caso di emergenza fisica su larga scala e accettare questo rischio. Per tali clienti, il ripristino di emergenza da zona a zona può essere usato come opzione di ripristino di emergenza.

- Molti altri clienti hanno un'infrastruttura di rete complessa e non vogliono ricrearla in un'area secondaria a causa dei costi e della complessità associati. Il ripristino di emergenza da zona a zona riduce la complessità perché sfrutta i concetti di rete ridondanti zone di disponibilità semplificando notevolmente la configurazione. Questi clienti preferiscono la semplicità e possono anche usare zone di disponibilità per il ripristino di emergenza.

- In alcune aree che non dispongono di un'area abbinata all'interno della stessa giurisdizione legale (ad esempio Asia sud-orientale), il ripristino di emergenza da zona a zona può fungere da soluzione di ripristino di emergenza di fatto perché consente di garantire la conformità legale, poiché le applicazioni e i dati non si spostano oltre i confini nazionali. 

- Il ripristino di emergenza da zona a zona implica la replica dei dati su distanze più brevi rispetto al ripristino di emergenza da Azure ad Azure e, di conseguenza, potrebbe verificarsi una latenza più bassa e di conseguenza un RPO inferiore.

Anche se si tratta di vantaggi notevoli, è possibile che il ripristino di emergenza da zona a zona non sia in grado di soddisfare i requisiti di resilienza in caso di emergenza naturale a livello di area.

## <a name="networking-for-zone-to-zone-disaster-recovery"></a>Rete per il ripristino di emergenza da zona a zona

Come accennato in precedenza, il ripristino di emergenza da zona a zona riduce la complessità perché sfrutta i concetti di rete ridondanti zone di disponibilità semplificando notevolmente la configurazione. Il comportamento dei componenti di rete nello scenario di ripristino di emergenza da zona a zona è descritto di seguito: 

- Rete virtuale: è possibile usare la stessa rete virtuale della rete di origine per i failover effettivi. Usare una rete virtuale diversa rispetto alla rete virtuale di origine per i failover di test.

- Subnet: è supportato il failover nella stessa subnet.

- Indirizzo IP privato: se si usano indirizzi IP statici, è possibile usare gli stessi indirizzi IP nella zona di destinazione se si sceglie di configurarli in questo modo.

- Rete accelerata: analogamente ad Azure al ripristino di emergenza di Azure, è possibile abilitare Rete accelerata se lo SKU della macchina virtuale lo supporta.

- Indirizzo IP pubblico: è possibile collegare un indirizzo IP pubblico standard creato in precedenza nella stessa area alla macchina virtuale di destinazione. Gli indirizzi IP pubblici di base non supportano scenari correlati alla zona di disponibilità.

- Bilanciamento del carico: il servizio di bilanciamento del carico Standard è una risorsa a livello di regione e pertanto la macchina virtuale di destinazione può essere collegata al pool back-end dello stesso servizio di bilanciamento del carico. Non è necessario un nuovo servizio di bilanciamento del carico.

- Gruppo di sicurezza di rete: è possibile usare gli stessi gruppi di sicurezza di rete applicati alla macchina virtuale di origine.

## <a name="pre-requisites"></a>Prerequisiti

Prima di distribuire il ripristino di emergenza da zona a zona per le macchine virtuali, è importante assicurarsi che altre funzionalità abilitate nella macchina virtuale siano interoperativi con il ripristino di emergenza da zona a zona.

|Funzionalità  | Informativa sul supporto  |
|---------|---------|
|Macchine virtuali classiche   |     Non supportato    |
|Macchine virtuali arm    |    Supportato    |
|Crittografia dischi di Azure v1 (doppio passaggio, con Azure Active Directory (Azure AD))     |     Supportato   |
|Crittografia dischi di Azure v2 (passaggio singolo, senza Azure AD)    |    Supportato    |
|Dischi non gestiti    |    Non supportato    |
|Dischi gestiti    |    Supportato    |
|Chiavi gestite dal cliente    |    Supportato    |
|Gruppi di selezione host di prossimità    |    Supportato    |
|Interoperabilità dei backup    |    Sono supportati il backup e il ripristino a livello di file. Backup e ripristino a livello di disco e macchina virtuale e non supportati.    |
|Aggiunta/rimozione a caldo    |    I dischi possono essere aggiunti dopo l'abilitazione della replica da zona a zona. La rimozione dei dischi dopo l'abilitazione della replica da zona a zona non è supportata.    | 

## <a name="set-up-site-recovery-zone-to-zone-disaster-recovery"></a>Configurare il ripristino Site Recovery zona per il ripristino di emergenza nella zona

### <a name="log-in"></a>Accesso

Accedere al portale di Azure.

### <a name="enable-replication-for-the-zonal-azure-virtual-machine"></a>Abilitare la replica per la macchina virtuale di Azure di zona

1. Nel menu del portale di Azure selezionare Macchine virtuali oppure cercare e selezionare Macchine virtuali in qualsiasi pagina. Selezionare la macchina virtuale da replicare. Per il ripristino di emergenza da zona a zona, questa macchina virtuale deve essere già in una zona di disponibilità.

2. In Operazioni selezionare Ripristino di emergenza.

3. Come illustrato di seguito, nella scheda Informazioni di base selezionare "Sì" per "Ripristino di emergenza tra zone di disponibilità?".

    ![Pagina Impostazioni di base](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-basic-settings-blade.png)

4. Se si accettano tutte le impostazioni predefinite, fare clic su "Rivedi e avvia replica" e quindi su "Avvia replica".

5. Per apportare modifiche alle impostazioni di replica, fare clic su "Avanti: Impostazioni avanzate".

6. Modificare le impostazioni in modo da non essere predefinite, laddove appropriato. Per gli utenti del ripristino di emergenza da Azure ad Azure, questa pagina potrebbe sembrare familiare. Altre informazioni sulle opzioni presentate in questo pannello sono disponibili [qui](./azure-to-azure-tutorial-enable-replication.md)

    ![Pagina Impostazioni avanzate](./media/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery/zonal-disaster-recovery-advanced-settings-blade.png)

7. Fare clic su "Avanti: Rivedi e avvia replica" e quindi su "Avvia replica".

## <a name="faqs"></a>Domande frequenti

**1. Come funzionano i prezzi per il ripristino di emergenza da zona a zona?**
I prezzi per il ripristino di emergenza da zona a zona sono identici a quelli di Azure per il ripristino di emergenza di Azure. Per altri dettagli, vedere la pagina dei prezzi [qui](https://azure.microsoft.com/pricing/details/site-recovery/) [e qui.](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) Si noti che gli addebiti in uscita che si noterebbe nel ripristino di emergenza da zona a zona sarebbero inferiori al ripristino di emergenza da area a area.

**2. Qual è il contratto di servizio per RTO e RPO?**
Il contratto di servizio RTO è uguale a quello per Site Recovery generale. L'RTO è promessa di un massimo di 2 ore. Non esiste alcun contratto di servizio definito per l'RPO.

**3. La capacità è garantita nella zona secondaria?**
Il team di Site Recovery e il team di gestione della capacità di Azure pianificano una capacità dell'infrastruttura sufficiente. Quando si avvia un failover, i team garantiscono anche che le istanze di macchine virtuali protette da Site Recovery verranno distribuite nella zona di destinazione.

**4. Quali sistemi operativi sono supportati?**
Il ripristino di emergenza da zona a zona supporta gli stessi sistemi operativi del ripristino di emergenza da Azure ad Azure. Fare riferimento alla matrice di supporto [qui.](./azure-to-azure-support-matrix.md)

**5. I gruppi di risorse di origine e di destinazione possono essere uguali?**
No, è necessario eseguire il failover in un gruppo di risorse diverso.

## <a name="next-steps"></a>Passaggi successivi

I passaggi da seguire per eseguire un'esercitazione sul ripristino di emergenza, eseguire il failover, la nuova protezione e il failback sono gli stessi di uno scenario di ripristino di emergenza da Azure ad Azure.

Per eseguire un'esercitazione sul ripristino di emergenza, seguire la procedura descritta [qui.](./azure-to-azure-tutorial-dr-drill.md)

Per eseguire un failover e riprotezione delle macchine virtuali nella zona secondaria, seguire la procedura descritta [qui.](./azure-to-azure-tutorial-failover-failback.md)

Per eseguire il failback nella zona primaria, seguire la procedura descritta [qui.](./azure-to-azure-tutorial-failback.md)
