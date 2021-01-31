---
title: Aggiornamento della versione principale nel database di Azure per MySQL-server singolo
description: Questo articolo descrive come è possibile aggiornare la versione principale per database di Azure per MySQL-server singolo
author: Bashar-MSFT
ms.author: bahusse
ms.service: mysql
ms.topic: how-to
ms.date: 1/28/2021
ms.openlocfilehash: 62faaed3672f721b26587d1bca3ddb0947f733e7
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2021
ms.locfileid: "99220837"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Aggiornamento della versione principale nel server singolo database di Azure per MySQL

> [!NOTE]
> Questo articolo contiene riferimenti al termine _slave_, un termine che Microsoft non usa più. Quando il termine viene rimosso dal software, questo verrà rimosso da questo articolo.
>

> [!IMPORTANT]
> L'aggiornamento della versione principale per il server singolo di database di Azure per MySQL è in anteprima pubblica.

Questo articolo descrive come aggiornare la versione principale di MySQL sul posto nel database di Azure per il singolo server MySQL.

Questa funzionalità consentirà ai clienti di eseguire aggiornamenti sul posto dei propri server MySQL 5,6 a MySQL 5,7 con un clic del pulsante senza lo spostamento dei dati o la necessità di modificare le stringhe di connessione dell'applicazione.

