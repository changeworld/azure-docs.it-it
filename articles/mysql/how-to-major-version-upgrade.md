---
title: Aggiornamento della versione principale nel database di Azure per MySQL-server singolo
description: Questo articolo descrive come è possibile aggiornare la versione principale per database di Azure per MySQL-server singolo
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: how-to
ms.date: 1/13/2021
ms.openlocfilehash: b62f4ebc61ac27478788d8b2bae5e4145f87ac8b
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630201"
---
# <a name="major-version-upgrade-in-azure-database-for-mysql-single-server"></a>Aggiornamento della versione principale nel server singolo database di Azure per MySQL

> [!IMPORTANT]
> L'aggiornamento della versione principale per il server singolo di database di Azure per MySQL è in anteprima pubblica.

Questo articolo descrive come aggiornare la versione principale di MySQL sul posto nel database di Azure per il singolo server MySQL.

Questa funzionalità consentirà ai clienti di eseguire aggiornamenti sul posto dei propri server MySQL 5,6 a MySQL 5,7 con un clic del pulsante senza lo spostamento dei dati o la necessità di modificare le stringhe di connessione dell'applicazione.

> [!Note]
> * L'aggiornamento della versione principale è disponibile solo per l'aggiornamento della versione principale da MySQL 5,6 a MySQL 5,7<br>
> * L'aggiornamento della versione principale non è ancora supportato nel server di replica.
> * Il server non sarà disponibile durante l'operazione di aggiornamento. È pertanto consigliabile eseguire gli aggiornamenti durante la finestra di manutenzione pianificata.

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
