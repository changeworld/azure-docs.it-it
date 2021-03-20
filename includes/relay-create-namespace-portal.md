---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 500db9f6d299ea81b1f1b1be864df5d91ba1eae7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "76021219"
---
1. Accedere al [portale di Azure][Azure portal].
1. Selezionare **Crea una risorsa**. Selezionare quindi **Integration**  >  **Relay**. Se **Inoltro** non è visibile nell'elenco, selezionare **Visualizza tutto** nell'angolo superiore destro.
1. Selezionare **Crea** e immettere un nome per lo spazio dei nomi nel campo **Nome**. Verrà eseguito un controllo sulla disponibilità del nome.
1. Scegliere una sottoscrizione di Azure in cui creare lo spazio dei nomi.
1. Per [Gruppo di risorse](../articles/azure-resource-manager/management/manage-resource-groups-portal.md) scegliere un gruppo di risorse esistente in cui inserire lo spazio dei nomi oppure crearne uno nuovo.  
1. Selezionare il paese o l'area in cui deve essere ospitato lo spazio dei nomi.

    ![Crea spazio dei nomi][create-namespace]

1. Selezionare **Crea**. Lo spazio dei nomi viene creato e abilitato nel portale di Azure. Dopo alcuni minuti, il sistema effettua il provisioning delle risorse per l'account.

### <a name="get-management-credentials"></a>Ottenere le credenziali di gestione

1. Selezionare **Tutte le risorse** e quindi selezionare il nome dello spazio dei nomi appena creato.
1. Selezionare **Criteri di accesso condiviso**.  
1. In **Criteri di accesso condiviso** selezionare **RootManageSharedAccessKey**.
1. In **criteri SAS: RootManageSharedAccessKey** selezionare il pulsante **copia** accanto a **stringa di connessione primaria**. La stringa di connessione viene copiata negli Appunti per un uso successivo. Incollare questo valore nel Blocco note o in un'altra posizione temporanea.
1. Ripetere il passaggio precedente per copiare e incollare il valore di **Chiave primaria** in un percorso temporaneo per usarlo in seguito.  

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace-vs2019.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string-vs2019.png
[Azure portal]: https://portal.azure.com