> [!Note]
> * L'aggiornamento della versione principale è disponibile solo per l'aggiornamento della versione principale da MySQL 5,6 a MySQL 5,7.
> * Il server non sarà disponibile durante l'operazione di aggiornamento. È pertanto consigliabile eseguire gli aggiornamenti durante la finestra di manutenzione pianificata. È possibile prendere [in considerazione l'aggiornamento della versione principale del tempo di inattività minimo da mysql 5,6 a mysql 5,7 con la replica di lettura.](#perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas)

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal"></a>Eseguire l'aggiornamento della versione principale da MySQL 5,6 a MySQL 5,7 usando portale di Azure

Seguire questa procedura per eseguire l'aggiornamento principale della versione per il database di Azure del server MySQL 5,6 usando portale di Azure

> [!IMPORTANT]
> È consigliabile eseguire prima l'aggiornamento su una copia ripristinata del server anziché aggiornare direttamente la produzione. Vedere [come eseguire il ripristino temporizzato](howto-restore-server-portal.md#point-in-time-restore).

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il server di database di Azure per MySQL 5,6 esistente.

2. Nella pagina **Panoramica** fare clic sul pulsante **Aggiorna** sulla barra degli strumenti.

3. Nella sezione **aggiornamento** selezionare **OK** per aggiornare il server di database di Azure per MySQL 5,6 al server 5,7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Database di Azure per MySQL-Panoramica-aggiornamento":::

4. Una notifica conferma che l'aggiornamento ha avuto esito positivo.


## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-cli"></a>Eseguire l'aggiornamento della versione principale da MySQL 5,6 a MySQL 5,7 usando l'interfaccia della riga di comando di Azure

Seguire questa procedura per eseguire l'aggiornamento principale della versione per il database di Azure del server MySQL 5,6 usando l'interfaccia della riga di comando di Azure

> [!IMPORTANT]
> È consigliabile eseguire prima l'aggiornamento su una copia ripristinata del server anziché aggiornare direttamente la produzione. Vedere [come eseguire il ripristino temporizzato](howto-restore-server-cli.md#server-point-in-time-restore).

1. Per eseguire i comandi di aggiornamento, installare l'interfaccia della riga di comando di [Azure per Windows](/cli/azure/install-azure-cli) o usare l' [Azure cloud Shell](../cloud-shell/overview.md) interfaccia della riga di comando 
 
   Questo aggiornamento richiede la versione 2.16.0 o successiva dell'interfaccia della riga di comando di Azure. Se si usa Azure Cloud Shell, la versione più recente è già installata. Eseguire az version per trovare la versione e le librerie dipendenti installate. Per eseguire l'aggiornamento alla versione più recente, eseguire az upgrade.

2. Dopo l'accesso, eseguire il comando [AZ MySQL Server Upgrade](https://docs.microsoft.com/cli/azure/mysql/server?view=azure-cli-latest#az_mysql_server_upgrade&preserve-view=true) :

   ```azurecli
   az mysql server upgrade --name testsvr --resource-group testgroup --subscription MySubscription --target-server-version 5.7"
   ```
   
   Il prompt dei comandi Visualizza il messaggio "-Running". Quando questo messaggio non viene più visualizzato, l'aggiornamento della versione è completato.

## <a name="perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal"></a>Eseguire l'aggiornamento della versione principale da MySQL 5,6 a MySQL 5,7 in lettura replica usando portale di Azure

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il server di replica del database di Azure per MySQL 5,6 in lettura.

2. Nella pagina **Panoramica** fare clic sul pulsante **Aggiorna** sulla barra degli strumenti.

3. Nella sezione **aggiornamento** selezionare **OK** per aggiornare il database di Azure per MySQL 5,6 leggere il server di replica nel server 5,7.

   :::image type="content" source="./media/how-to-major-version-upgrade-portal/upgrade.png" alt-text="Database di Azure per MySQL-Panoramica-aggiornamento":::

4. Una notifica conferma che l'aggiornamento ha avuto esito positivo.

5. Dalla pagina **Overview (panoramica** ) verificare che la versione del server di replica del database di Azure per MySQL in lettura sia 5,7.

6. Passare quindi al server primario ed [eseguire l'aggiornamento della versione principale](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-using-azure-portal) .

## <a name="perform-minimal-downtime-major-version-upgrade-from-mysql-56-to-mysql-57-using-read-replicas"></a>Eseguire l'aggiornamento della versione principale del tempo di inattività minimo da MySQL 5,6 a MySQL 5,7 usando le repliche di lettura

È possibile eseguire un aggiornamento della versione principale di tempo di inattività minimo da MySQL 5,6 a MySQL 5,7 usando le repliche di lettura. L'idea è aggiornare la replica di lettura del server a 5,7 prima e successivamente eseguire il failover dell'applicazione in modo da puntare alla lettura della replica e impostarla come nuova replica primaria.

1. Nella [portale di Azure](https://portal.azure.com/)selezionare il database di Azure per MySQL 5,6 esistente.

2. Creare una [replica di lettura](https://docs.microsoft.com/azure/mysql/concepts-read-replicas#create-a-replica) dal server primario.

3. [Aggiornare la replica di lettura](#perform-major-version-upgrade-from-mysql-56-to-mysql-57-on-read-replica-using-azure-portal) alla versione 5,7.

4. Dopo aver verificato che il server di replica sia in esecuzione nella versione 5,7, arrestare l'applicazione per la connessione al server primario.
 
5. Verificare lo stato della replica e verificare che la replica sia stata rilevata con la replica primaria, in modo che tutti i dati siano sincronizzati e che non siano state eseguite nuove operazioni nel database primario.

   Chiamare il [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) comando sul server di replica per visualizzare lo stato della replica.

   ```sql
   SHOW SLAVE STATUS\G
   ```

   Se lo stato di `Slave_IO_Running` e è `Slave_SQL_Running` "Yes" e il valore di `Seconds_Behind_Master` è "0", la replica funziona correttamente. `Seconds_Behind_Master` indica il ritardo della replica. Se il valore non è "0", significa che la replica sta elaborando gli aggiornamenti. Quando si conferma `Seconds_Behind_Master` è "0", è possibile arrestare la replica.

6. Alzare di livello la replica di lettura al database primario [arrestando la replica](https://docs.microsoft.com/azure/mysql/howto-read-replicas-portal#stop-replication-to-a-replica-server).

7. Puntare l'applicazione al nuovo database primario (replica precedente) che esegue il server 5,7. Ogni server dispone di una stringa di connessione univoca. Aggiornare l'applicazione in modo che punti alla replica (precedente) invece che all'origine.

> [!Note]
> Questo scenario avrà tempi di inattività solo nei passaggi 4, 5 e 6.


## <a name="frequently-asked-questions"></a>Domande frequenti

### <a name="when-will-this-upgrade-feature-be-ga-as-we-have-mysql-v56-in-our-production-environment-that-we-need-to-upgrade"></a>Quando questa funzionalità di aggiornamento sarà GA perché nell'ambiente di produzione è disponibile MySQL v 5.6, è necessario eseguire l'aggiornamento?

La GA di questa funzionalità è pianificata prima del ritiro di MySQL v 5.6. Tuttavia, la funzionalità è pronta per la produzione e completamente supportata da Azure, pertanto è consigliabile eseguirla in tutta sicurezza nell'ambiente in uso. Come procedura consigliata, è consigliabile eseguire e testare prima di tutto una copia ripristinata del server in modo che sia possibile stimare il tempo di inattività durante l'aggiornamento ed eseguire il test di compatibilità delle applicazioni prima di eseguirlo nell'ambiente di produzione. Per ulteriori informazioni, vedere [come eseguire il ripristino temporizzato](howto-restore-server-portal.md#point-in-time-restore) per creare una copia temporizzata del server. 

### <a name="will-this-cause-downtime-of-the-server-and-if-so-how-long"></a>Questa operazione causerà tempi di inattività del server e, in caso affermativo, per quanto tempo?

Sì, il server non sarà disponibile durante il processo di aggiornamento, pertanto si consiglia di eseguire questa operazione durante la finestra di manutenzione pianificata. Il tempo di inattività stimato dipende dalla dimensione del database, dalle dimensioni di archiviazione di cui è stato effettuato il provisioning (IOPs con provisioning) e dal numero di tabelle nel database. Il tempo di aggiornamento è direttamente proporzionale al numero di tabelle nel server. Gli aggiornamenti dei server SKU Basic dovrebbero richiedere più tempo, perché si trova nella piattaforma di archiviazione standard. Per stimare il tempo di inattività per l'ambiente server, è consigliabile eseguire prima l'aggiornamento sulla copia ripristinata del server.  

### <a name="it-is-noted-that-it-is-not-supported-on-replica-server-yet-what-does-that-mean-concrete"></a>Si noti che non è ancora supportata nel server di replica. Che cosa significa concreto?

Attualmente, l'aggiornamento della versione principale non è supportato per il server di replica, pertanto non è consigliabile eseguirlo per i server che interessano la replica, ovvero il server di origine o di replica. Se si desidera testare l'aggiornamento dei server che coinvolgono la replica prima di aggiungere il supporto della replica per la funzionalità di aggiornamento, è consigliabile seguire questa procedura:

1. Durante la manutenzione pianificata, [arrestare la replica ed eliminare il server di replica](howto-read-replicas-portal.md) dopo aver acquisito il nome e tutte le informazioni di configurazione (impostazioni del firewall, configurazione dei parametri del server se è diversa da quella del server di origine).
2. Eseguire l'aggiornamento del server di origine.
3. Eseguire il provisioning di un nuovo server di replica di lettura con le stesse impostazioni di configurazione e nome acquisite nel passaggio 1. Il nuovo server di replica sarà automaticamente in v 5.7 dopo l'aggiornamento del server di origine a v 5.7.

### <a name="what-will-happen-if-we-do-not-choose-to-upgrade-our-mysql-v56-server-before-february-5-2021"></a>Cosa accade se non si sceglie di aggiornare il server MySQL v 5.6 prima del 5 febbraio 2021?

È comunque possibile continuare a eseguire il server MySQL v 5.6 come prima. Azure **non** eseguirà mai l'aggiornamento forzato sul server. Tuttavia, verranno applicate le restrizioni documentate nei [criteri di controllo delle versioni del database di Azure per MySQL](concepts-version-policy.md) .

## <a name="next-steps"></a>Passaggi successivi

Informazioni sui [criteri di controllo delle versioni di database di Azure per MySQL](concepts-version-policy.md).
