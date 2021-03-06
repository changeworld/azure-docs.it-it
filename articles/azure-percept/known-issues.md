---
title: Problemi noti di Azure Percept
description: Altre informazioni sui problemi noti di Azure Percept e sulle relative soluzioni alternative
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: reference
ms.date: 03/25/2021
ms.openlocfilehash: 49ac497505930d82a3ab8e90fb3f386cc1741dc7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605060"
---
# <a name="known-issues"></a>Problemi noti

Se si verifica uno di questi problemi, non è necessario aprire un bug. Se si verificano problemi con una qualsiasi soluzione alternativa, aprire un problema.

|Area|Descrizione del problema|Soluzione alternativa|
|-------|---------|---------|
| Esperienza di onboarding | Non è possibile completare l'esperienza di onboarding a meno che non sia configurata la Wi-Fi del dispositivo (accesso di Azure non riuscito). | 1. [SSH in Azure PERCEPT dk](./how-to-ssh-into-percept-dk.md). <br> 2. identificare e copiare l'indirizzo IP Ethernet del dispositivo. <br> 3. connettersi all'esperienza di onboarding usando l'URL basato su IP Ethernet. |
| Esperienza di onboarding | Quando si fa clic sui collegamenti nel contratto di licenza (EULA), a volte non viene aperta una nuova pagina Web. | Copiare il collegamento e aprirlo in una finestra del browser distinta. |
| Esperienza di onboarding | Non è possibile usare l'esperienza di onboarding quando si è connessi a un hotspot per dispositivi mobili Wi-Fi. | Connettere il dispositivo direttamente al SoftAP, a una rete Wi-Fi o a una rete su Ethernet. |
| Wi-Fi | Il SoftAP può talvolta disconnettersi o scomparire. | Stiamo esaminando.  Il riavvio del dispositivo in genere lo riporta. |
| Wi-Fi | Il pulsante hardware che attiva o disattiva la Wi-Fi SoftAP a volte non funziona. | Continuare a premere il pulsante o riavviare il dispositivo. |
| Wi-Fi | Gli utenti possono visualizzare un messaggio dopo la connessione al Wi-Fi: <br> "Questa rete Wi-Fi usa uno standard di sicurezza obsoleto". | Il SoftAP di DevKit usa l'algoritmo di crittografia WEP. |
| Wi-Fi | Non è possibile connettersi al SoftAP dal PC Windows 10 con il messaggio di errore seguente: <br> "Non è possibile connettersi a questa rete" | Riavviare sia DevKit che il computer. |
| Aggiornamento dei dispositivi | I contenitori non vengono eseguiti dopo un aggiornamento OTA. | Connettersi tramite SSH al dispositivo e riavviare il contenitore IoT Edge con il comando seguente: `systemctl restart iotedge` . Tutti i contenitori vengono riavviati. |
| Aggiornamento dei dispositivi | È possibile che gli utenti ottengano un messaggio di errore durante l'aggiornamento, anche se ha avuto esito positivo. | Confermare l'aggiornamento passando al dispositivo gemello di DevKit nell'hub Internet e controllando il valore di `swVersion` . Questo problema viene risolto dopo il primo aggiornamento. |
| Aggiornamento dei dispositivi | È possibile che gli utenti perdano le impostazioni di connessione Wi-Fi dopo il primo aggiornamento. | Eseguire l'esperienza di onboarding dopo l'aggiornamento per configurare la connessione Wi-Fi. Questo problema viene risolto dopo il primo aggiornamento. |
| Aggiornamento dei dispositivi | Dopo l'esecuzione di un aggiornamento OTA, gli utenti non possono più accedere tramite SSH usando gli account utente creati in precedenza e non è possibile creare nuovi utenti SSH tramite l'esperienza di onboarding. Questo problema interessa i sistemi che eseguono aggiornamenti OTA dalle seguenti versioni di immagine preinstallate: 2020.110.114.105 e 2020.109.101.105. | Per ripristinare i profili utente, seguire questa procedura dopo l'aggiornamento OTA: <br> Connettersi tramite [SSH alla DevKit](./how-to-ssh-into-percept-dk.md) usando "root" come nome utente. Se è stato disabilitato l'accesso utente "root" SSH tramite l'esperienza di onboarding, è necessario riabilitarlo. Eseguire questo comando dopo la connessione: <br> ```mkdir -p /var/custom-configs/home; chmod 755 /var/custom-configs/home``` <br> Per ripristinare i dati della Home page dell'utente precedente, eseguire il comando seguente: <br> ```mkdir -p /tmp/prev-rootfs && mount /dev/mmcblk0p3 /tmp/prev-rootfs && [ ! -L /tmp/prev-rootfs/home ] && cp -a /tmp/prev-rootfs/home/* /var/custom-configs/home/. && echo "User home migrated!"; umount /tmp/prev-rootfs``` |
| Aggiornamento dei dispositivi | Dopo l'esecuzione di un aggiornamento OTA, i gruppi di aggiornamento andranno perduti. | Aggiornare il tag del dispositivo seguendo [queste istruzioni](./how-to-update-over-the-air.md#create-a-device-update-group). |
| Programma di installazione di Dev Tools Pack | L'installazione facoltativa di caffe potrebbe non riuscire se Docker non viene eseguito correttamente nel sistema. | Assicurarsi che Docker sia installato e in esecuzione, quindi riprovare l'installazione di caffe. |
| Programma di installazione di Dev Tools Pack | L'installazione CUDA facoltativa non riesce nei sistemi incompatibili. | Verificare la compatibilità del sistema con CUDA prima di eseguire il programma di installazione. |
| Docker, rete, IoT Edge | Se la rete interna USA 172. x.x. x, i contenitori Docker non riusciranno a connettersi a IoT Edge. | Aggiungere una sezione bip speciale al daemon.js/etc/Docker/nel file come segue: `{    "bip": "192.168.168.1/24"}` |
|Azure Percept Studio | I collegamenti "Visualizza flusso" in Azure Percept Studio non aprono una nuova finestra che mostra il flusso Web del dispositivo. | 1. Aprire il [portale di Azure](https://portal.azure.com) e selezionare **Hub** Internet. <br> 2. fare clic sull'hub delle cose a cui è connesso il dispositivo. <br> 3. Selezionare **IOT Edge** in **gestione automatica dispositivi** nella pagina dell'hub Internet delle cose. <br> 4. Selezionare il dispositivo dall'elenco. <br> 5. Selezionare **imposta moduli** nella parte superiore della pagina del dispositivo. <br> 6. fare clic sull'icona del cestino accanto a **HostIpModule** per eliminare il modulo. <br> 7. per confermare l'azione, fare clic su **Verifica + crea** e quindi su **Crea**. <br> 8. Aprire [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) e fare clic su **dispositivi** nel pannello di menu a sinistra. <br> 9. Selezionare il dispositivo dall'elenco. <br> 10. nella scheda **visione** , fare clic su **Visualizza il flusso del dispositivo**. Nel dispositivo viene scaricata una nuova versione di HostIpModule e viene aperta una scheda del browser con il flusso Web del dispositivo. |