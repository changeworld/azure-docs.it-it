---
title: Assegnare un Azure Key Vault di accesso remoto (portale)
description: Come usare il portale di Azure per assegnare un criterio Key Vault di accesso a un'entità di sicurezza o a un'identità dell'applicazione.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 443b269e7155fc206ee50e7907a7acded2c22f53
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751491"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Assegnare criteri di accesso dell'insieme di credenziali delle chiavi tramite il portale di Azure

Un Key Vault di accesso remoto determina se un'entità di sicurezza specifica, ad esempio un utente, [](../keys/index.yml)un'applicazione o un gruppo di utenti, può eseguire operazioni diverse su [segreti,](../secrets/index.yml)chiavi e certificati di [Key Vault.](../certificates/index.yml) È possibile assegnare criteri di accesso usando il portale di Azure (questo articolo), l'interfaccia della riga di comando di [Azure](assign-access-policy-cli.md) [o Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Per altre informazioni sulla creazione di gruppi in Azure Active Directory tramite portale di Azure, vedere Creare un gruppo [di base e aggiungere membri](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)

## <a name="assign-an-access-policy"></a>Assegnare un criterio di accesso

1.  Nel [portale di Azure](https://portal.azure.com)passare alla risorsa Key Vault locale. 

1.  In **Impostazioni selezionare** Criteri di **accesso** e quindi Aggiungi criteri **di accesso:**

    ![Selezionare Criteri di accesso, quindi Aggiungi assegnazione di ruolo](../media/authentication/assign-policy-portal-01.png)

1.  Selezionare le autorizzazioni desiderate in **Autorizzazioni certificato**, **Autorizzazioni chiave** e Autorizzazioni **segrete**. È anche possibile selezionare un modello che contiene combinazioni di autorizzazioni comuni:

    ![Specifica delle autorizzazioni per i criteri di accesso](../media/authentication/assign-policy-portal-02.png)

1. In **Seleziona entità** scegliere il collegamento Nessuno **selezionato** per aprire il riquadro **di** selezione Entità. Immettere il nome dell'utente, dell'app o dell'entità servizio nel campo di ricerca, selezionare il risultato appropriato, quindi scegliere **Seleziona**.

    ![Selezione dell'entità di sicurezza per i criteri di accesso](../media/authentication/assign-policy-portal-03.png)

    Se si usa un'identità gestita per l'app, cercare e selezionare il nome dell'app stessa. Per altre informazioni sull'identità gestita e sulle entità servizio, vedere autenticazione [Key Vault- identità dell'app](authentication.md#app-identity-and-security-principals)ed entità servizio.
 
1.  Nel riquadro **Aggiungi criteri di accesso** selezionare Aggiungi per salvare i criteri di accesso. 

    ![Aggiunta dei criteri di accesso con l'entità di sicurezza assegnata](../media/authentication/assign-policy-portal-04.png)

1. Nella pagina **Criteri di accesso** verificare che i criteri di accesso siano elencati in Criteri di accesso **correnti** e quindi selezionare **Salva**. I criteri di accesso non vengono applicati fino a quando non vengono salvate.

    ![Salvataggio delle modifiche ai criteri di accesso](../media/authentication/assign-policy-portal-05.png)


## <a name="next-steps"></a>Passaggi successivi

- [Sicurezza di Azure Key Vault: Gestione delle identità e dell'accesso](security-overview.md#identity-management)
- [Proteggere l'insieme di credenziali delle chiavi.](security-overview.md)
- [Guida per gli sviluppatori per Azure Key Vault](developers-guide.md)