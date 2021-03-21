---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/08/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f3eb2d9469ab3a3d2c1d09e4adc3ee2cb1f86e6e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979071"
---
## <a name="extract-the-zip-file"></a>Estrarre il file zip

Estrarre il file con estensione zip. Il file contiene le cartelle seguenti:

* AzureVPN
* Generico
* OpenVPN (se è stata abilitata la funzionalità OpenVPN con il **certificato di Azure** o le impostazioni di **autenticazione RADIUS** sul gateway). Selezionare l'articolo appropriato che corrisponde alla configurazione per creare un tenant.

  * [Gateway VPN: creare un tenant](../articles/vpn-gateway/openvpn-azure-ad-tenant.md).
  * [Rete WAN virtuale: creare un tenant](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="retrieve-information"></a>Recupera informazioni

Nella cartella **AzureVPN** passare al file **_azurevpnconfig.xml_** e aprirlo con il blocco note. Prendere nota del testo tra i tag seguenti.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Dettagli del profilo

Quando si aggiunge una connessione, è necessario specificare le informazioni raccolte nel passaggio precedente nella pagina dei dettagli del profilo. I campi corrispondono alle informazioni seguenti:

* **Destinatari:** Identifica la risorsa destinatario a cui è destinato il token.
* **Autorità di certificazione:** Identifica il servizio token di sicurezza (STS) che ha emesso il token e il tenant del Azure AD.
* **Tenant:** Contiene un identificatore univoco e non modificabile del tenant di directory che ha emesso il token.
* **FQDN:** Nome di dominio completo (FQDN) nel gateway VPN di Azure.
* **ServerSecret:** Chiave precondivisa del gateway VPN.

## <a name="folder-contents"></a>Contenuto cartelle

* La **cartella generica** contiene il certificato del server pubblico e il file VpnSettings.xml. Il file VpnSettings.xml contiene le informazioni necessarie per configurare un client generico.

* Il file con estensione zip scaricato può contenere anche le cartelle **WindowsAmd64** e **WindowsX86**. Queste cartelle includono il programma di installazione per SSTP e IKEv2 per i client Windows. Per eseguire l'installazione, è necessario disporre dei diritti di amministratore per il client.
