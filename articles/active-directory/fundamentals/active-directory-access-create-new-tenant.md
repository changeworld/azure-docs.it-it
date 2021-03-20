---
title: Avvio rapido - Accedere e creare un nuovo tenant - Azure AD
description: Istruzioni per trovare Azure Active Directory e creare un nuovo tenant per l'organizzazione.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeea88d8c21ba754fbeadbb24891126b639616c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96437241"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Guida introduttiva: Creare un nuovo tenant in Azure Active Directory
Nel portale di Azure Active Directory (Azure AD) è possibile eseguire tutte le attività amministrative, inclusa la creazione di un nuovo tenant per l'organizzazione. 

In questa guida introduttiva si apprenderà come accedere al portale di Azure e ad Azure Active Directory e come creare un tenant di base per l'organizzazione.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-a-new-tenant-for-your-organization"></a>Creare un nuovo tenant per l'organizzazione
Dopo aver eseguito l'accesso al portale di Azure, è possibile creare un nuovo tenant per l'organizzazione. Il nuovo tenant rappresenta l'organizzazione e consente di gestire un'istanza specifica dei servizi cloud Microsoft per gli utenti interni ed esterni.

### <a name="to-create-a-new-tenant"></a>Per creare un nuovo tenant

1. Accedere al [portale di Azure](https://portal.azure.com/) dell'organizzazione.

1. Nel menu del portale di Azure selezionare **Azure Active Directory**.  

    <kbd>![Azure Active Directory - Pagina di panoramica - Crea un tenant](media/active-directory-access-create-new-tenant/azure-ad-portal.png)</kbd>  

1. Selezionare **Crea un tenant**.

1. Nella scheda Informazioni di base selezionare il tipo di tenant da creare, ovvero **Azure Active Directory** oppure **Azure Active Directory (B2C)** .

1. Selezionare **Avanti: Configurazione** per passare alla scheda Configurazione.

    <kbd>![Azure Active Directory - pagina Crea un tenant - scheda Configurazione ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)</kbd>

1.  Nella scheda Configurazione immettere le informazioni seguenti:
    
    - Digitare _Contoso Organization_ nella casella **Nome organizzazione**.

    - Digitare _Contosoorg_ nella casella **Nome di dominio iniziale**.

    - Nella casella **Paese o area geografica** lasciare l'opzione _Stati Uniti_.

1. Selezionare **Avanti: Rivedi e crea**. Esaminare le informazioni immesse e, se sono corrette, selezionare **Crea**.

    <kbd>![Azure Active Directory - pagina Rivedi e crea per il tenant](media/active-directory-access-create-new-tenant/azure-ad-review.png)</kbd>

Viene creato il nuovo tenant con il dominio contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Pulire le risorse
Se non si intende continuare a usare questa applicazione, è possibile eliminare il tenant seguendo questa procedura:

- Assicurarsi di aver eseguito l'accesso alla directory da eliminare tramite il filtro **Directory e sottoscrizione** nel portale di Azure e passare alla directory di destinazione, se necessario.
- Selezionare **Azure Active Directory** e, nella pagina **Contoso - Panoramica**, selezionare **Elimina directory**.

    Viene eliminato il tenant, insieme alle informazioni associate.

    <kbd>![Pagina Panoramica, con il pulsante Elimina directory evidenziato](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)</kbd>

## <a name="next-steps"></a>Passaggi successivi
- Modificare o aggiungere altri nomi di dominio: vedere [Aggiungere un nome di dominio personalizzato ad Azure Active Directory](add-custom-domain.md)

- Aggiungere utenti: vedere [Aggiungere o eliminare un nuovo utente](add-users-azure-active-directory.md)

- Aggiungere gruppi e membri: vedere [Creare un gruppo di base e aggiungere membri](active-directory-groups-create-azure-portal.md)

- Acquisire altre informazioni sull'[accesso in base al ruolo con Privileged Identity Management](../../role-based-access-control/best-practices.md) e sull'[accesso condizionale](../../role-based-access-control/conditional-access-azure-management.md) per gestire l'accesso alle risorse e alle applicazioni dell'organizzazione.

- Acquisire altre informazioni su Azure AD, tra cui [informazioni di base sulle licenze, sulla terminologia e sulle funzionalità associate](active-directory-whatis.md).
