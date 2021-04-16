---
title: Creare un nuovo pacchetto di accesso nella gestione entitlement - Azure AD
description: Informazioni su come creare un nuovo pacchetto di accesso di risorse da condividere in Azure Active Directory entitlement.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb0312d905284f8c5a9817e9550d340bf6135032
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532202"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Creare un nuovo pacchetto di accesso nella Azure AD entitlement

Un pacchetto di accesso consente di eseguire una configurazione una sola volta di risorse e criteri che amministrano automaticamente l'accesso per tutta la durata del pacchetto di accesso. Questo articolo descrive come creare un nuovo pacchetto di accesso.

## <a name="overview"></a>Panoramica

Tutti i pacchetti di accesso devono essere inseriti in un contenitore denominato catalogo. Un catalogo definisce le risorse che è possibile aggiungere al pacchetto di accesso. Se non si specifica un catalogo, il pacchetto di accesso verrà inserito nel catalogo Generale. Attualmente non è possibile spostare un pacchetto di accesso esistente in un catalogo diverso.

Se si è un gestore di pacchetti di accesso, non è possibile aggiungere le risorse di cui si è proprietari a un catalogo. Si è limitati all'uso delle risorse disponibili nel catalogo. Se è necessario aggiungere risorse a un catalogo, è possibile chiedere al proprietario del catalogo.

Tutti i pacchetti di accesso devono avere almeno un criterio. I criteri specificano chi può richiedere il pacchetto di accesso e anche le impostazioni di approvazione e ciclo di vita. Quando si crea un nuovo pacchetto di accesso, è possibile creare un criterio iniziale per gli utenti nella directory, per gli utenti non presenti nella directory, solo per le assegnazioni dirette dell'amministratore oppure è possibile scegliere di creare il criterio in un secondo momento.

![Creare un pacchetto di accesso](./media/entitlement-management-access-package-create/access-package-create.png)

Ecco i passaggi di alto livello per creare un nuovo pacchetto di accesso.

1. In Governance delle identità avviare il processo per creare un nuovo pacchetto di accesso.

1. Selezionare il catalogo in cui si vuole creare il pacchetto di accesso.

1. Aggiungere risorse dal catalogo al pacchetto di accesso.

1. Assegnare ruoli di risorsa per ogni risorsa.

1. Specificare gli utenti che possono richiedere l'accesso.

1. Specificare le impostazioni di approvazione.

1. Specificare le impostazioni del ciclo di vita.

## <a name="start-new-access-package"></a>Avviare un nuovo pacchetto di accesso

**Ruolo prerequisito:** amministratore globale, amministratore utenti, proprietario del catalogo o responsabile dei pacchetti di accesso

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Fare clic su **Azure Active Directory** e quindi su **Identity Governance**.

1. Nel menu a sinistra fare clic su **Pacchetti di accesso**.

