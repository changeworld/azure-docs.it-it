---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 27a4d775b8d88e02be69655e5adfc6155f867ef6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96582173"
---
Un certificato SSL appropriato garantisce che vengano inviate informazioni crittografate al server appropriato. Oltre alla crittografia, il certificato consente anche l'autenticazione. È possibile caricare il proprio certificato SSL attendibile tramite l'interfaccia di PowerShell del dispositivo.

1. [Connettersi all'interfaccia di PowerShell](#connect-to-the-powershell-interface).
2. Usare il `Set-HcsCertificate` cmdlet per caricare il certificato. Quando richiesto, specificare i parametri seguenti:

   - `CertificateFilePath` : Percorso della condivisione che contiene il file del certificato in formato *PFX* .
   - `CertificatePassword` : Password usata per proteggere il certificato.
   - `Credentials` -Username per accedere alla condivisione che contiene il certificato. Quando richiesto, fornire la password alla condivisione di rete.

     Nell'esempio seguente viene illustrato l'utilizzo di questo cmdlet:

     ```
     Set-HcsCertificate -Scope LocalWebUI -CertificateFilePath "\\myfileshare\certificates\mycert.pfx" -CertificatePassword "mypassword" -Credential "Username"
     ```
