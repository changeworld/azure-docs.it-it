---
title: Rifiutare esplicitamente le credenziali Azure Active Directory verificabili (anteprima)
description: Informazioni su come rifiutare esplicitamente l'anteprima delle credenziali verificabili
documentationCenter: ''
author: barclayn
manager: daveba
ms.service: identity
ms.topic: how-to
ms.subservice: verifiable-credentials
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: d6e72b6d6f566fcf3f52e1c48ab6824c0e9a968e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222793"
---
# <a name="opt-out-of-the-verifiable-credentials-preview"></a>Rifiutare esplicitamente le credenziali verificabili (anteprima)

Contenuto dell'articolo:

- Il motivo per cui potrebbe essere necessario rifiutare esplicitamente.
- Passaggi necessari.
- Cosa accade ai dati?
- Effetto sulle credenziali verificabili esistenti.

> [!IMPORTANT]
> Azure Active Directory credenziali verificabili è attualmente disponibile in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Prerequisiti

- Caricamento completo delle credenziali verificabili.

## <a name="potential-reasons-for-opting-out"></a>Possibili motivi per la disattivazione

Al momento, non è possibile apportare modifiche alle informazioni sul dominio. Di conseguenza, se si commette un errore o si decide di apportare una modifica, non sono disponibili altre opzioni oltre a rifiutare e a riavviare.

## <a name="the-steps-required"></a>Passaggi necessari

1. Dalla portale di Azure cercare le credenziali verificabili.
2. Scegliere **Impostazioni** dal menu a sinistra.
3. Nella sezione **ripristinare l'organizzazione**, selezionare **Elimina tutte le credenziali e rifiutare esplicitamente l'anteprima**.

   ![impostazione Reimposta organizzazione](media/how-to-opt-out/settings-reset.png)

4. Leggere il messaggio di avviso e per continuare selezionare **Elimina ed** escludere.

   ![impostazioni eliminare e rifiutare esplicitamente](media/how-to-opt-out/delete-and-opt-out.png)

Si è scelto di escludere l'anteprima delle credenziali verificabili. Continua a leggere per capire cosa accade dietro le quinte.

## <a name="what-happens-to-your-data"></a>Cosa accade ai dati?

Quando si sceglie di rifiutare esplicitamente il Azure Active Directory servizio di credenziali verificabili, verranno eseguite le azioni seguenti:

- Le chiavi in Key Vault verranno [eliminate temporaneamente](../../key-vault/general/soft-delete-overview.md).
- L'oggetto emittente verrà eliminato dal database.
- Il tenant identificatore verrà eliminato dal database. 
- Tutti gli oggetti Contract verranno eliminati dal database.

Quando si verifica un rifiuto esplicito, non sarà possibile ripristinare il fatto o eseguire operazioni sul fatto. Questo passaggio è un'operazione unidirezionale ed è necessario acconsentire di nuovo. ciò comporta la creazione di un nuovo oggetto.  

## <a name="effect-on-existing-verifiable-credentials"></a>Effetti sulle credenziali verificabili esistenti

Tutte le credenziali verificabili già eseguite continueranno a esistere. Non verranno invalidati in modo crittografico perché il fatto rimarrà risolvibile tramite ION.
Tuttavia, quando le relying party chiamano l'API di stato, riceveranno sempre un messaggio di errore.

## <a name="next-steps"></a>Passaggi successivi

- Configurare credenziali verificabili sul tenant di [Azure](get-started-verifiable-credentials.md)