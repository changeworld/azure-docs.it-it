---
title: Archiviazione dei dati di identità per i clienti australiani e della Nuova Zelanda - Azure AD
description: Informazioni su dove Azure Active Directory i dati relativi all'identità per i clienti australiani e della Nuova Zelanda.
services: active-directory
author: ajburnle
manager: daveba
ms.author: ajburnle
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4bb095e93a3728835e26cbe283f79569c91b7487
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479067"
---
# <a name="identity-data-storage-for-australian-and-new-zealand-customers-in-azure-active-directory"></a>Archiviazione dei dati di identità per i clienti australiani e della Nuova Zelanda in Azure Active Directory

I dati di identità vengono archiviati Azure AD in una posizione geografica in base all'indirizzo fornito dall'organizzazione durante la sottoscrizione di un servizio Microsoft Online, ad esempio Microsoft 365 e Azure. Per informazioni sulla posizione in cui sono archiviati i dati dei clienti delle identità, è possibile usare la sezione [Where is your data located?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) (Dove si trovano i dati) del Microsoft Trust Center.

> [!NOTE]
> I servizi e le applicazioni che si integrano con Azure AD hanno accesso ai dati dei clienti di identità. Valutare ogni servizio e applicazione in uso per determinare il modo in cui i dati dei clienti di identità vengono elaborati dal servizio e dall'applicazione specifici e se soddisfano i requisiti di archiviazione dei dati aziendali. Per altre informazioni sulla residenza dei dati dei servizi Microsoft, vedere la sezione Where is your data located? (Percorso di archiviazione dei dati) di Microsoft Trust Center.

Per i clienti che hanno fornito un indirizzo in Australia o Nuova Zelanda, Azure AD i dati di identità per questi servizi all'interno dei data center australiani: 
- Azure AD gestione directory 
- Autenticazione

Tutti gli altri Azure AD archiviano i dati dei clienti in data center globali. Per individuare il data center per un servizio, [vedere Azure Active Directory : Dove si trovano i dati?](https://aka.ms/AADDataMap)

## <a name="microsoft-azure-ad-multi-factor-authentication-mfa"></a>Microsoft Azure AD Multi-Factor Authentication (MFA)

MFA archivia i dati dei clienti delle identità nei data center globali. Per altre informazioni sulle informazioni utente raccolte e archiviate da Azure AD MFA e server Azure MFA basate sul cloud, vedere Raccolta di dati utente di [Azure Multi-Factor Authentication.](../authentication/concept-mfa-data-residency.md)

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle caratteristiche e le funzionalità descritte in precedenza, vedere questi articoli:
- [Informazioni su Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)
