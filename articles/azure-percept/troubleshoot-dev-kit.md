---
title: Risolvere i problemi generali relativi ad Azure Percept DK e IoT Edge
description: Suggerimenti per la risoluzione dei problemi relativi ad alcuni dei problemi più comuni relativi ad Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 826759907bfe5ec3359bf5c9125909466372c68f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608147"
---
# <a name="azure-percept-dk-dev-kit-troubleshooting"></a>Risoluzione dei problemi di Azure Percept DK (Dev Kit)

Per suggerimenti generali sulla risoluzione dei problemi relativi ad Azure Percept DK, vedere le linee guida riportate di seguito.

## <a name="general-troubleshooting-commands"></a>Comandi generali per la risoluzione dei problemi

Per eseguire questi comandi, 
1. Connettersi al [Wi-Fi AP del kit di sviluppo](./quickstart-percept-dk-set-up.md)
1. [SSH in dev Kit](./how-to-ssh-into-percept-dk.md)
1. Immettere i comandi nel terminale SSH

Per reindirizzare l'output a un file con estensione txt per un'ulteriore analisi, usare la sintassi seguente:

```console
sudo [command] > [file name].txt
```

Modificare le autorizzazioni del file. txt in modo che sia possibile copiarlo:

```console
sudo chmod 666 [file name].txt
```

Dopo il reindirizzamento dell'output a un file con estensione txt, copiare il file nel PC host tramite SCP:

```console
scp [remote username]@[IP address]:[remote file path]/[file name].txt [local host file path]
```

```[local host file path]``` si riferisce al percorso nel computer host in cui si desidera copiare il file con estensione txt. ```[remote username]``` nome utente SSH scelto durante l' [installazione](./quickstart-percept-dk-set-up.md). Se durante la configurazione guidata non è stato configurato un account di accesso SSH, il nome utente remoto sarà ```root``` .

