---
title: Assegnare la licenza per la reimpostazione della password self-service - Azure Active Directory
description: Informazioni sulle differenze Azure Active Directory requisiti di licenza per la reimpostazione della password self-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d332c831cc764c61a4672ea5ad1db231b68e106
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "104952372"
---
# <a name="licensing-requirements-for-azure-active-directory-self-service-password-reset"></a>Requisiti di licenza per la reimpostazione della password self-service Azure Active Directory

Per ridurre le chiamate a help desk e la perdita di produttività quando un utente non riesce ad accedere al dispositivo o a un'applicazione, è possibile abilitare gli account utente in Azure Active Directory (Azure AD) per la reimpostazione della password self-service (SSPR). Le funzionalità che costituiscono SSPR includono modifiche della password, reimpostazione, sblocco e writeback in una directory locale. Le funzionalità di base di SSPR sono disponibili in Microsoft 365 Business standard o versione successiva e tutti gli SKU di Azure AD Premium senza costi aggiuntivi.

Questo articolo illustra in dettaglio i diversi modi in cui la reimpostazione della password self-service può essere concessa in licenza e usata. Per dettagli specifici su prezzi e fatturazione, vedere la [pagina](https://azure.microsoft.com/pricing/details/active-directory/)relativa ai prezzi di Azure ad.

## <a name="compare-editions-and-features"></a>Confrontare le edizioni e le funzionalità

SSPR richiede una licenza solo per il tenant. 

La tabella seguente descrive i diversi scenari di SSPR per la modifica della password, la reimpostazione o il writeback locale e quali SKU forniscono la funzionalità.

| Funzionalità | Azure AD Free | Microsoft 365 Business Standard | Microsoft 365 Business Premium | Azure AD Premium P1 o P2 |
| --- |:---:|:---:|:---:|:---:|
| **Modifica della password utente solo cloud**<br />Quando un utente in Azure AD conosce la password e vuole modificarla in un nuovo elemento. | ● | ● | ● | ● |
| **Reimpostazione della password utente solo cloud**<br />Quando un utente in Azure AD ha dimenticato la password ed è necessario reimpostarla. | | ● | ● | ● |
| **Modifica o reimpostazione della password di un utente ibrido con writeback locale**<br />Quando un utente in Azure AD sincronizzato da una directory locale con Azure AD Connect desidera modificare o reimpostare la password e scrivere nuovamente la nuova password in locale. | | | ● | ● |

> [!WARNING]
> I piani di licenza autonomi Microsoft 365 Basic e standard non supportano SSPR con writeback locale. La funzionalità di writeback locale richiede Azure AD Premium P1, Premium P2 o Microsoft 365 Business Premium.

Per ulteriori informazioni sulle licenze, inclusi i costi, vedere le pagine seguenti:

* [Prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Caratteristiche e funzionalità di Azure Active Directory](https://www.microsoft.com/cloud-platform/azure-active-directory-features)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/microsoft-365/enterprise)
* [Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description)

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare SSPR, completare l'esercitazione seguente:

> [!div class="nextstepaction"]
> [Esercitazione: abilitazione della reimpostazione della password self-service (SSPR)](tutorial-enable-sspr.md)