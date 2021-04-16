---
title: Impostare la modalità svuotamento di Desktop virtuale Windows - Azure
description: Come configurare e usare la modalità svuotamento in Desktop virtuale Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 19ef7d520800ac703ed77dc0520e5b860306c4bd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509126"
---
# <a name="set-drain-mode"></a>Impostare la modalità svuotamento

La modalità svuotamento isola un host di sessione quando si desidera applicare patch ed eseguire operazioni di manutenzione senza interrompere le sessioni utente. Se isolato, l'host sessione non accetterà nuove sessioni utente. Tutte le nuove connessioni verranno reindirizzate al successivo host di sessione disponibile. Le connessioni esistenti nell'host sessione continuano a funzionare fino a quando l'utente non si chiude o l'amministratore termina la sessione. Quando l'host sessione è in modalità svuotamento, gli amministratori possono anche connettersi in remoto al server senza passare attraverso il servizio Desktop virtuale Windows. È possibile applicare questa impostazione sia ai desktop in pool che ai desktop personali.

## <a name="set-drain-mode-using-the-azure-portal"></a>Impostare la modalità svuotamento usando il portale di Azure

Per attivare la modalità svuotamento nel portale di Azure:

1. Aprire il portale di Azure e passare al pool di host che si vuole isolare.

2. Nel menu di spostamento selezionare **Host sessione.**

3. Selezionare quindi gli host per cui si vuole attivare la modalità svuotamento, quindi selezionare **Attiva modalità svuotamento.**

4. Per disattivare la modalità svuotamento, selezionare i pool di host in cui è attivata la modalità svuotamento, quindi selezionare **Disattiva modalità svuotamento.**

## <a name="set-drain-mode-using-powershell"></a>Impostare la modalità svuotamento con PowerShell

È possibile impostare la modalità svuotamento in PowerShell con il parametro *AllowNewSessions,* che fa parte [del comando Update-AzWvdSessionhost.](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true)

Eseguire questo cmdlet per abilitare la modalità svuotamento:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

Eseguire questo cmdlet per disabilitare la modalità svuotamento:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>È necessario eseguire questo comando per ogni host sessione a cui si applica l'impostazione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul portale di Azure desktop virtuale Windows, vedere [le esercitazioni.](create-host-pools-azure-marketplace.md) Se si ha già familiarità con le nozioni di base, vedere alcune delle altre funzionalità che è possibile usare con portale di Azure, ad esempio montaggio app MSIX [e](app-attach-azure-portal.md) [Azure Advisor](azure-advisor.md).

Se si usa il metodo PowerShell e si vuole vedere cos'altro può fare il modulo, vedere Configurare il modulo [PowerShell](powershell-module.md) per Desktop virtuale Windows e le informazioni di [riferimento su PowerShell.](/powershell/module/az.desktopvirtualization/)