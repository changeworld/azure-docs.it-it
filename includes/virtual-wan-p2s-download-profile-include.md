---
title: includere file
description: File di inclusione
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/05/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: eea8cb61ce98b2394abff6995e5cc89f00a7cf46
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99628878"
---
1. Nella pagina della rete WAN virtuale selezionare **Configurazioni VPN utente**.
1. Nella pagina **configurazioni VPN utente** selezionare una configurazione e quindi fare clic su **Scarica profilo VPN utente rete virtuale**. Quando si Scarica la configurazione a livello di WAN, si ottiene un profilo di VPN utente basato su Traffic Manager predefinito. Per altre informazioni sui profili globali o sui profili basati su hub, vedere [Profili degli hub](../articles/virtual-wan/global-hub-profile.md). Gli scenari di failover sono semplificati con il profilo globale.

   
   Se per qualche motivo un hub non è disponibile, il profilo predefinito di Gestione traffico fornito dal servizio assicura la connettività tramite un hub diverso alle risorse di Azure per gli utenti da punto a sito. È sempre possibile scaricare una configurazione VPN specifica dell'hub passando all'hub. In **VPN utente (da punto a sito)** scaricare il profilo **VPN utente** dell'hub virtuale.
1. Nella pagina **Scarica profilo VPN utente WAN virtuale** selezionare il tipo di **autenticazione** e quindi selezionare **genera e Scarica profilo**. Il pacchetto del profilo genererà un file zip contenente le impostazioni di configurazione da scaricare.