1. Fare clic su **Nuovo pacchetto di accesso**.
   
    ![Gestione entitlement nel portale di Azure](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Operazioni di base

Nella scheda **Informazioni di** base assegnare un nome al pacchetto di accesso e specificare il catalogo in cui creare il pacchetto di accesso.

1. Immettere un nome visualizzato e una descrizione per il pacchetto di accesso. Gli utenti visualizzano queste informazioni quando inviano una richiesta per il pacchetto di accesso.

1. **Nell'elenco a** discesa Catalogo selezionare il catalogo in cui si vuole creare il pacchetto di accesso. Ad esempio, si potrebbe avere un proprietario del catalogo che gestisce tutte le risorse di marketing che possono essere richieste. In questo caso, è possibile selezionare il catalogo marketing.

    Verranno visualizzati solo i cataloghi in cui si è autorizzati a creare pacchetti di accesso. Per creare un pacchetto di accesso in un catalogo esistente, è necessario essere un amministratore globale o un amministratore utente oppure essere un proprietario del catalogo o accedere a Gestione pacchetti in tale catalogo.

    ![Pacchetto di accesso - Nozioni di base](./media/entitlement-management-access-package-create/basics.png)

    Se si è un amministratore globale, un amministratore utente o un autore del catalogo e si vuole creare il pacchetto di accesso in un nuovo catalogo non elencato, fare clic su **Crea nuovo catalogo**. Immettere il nome e la descrizione del catalogo e quindi fare clic su **Crea**.

    Il pacchetto di accesso che si sta creando e tutte le risorse in esso incluse verranno aggiunte al nuovo catalogo. È anche possibile aggiungere altri proprietari del catalogo in un secondo momento.

1. Fare clic su **Avanti**.

## <a name="resource-roles"></a>Ruoli delle risorse

Nella scheda **Ruoli risorsa** selezionare le risorse da includere nel pacchetto di accesso. Gli utenti che richiedono e ricevono il pacchetto di accesso riceveranno tutti i ruoli delle risorse nel pacchetto di accesso.

1. Fare clic sul tipo di risorsa da aggiungere (**Gruppi e team**, **Applicazioni** o siti **di SharePoint**).

1. Nel riquadro Seleziona visualizzato selezionare una o più risorse dall'elenco.

    ![Pacchetto di accesso - Ruoli delle risorse](./media/entitlement-management-access-package-create/resource-roles.png)

    Se si crea il pacchetto di accesso nel catalogo Generale o in un nuovo catalogo, sarà possibile selezionare qualsiasi risorsa dalla directory di cui si è proprietari. È necessario essere almeno un amministratore globale, un amministratore utente o un autore del catalogo.

    Se si crea il pacchetto di accesso in un catalogo esistente, è possibile selezionare qualsiasi risorsa già presente nel catalogo senza essere proprietaria.

    Se si è un amministratore globale, un amministratore utente o un proprietario del catalogo, è possibile selezionare le risorse di cui si è proprietari che non sono ancora presenti nel catalogo. Se si selezionano risorse non attualmente presenti nel catalogo selezionato, queste risorse verranno aggiunte al catalogo anche per gli altri amministratori del catalogo con cui compilare pacchetti di accesso. Per visualizzare tutte le risorse che è possibile  aggiungere al catalogo, selezionare la casella di controllo Visualizza tutto nella parte superiore del riquadro Seleziona. Se si desidera selezionare solo le risorse attualmente presenti nel catalogo selezionato, lasciare deselezionata la casella di controllo **Visualizza** tutto (stato predefinito).

1. Dopo aver selezionato le risorse, **nell'elenco Ruolo** selezionare il ruolo che si vuole assegnare agli utenti per la risorsa.

    ![Pacchetto di accesso - Selezione del ruolo della risorsa](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Fare clic su **Avanti**.

>[!NOTE]
>È possibile aggiungere gruppi dinamici a un catalogo e a un pacchetto di accesso. Tuttavia, sarà possibile selezionare solo il ruolo Proprietario quando si gestisce una risorsa gruppo dinamico in un pacchetto di accesso.

## <a name="requests"></a>Requests

Nella scheda **Richieste** creare il primo criterio per specificare chi può richiedere il pacchetto di accesso e anche le impostazioni di approvazione. Successivamente, è possibile creare altri criteri di richiesta per consentire ad altri gruppi di utenti di richiedere il pacchetto di accesso con le proprie impostazioni di approvazione.

![Pacchetto di accesso - scheda Richieste](./media/entitlement-management-access-package-create/requests.png)

A seconda di chi si vuole poter richiedere questo pacchetto di accesso, seguire questa procedura in una delle sezioni seguenti.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Rivedi e crea

Nella scheda **Rivedi e crea** è possibile esaminare le impostazioni e verificare la presenza di eventuali errori di convalida.

1. Esaminare le impostazioni del pacchetto di accesso

    ![Pacchetto di accesso - Abilitare l'impostazione dei criteri](./media/entitlement-management-access-package-create/review-create.png)

1. Fare **clic su** Crea per creare il pacchetto di accesso.

    Il nuovo pacchetto di accesso viene visualizzato nell'elenco dei pacchetti di accesso.

## <a name="creating-an-access-package-programmatically"></a>Creazione di un pacchetto di accesso a livello di codice

È anche possibile creare un pacchetto di accesso usando Microsoft Graph.  Un utente con un ruolo appropriato con un'applicazione con l'autorizzazione `EntitlementManagement.ReadWrite.All` delegata può chiamare l'API a

1. [Elencare accessPackageResources nel catalogo e](/graph/api/accesspackagecatalog-list?tabs=http&view=graph-rest-beta&preserve-view=true) [creare un oggetto accessPackageResourceRequest](/graph/api/accesspackageresourcerequest-post?tabs=http&view=graph-rest-beta&preserve-view=true) per tutte le risorse non ancora presenti nel catalogo.
1. [Elenca gli accessPackageResourceRoles di](/graph/api/accesspackage-list-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta&preserve-view=true) ogni accessPackageResource in un accessPackageCatalog. Questo elenco di ruoli verrà quindi usato per selezionare un ruolo, quando successivamente si crea un accessPackageResourceRoleScope.
1. [Creare un accessPackage](/graph/tutorial-access-package-api&view=graph-rest-beta&preserve-view=true).
1. [Creare un oggetto accessPackageAssignmentPolicy.](/graph/api/accesspackageassignmentpolicy-post?tabs=http&view=graph-rest-beta&preserve-view=true)
1. [Creare un accessPackageResourceRoleScope per](/graph/api/accesspackage-post-accesspackageresourcerolescopes?tabs=http&view=graph-rest-beta&preserve-view=true) ogni ruolo di risorsa necessario nel pacchetto di accesso.

## <a name="next-steps"></a>Passaggi successivi

- [Collegamento di condivisione per richiedere un pacchetto di accesso](entitlement-management-access-package-settings.md)
- [Modificare i ruoli delle risorse per un pacchetto di accesso](entitlement-management-access-package-resources.md)
- [Assegnare direttamente un utente al pacchetto di accesso](entitlement-management-access-package-assignments.md)
- [Creare una verifica di accesso per un pacchetto di accesso](entitlement-management-access-reviews-create.md)
