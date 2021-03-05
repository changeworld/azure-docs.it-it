---
title: Accesso ibrido sicuro di Azure AD s| Microsoft Docs
description: Questo articolo descrive le soluzioni dei partner per l'integrazione di applicazioni legacy locali, nel cloud pubblico o nel cloud privato con Azure AD. Proteggi le app legacy connettendo le reti e i controller di distribuzione delle app ad Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 2/16/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06ed37b914c352d7162db7f8409ac09b7615da47
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174428"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Accesso ibrido sicuro: Proteggi le app legacy con Azure Active Directory

È ora possibile proteggere le applicazioni locali e di autenticazione legacy del cloud connettendosi a Azure Active Directory (AD) con:

- [Proxy applicazione Azure AD](#secure-hybrid-access-sha-through-azure-ad-application-proxy)

- [Controller e reti per la distribuzione di applicazioni esistenti](#sha-through-networking-and-delivery-controllers)

- [Applicazioni di rete privata virtuale (VPN) e Software-Defined perimetrale (SDP)](#sha-through-vpn-and-sdp-applications)

È possibile colmare il gap e rafforzare il comportamento di sicurezza in tutte le applicazioni con funzionalità Azure AD come Azure AD [l'accesso condizionale](../conditional-access/overview.md) e Azure ad [Identity Protection](../identity-protection/overview-identity-protection.md).

## <a name="secure-hybrid-access-sha-through-azure-ad-application-proxy"></a>SHA (Secure Hybrid Access) tramite il proxy di applicazione Azure AD
  
Con il [proxy di applicazione](./what-is-application-proxy.md) è possibile fornire [l'accesso remoto sicuro](./application-proxy.md) alle applicazioni Web locali. Gli utenti non richiedono l'uso di una VPN. Gli utenti traggono vantaggio grazie alla connessione semplificata alle applicazioni da qualsiasi dispositivo dopo un [Single Sign-on](./add-application-portal-setup-sso.md). Il proxy di applicazione fornisce accesso remoto come servizio e consente di [pubblicare facilmente le applicazioni locali](./application-proxy-add-on-premises-application.md) a utenti esterni alla rete aziendale. Consente di ridimensionare la gestione dell'accesso al cloud senza che sia necessario modificare le applicazioni locali. [Pianificare un Azure ad distribuzione del proxy di applicazione](./application-proxy-deployment-plan.md) come passaggio successivo.

## <a name="azure-ad-partner-integrations"></a>Integrazioni di partner Azure AD

### <a name="sha-through-networking-and-delivery-controllers"></a>SHA tramite i controller di rete e di recapito

Oltre a [Azure ad proxy di applicazione](./what-is-application-proxy.md), per consentire all'utente di utilizzare il [Framework zero Trust](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/), partner Microsoft con provider di terze parti. È possibile utilizzare i controller di rete e di distribuzione esistenti e proteggere con facilità le applicazioni legacy cruciali per i processi aziendali, ma che non è stato possibile proteggere prima con Azure AD. È probabile che siano già presenti tutti gli elementi necessari per iniziare a proteggere le applicazioni.

![Immagine mostra l'accesso ibrido sicuro con i partner di rete e il proxy applicazione](./media/secure-hybrid-access/secure-hybrid-access.png)

I fornitori di reti seguenti offrono soluzioni predefinite e linee guida dettagliate per l'integrazione con Azure AD.

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Citrix Application Delivery Controller (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

- [Gestione traffico virtuale Pulse Secure (VTM)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

### <a name="sha-through-vpn-and-sdp-applications"></a>SHA tramite applicazioni VPN e SDP

Grazie alle soluzioni VPN e SDP è possibile fornire l'accesso sicuro alla rete aziendale da qualsiasi dispositivo, in qualsiasi momento, in qualsiasi posizione, proteggendo al tempo stesso i dati dell'organizzazione. Con Azure AD come provider di identità (IDP), è possibile usare metodi di autenticazione e autorizzazione moderni come Azure AD [Single Sign-on](./what-is-single-sign-on.md) e [l'autenticazione](../authentication/concept-mfa-howitworks.md) a più fattori per proteggere le applicazioni legacy locali.  

![Immagine mostra l'accesso ibrido sicuro con i partner VPN e il proxy app ](./media/secure-hybrid-access/app-proxy-vpn.png)

I fornitori di VPN seguenti offrono soluzioni predefinite e linee guida dettagliate per l'integrazione con Azure AD.

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)

- [Protezione globale di Palo Alto Networks](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

- [Pulse Secure Pulse Connect (PC)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial)

I fornitori SDP seguenti offrono soluzioni predefinite e linee guida dettagliate per l'integrazione con Azure AD.

- [Broker di accesso Datawiza](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Perimeter 81](https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial#:~:text=For%20SSO%20to%20work,%20you%20need%20to%20establish,to%20test%20Azure%20AD%20single%20sign-on%20with%20B.Simon.)

- [Piattaforma di autenticazione Silverfort](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)