Per ulteriori informazioni sui comandi di Azure IoT Edge, vedere la [documentazione relativa alla risoluzione dei problemi del dispositivo Azure IOT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

|Categoria:         |Comando:                    |Funzione:                  |
|------------------|----------------------------|---------------------------|
|OS                |```cat /etc/os-release```         |verificare la versione dell'immagine Mariner |
|OS                |```cat /etc/os-subrelease```      |verificare la versione dell'immagine derivata |
|OS                |```cat /etc/adu-version```        |controllare la versione di ADU |
|Temperatura       |```cat /sys/class/thermal/thermal_zone0/temp``` |controllare la temperatura di DevKit |
|Wi-Fi             |```sudo journalctl -u hostapd.service``` |controllare i log di SoftAP|
|Wi-Fi             |```sudo journalctl -u wpa_supplicant.service``` |controllare i registri dei servizi Wi-Fi |
|Wi-Fi             |```sudo journalctl -u ztpd.service```  |controllare Wi-Fi log del servizio di provisioning con zero touch |
|Wi-Fi             |```sudo journalctl -u systemd-networkd``` |controllare i log dello stack di rete Mariner |
|Wi-Fi             |```sudo cat /etc/hostapd/hostapd-wlan1.conf``` |controllare i dettagli di configurazione del punto di accesso Wi-Fi |
|Configurazione guidata              |```sudo journalctl -u oobe -b```       |controllare i log OOBE |
|Telemetria         |```sudo azure-device-health-id```      |trova HW_ID di telemetria univoci |
|Azure IoT Edge          |```sudo iotedge check```          |eseguire controlli di configurazione e connettività per problemi comuni |
|Azure IoT Edge          |```sudo iotedge logs [container name]``` |controllare i log dei contenitori, ad esempio moduli di riconoscimento vocale e visione artificiale |
|Azure IoT Edge          |```sudo iotedge support-bundle --since 1h``` |raccogliere i registri dei moduli, i log di Azure IoT Edge Security Manager, i log del motore di contenitori, l' ```iotedge check``` output JSON e altre informazioni utili per il debug dell'ora precedente |
|Azure IoT Edge          |```sudo journalctl -u iotedge -f``` |visualizzare i log di Azure IoT Edge Security Manager |
|Azure IoT Edge          |```sudo systemctl restart iotedge``` |riavviare il daemon di sicurezza di Azure IoT Edge |
|Azure IoT Edge          |```sudo iotedge list```           |elencare i moduli di Azure IoT Edge distribuiti |
|Altro             |```df [option] [file]```          |Visualizza informazioni sullo spazio disponibile/totale nei file system specificati |
|Altro             |`ip route get 1.1.1.1`        |visualizzare le informazioni sull'interfaccia e sull'IP del dispositivo |
|Altro             |<code>ip route get 1.1.1.1 &#124; awk '{print $7}'</code> <br> `ifconfig [interface]` |Visualizza solo l'indirizzo IP del dispositivo |


I ```journalctl``` comandi di Wi-Fi possono essere combinati nel comando singolo seguente:

```console
sudo journalctl -u hostapd.service -u wpa_supplicant.service -u ztpd.service -u systemd-networkd -b
```

## <a name="docker-troubleshooting-commands"></a>Comandi per la risoluzione dei problemi di Docker

|Comando:                        |Funzione:                  |
|--------------------------------|---------------------------|
|```sudo docker ps``` |[Mostra i contenitori in esecuzione](https://docs.docker.com/engine/reference/commandline/ps/) |
|```sudo docker images``` |[Mostra le immagini presenti nel dispositivo](https://docs.docker.com/engine/reference/commandline/images/)|
|```sudo docker rmi [image id] -f``` |[Elimina un'immagine dal dispositivo](https://docs.docker.com/engine/reference/commandline/rmi/) |
|```sudo docker logs -f edgeAgent``` <br> ```sudo docker logs -f [module_name]``` |[accetta i log del contenitore del modulo specificato](https://docs.docker.com/engine/reference/commandline/logs/) |
|```sudo docker image prune``` |[Rimuove tutte le immagini in sospeso](https://docs.docker.com/engine/reference/commandline/image_prune/) |
|```sudo watch docker ps``` <br> ```watch ifconfig [interface]``` |verificare lo stato di download del contenitore Docker |

## <a name="usb-updating"></a>Aggiornamento USB

|Errore:                                    |Soluzione:                                               |
|------------------------------------------|--------------------------------------------------------|
|LIBUSB_ERROR_XXX durante flash USB tramite UUU |Questo errore è il risultato di un errore di connessione USB durante l'aggiornamento di UUU. Se il cavo USB non è connesso correttamente alle porte USB del PC o a PE-10X, si verificherà un errore di questo modulo. Provare a scollegare e ricollegare entrambe le estremità del cavo USB e scuotere il cavo per garantire una connessione protetta. Questo risolve quasi sempre il problema. |

## <a name="azure-percept-dk-carrier-board-led-states"></a>Stati del LED della lavagna del vettore di Azure Percept DK

Sono presenti tre piccoli LED nella parte superiore dell'alloggiamento della lavagna del vettore. Accanto al LED 1 viene stampata un'icona del cloud, viene visualizzata un'icona Wi-Fi accanto al LED 2 e viene stampata un'icona con il punto esclamativo accanto al LED 3. Vedere la tabella seguente per informazioni su ogni stato del LED.

|LED             |State      |Descrizione                      |
|----------------|-----------|---------------------------------|
|LED 1 (hub tutto) |On (tinta unita) |Il dispositivo è connesso a un hub Internet. |
|LED 2 (Wi-Fi)   |Lampeggio lento |Il dispositivo è pronto per essere configurato con Wi-Fi Easy Connect e annuncia la sua presenza a uno strumento di configurazione. |
|LED 2 (Wi-Fi)   |Lampeggio rapido |L'autenticazione è riuscita, l'associazione del dispositivo è in corso. |
|LED 2 (Wi-Fi)   |On (tinta unita) |Autenticazione e associazione sono state completate. il dispositivo è connesso a una rete Wi-Fi. |
|LED 3           |N/D         |Il LED non è in uso. |


