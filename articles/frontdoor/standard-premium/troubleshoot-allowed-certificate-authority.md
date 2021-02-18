---
title: Autorità di certificazione consentite per Azure front door standard/Premium (anteprima)
description: Questo articolo elenca tutte le autorità di certificazione consentite quando si crea un certificato personalizzato.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: troubleshooting
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 35c1e4f6c700333c72b97289cda1772335037211
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100235"
---
# <a name="allowed-certificate-authorities-for-azure-front-door-standardpremium-preview"></a>Autorità di certificazione consentite per Azure front door standard/Premium (anteprima)

Quando si Abilita la funzionalità HTTPS usando il proprio certificato per un dominio personalizzato di Azure front door standard/Premium. Per creare il certificato TLS/SSL è necessaria un'autorità di certificazione (CA) consentita. In caso contrario, se si usa una CA non consentita o un certificato autofirmato, la richiesta verrà rifiutata.

Le CA seguenti sono consentite per la creazione di un certificato proprio:

- AddTrust External CA Root
- AlphaSSL Root CA
- AME Infra CA 01
- AME Infra CA 02
- Ameroot
- APCA-DM3P
- Atos TrustedRoot 2011
- Autopilot Root CA
- Baltimore CyberTrust Root
- Class 3 Public Primary Certification Authority
- COMODO RSA Certification Authority
- COMODO RSA Domain Validation Secure Server CA
- D-TRUST Root Class 3 CA 2 2009
- DigiCert Cloud Services CA-1
- DigiCert Global Root CA
- DigiCert Global Root G2
- DigiCert High Assurance CA-3
- DigiCert High Assurance EV Root CA
- DigiCert SHA2 Extended Validation Server CA
- DigiCert SHA2 High Assurance Server CA
- DigiCert SHA2 Secure Server CA
- DST Root CA X3
- D-trust Root Class 3 CA 2 2009
- Encryption Everywhere DV TLS CA
- Entrust Root Certification Authority
- Entrust Root Certification Authority - G2
- Entrust.net Certification Authority (2048)
- GeoTrust Global CA
- GeoTrust Primary Certification Authority
- GeoTrust Primary Certification Authority - G2
- Geotrust RSA CA 2018
- GlobalSign
- GlobalSign Extended Validation CA - SHA256 - G2
- GlobalSign Organization Validation CA - G2
- GlobalSign Root CA
- Go Daddy Root Certificate Authority - G2
- Go Daddy Secure Certificate Authority - G2
- Crittografare l'autorità X3
- Medi e-Szigno CA radice 2009
- QuoVadis Root CA2 G3
- RapidSSL RSA CA 2018
- Comunicazione di sicurezza RootCA1
- Comunicazione di sicurezza RootCA2
- Comunicazione di sicurezza RootCA3
- Symantec Class 3 EV SSL CA - G3
- Symantec Class 3 Secure Server CA - G4
- Symantec Enterprise Mobile Root for Microsoft
- Thawte Primary Root CA
- Thawte Primary Root CA - G2
- Thawte Primary Root CA - G3
- Thawte RSA CA 2018
- Thawte Timestamping CA
- TrustAsia TLS RSA CA
- VeriSign Class 3 Extended Validation SSL CA
- VeriSign Class 3 Extended Validation SSL SGC CA
- VeriSign Class 3 Public Primary Certification Authority - G5
- VeriSign International Server CA - Class 3
- VeriSign Time Stamping Service Root
- VeriSign Universal Root Certification Authority
