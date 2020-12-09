---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/04/2020
ms.author: inhenkel
ms.custom: portal
ms.openlocfilehash: c3a27218f03980e9b42a4a56fa739203c43754a0
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "96013276"
---
<!--Set the encryption on storage account in the portal-->

## <a name="set-the-encryption-on-a-storage-account"></a>Impostare la crittografia in un account di archiviazione

1. Nel portale di Azure, immettere il nome dell'account di archiviazione da crittografare nel campo **Cerca** nella parte superiore della schermata.  Le corrispondenze verranno visualizzate sotto il campo di ricerca.
1. Selezionare l'account di archiviazione che si sta cercando. Verrà visualizzata la schermata dell'account di archiviazione.
1. Selezionare **Crittografia**.
1. Selezionare Chiavi gestite da Microsoft o Chiavi gestite dal cliente.

### <a name="use-microsoft-managed-keys"></a>Usare le chiavi gestite da Microsoft

Per impostazione predefinita, i dati nell'account di archiviazione vengono crittografati usando le chiavi gestite da Microsoft.

### <a name="use-customer-managed-keys"></a>Usare chiavi gestite dal cliente

1. Selezionare **Chiavi gestite dal cliente**.
1. Selezionare **Immettere l'URI della chiave** o **Selezionare dall'insieme di credenziali delle chiavi**.
    1. Se si seleziona **Immettere l'URI della chiave**, immettere l'URI della chiave nel campo URI chiave e selezionare la sottoscrizione (che potrebbe essere già selezionata).
    1. Se si seleziona **Selezionare dall'insieme di credenziali delle chiavi**, selezionare quindi **Selezionare l'insieme di credenziali delle chiavi e la chiave**. Viene visualizzata la schermata Selezionare chiave da Azure Key Vault.
1. Selezionare l'**insieme di credenziali delle chiavi** che si vuole usare e selezionare una chiave già presente nell'insieme di credenziali delle chiavi o **creare una nuova chiave**.
    1. Se si sceglie di creare una nuova chiave, selezionare **Genera** o **Importa** nel menu a discesa **Opzioni**. È possibile importare solo le chiavi RSA.
    1. Per generare una nuova chiave, assegnare un nome alla chiave nel campo **Nome**, quindi selezionare il tipo di chiave:
        1. RSA e le dimensioni della chiave:  2048, 3072 o 4096. Questo è la scelta più popolare tra i clienti.
        1. EC - Nomi curva ellittica: P-256, P-384, P-521 o P-256K
        1. Facoltativamente, è possibile impostare le date di attivazione e di scadenza della chiave.
        1. Selezionare **Sì** per abilitare la rotazione automatica delle chiavi.
        1. Selezionare **Crea**.
    1. Per importare una chiave, selezionare il file da caricare facendo clic in un punto qualsiasi del campo **Selezionare un file**.
        1. Specificare un nome alla chiave nel campo **Nome**.
        1. Facoltativamente, è possibile impostare le date di attivazione e di scadenza della chiave.
        1. Selezionare **Sì** per abilitare la rotazione automatica delle chiavi.
        1. Selezionare **Crea**.
    1. Scegliere **Selezionare** per selezionare questa chiave per crittografare l'account di archiviazione. Verrà visualizzata di nuovo la schermata Crittografia.
1. **IMPORTANTE** Selezionare **Salva** per salvare le impostazioni di crittografia o qualsiasi configurazione andrà persa.
