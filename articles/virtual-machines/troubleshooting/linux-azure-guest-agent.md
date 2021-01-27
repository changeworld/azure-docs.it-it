---
title: Risolvere i problemi dell'agente Linux di Azure
description: Risolvere i problemi relativi all'agente Linux di Azure.
services: virtual-machines-linux
ms.service: virtual-machines-linux
author: axelg
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/17/2020
ms.author: axelg
ms.openlocfilehash: 62b462d8e75fc291ac599ac99dbe4fb3a74fde2b
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878698"
---
# <a name="troubleshoot-the-azure-linux-agent"></a>Risolvere i problemi dell'agente Linux di Azure

L' [agente Linux di Azure](../extensions/agent-linux.md) consente a una macchina virtuale (VM) di comunicare con il controller di infrastruttura (il server fisico sottostante in cui è ospitata la VM) nell'indirizzo IP 168.63.129.16.

>[!NOTE]
>Questo indirizzo IP è un indirizzo IP pubblico virtuale che facilita la comunicazione e non deve essere bloccato. Per ulteriori informazioni, vedere [che cos'è l'indirizzo IP 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md).

## <a name="before-you-begin"></a>Prima di iniziare

Verificare lo stato e la versione dell'agente per assicurarsi che sia ancora supportata. Vedere [supporto della versione minima per gli agenti di macchina virtuale in Azure](/troubleshoot/azure/virtual-machines/support-extensions-agent-version) per verificare il supporto della versione o vedere [domande frequenti su WALinuxAgent](https://github.com/Azure/WALinuxAgent/wiki/FAQ#what-does-goal-state-agent-mean-in-waagent---version-output) per informazioni su come individuare lo stato e la versione.

## <a name="troubleshoot-a-not-ready-status"></a>Risolvere i problemi relativi a uno stato non pronto

1. Verificare lo stato del servizio dell'agente Linux di Azure per assicurarsi che sia in esecuzione. Il nome del servizio potrebbe essere **walinuxagent** o **waagent**.

   ```
   root@nam-u18:/home/nam# service walinuxagent status
   ● walinuxagent.service - Azure Linux Agent
      Loaded: loaded (/lib/systemd/system/walinuxagent.service; enabled; vendor preset: enabled)
      Active: active (running) since Thu 2020-10-08 17:10:29 UTC; 3min 9s ago
    Main PID: 1036 (python3)
       Tasks: 4 (limit: 4915)
      CGroup: /system.slice/walinuxagent.service
              ├─1036 /usr/bin/python3 -u /usr/sbin/waagent -daemon
              └─1156 python3 -u bin/WALinuxAgent-2.2.51-py2.7.egg -run-exthandlers
   Oct 08 17:10:33 nam-u18 python3[1036]: 2020-10-08T17:10:33.129375Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.OSTCExtensions.VMAccessForLinux-1.5.10, path: /sys/fs/cgroup/memory/sys
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.189020Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Target handler state: enabled [incarnation 2]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.197932Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] [Enable] current handler state is: enabled
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.212316Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Update settings file: 0.settings
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.224062Z INFO ExtHandler [Microsoft.CPlat.Core.RunCommandLinux-1.0.1] Enable extension [bin/run-command-shim enable]
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.236993Z INFO ExtHandler ExtHandler Started extension in unit 'Microsoft.CPlat.Core.RunCommandLinux_1.0.1_db014406-294a-49ed-b112-c7912a86ae9e
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.263572Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/cpu,cpuacct/syst
   Oct 08 17:10:35 nam-u18 python3[1036]: 2020-10-08T17:10:35.280691Z INFO ExtHandler ExtHandler Started tracking cgroup: Microsoft.CPlat.Core.RunCommandLinux-1.0.1, path: /sys/fs/cgroup/memory/system.sl
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.349090Z INFO ExtHandler ExtHandler ProcessGoalState completed [incarnation 2; 4496 ms]
   Oct 08 17:10:37 nam-u18 python3[1036]: 2020-10-08T17:10:37.365590Z INFO ExtHandler ExtHandler [HEARTBEAT] Agent WALinuxAgent-2.2.51 is running as the goal state agent [DEBUG HeartbeatCounter: 1;Heartb
   root@nam-u18:/home/nam#
   ```

   Se il servizio è in esecuzione, riavviarlo per risolvere il problema. Se il servizio è arrestato, avviarlo, attendere alcuni minuti, quindi controllare di nuovo lo stato.

1. Verificare che l'opzione aggiornamento automatico sia abilitata. Controllare l'impostazione di aggiornamento automatico in **/etc/waagent.conf**.

   ```
   AutoUpdate.Enabled=y
   ```

   Per altre informazioni su come aggiornare l'agente Linux di Azure, vedere [come aggiornare l'agente Linux di Azure in una macchina virtuale](../extensions/update-linux-agent.md).

1. Assicurarsi che la macchina virtuale sia in grado di connettersi al controller di infrastruttura. Usare uno strumento come curl per verificare se la macchina virtuale è in grado di connettersi a 168.63.129.16 sulle porte 80, 443 e 32526. Se la macchina virtuale non si connette come previsto, controllare se la comunicazione in uscita sulle porte 80, 443 e 32526 è aperta nel firewall locale della macchina virtuale. Se l'indirizzo IP è bloccato, l'agente VM potrebbe visualizzare un comportamento imprevisto.

## <a name="advanced-troubleshooting"></a>Risoluzione dei problemi avanzata

Gli eventi per la risoluzione dei problemi relativi all'agente Linux di Azure vengono registrati nel file **/var/log/waagent.log** .

### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Impossibile connettersi a WireServer IP (host IP)

Il seguente errore viene visualizzato nel file **/var/log/waagent.log** quando la macchina virtuale non riesce a raggiungere l'IP WireServer nel server host.

```
2020-10-02T18:11:13.148998Z WARNING ExtHandler ExtHandler An error occurred while retrieving the goal state:
```

Per risolvere il problema:

* Connettersi alla macchina virtuale tramite SSH, quindi provare ad accedere all'URL seguente da CURL: http://168.63.129.16/?comp=versions .
* Verificare la presenza di problemi che potrebbero essere causati da un firewall, da un proxy o da un'altra origine che potrebbe bloccare l'accesso all'indirizzo IP 168.63.129.16.
* Controllare se IPTables Linux o un firewall di terze parti sta bloccando l'accesso alle porte 80, 443 e 32526.

## <a name="next-steps"></a>Passaggi successivi

Per risolvere i problemi dell'agente Linux di Azure, [contattare il supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).