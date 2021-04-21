---
title: Autenticazione SSH con Azure Active Directory
description: Indicazioni sull'architettura per ottenere l'integrazione SSH con Azure Active Directory
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/19/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 315fe35a79ade39de9f541504fc2fe52754614de
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749349"
---
# <a name="ssh"></a>SSH  

Secure Shell (SSH) è un protocollo di rete che fornisce la crittografia per il funzionamento sicuro dei servizi di rete su una rete non protetta. SSH fornisce anche un accesso dalla riga di comando, esegue comandi remoti e trasferisce in modo sicuro i file. Viene comunemente usato nei sistemi basati su UNIX, ad esempio Linux®. SSH sostituisce il protocollo Telnet, che non fornisce la crittografia in una rete non protetta. 

Azure Active Directory (Azure AD) fornisce un'estensione macchina virtuale (VM) per sistemi basati ® Linux in esecuzione in Azure. 

## <a name="use-when"></a>Casi di utilizzo 

* Uso di macchine ® linux che richiedono l'accesso remoto

* Esecuzione di comandi remoti in sistemi basati ® Linux

* Trasferire in modo sicuro i file in una rete non protetta

![Diagramma della Azure AD con il protocollo SSH](./media/authentication-patterns/ssh-auth.png)

SSH con Azure AD

## <a name="components-of-system"></a>Componenti del sistema 

* **Utente:** avvia il client SSH per configurare una connessione con le macchine virtuali Linux® e fornisce le credenziali per l'autenticazione.

* **Web browser**: componente con cui interagisce l'utente. Comunica con il provider di identità (Azure AD) per autenticare e autorizzare l'utente in modo sicuro.

* **Client SSH:** determina il processo di configurazione della connessione.

* **Azure AD:** autentica l'identità dell'utente usando il flusso del dispositivo ed eseguono il token per le macchine virtuali Linux.

* **Macchina virtuale Linux:** accetta il token e fornisce una connessione riuscita.

## <a name="implement-ssh-with-azure-ad"></a>Implementare SSH con Azure AD 

* [Accedere a una macchina virtuale Linux® con Azure Active Directory credenziali - Macchine virtuali di Azure ](../../virtual-machines/linux/login-using-aad.md) 

* [Flusso di codice del dispositivo OAuth 2.0 - Microsoft Identity Platform ](../develop/v2-oauth2-device-code.md)

* [Integrazione con Azure Active Directory (akamai.com)](https://learn.akamai.com/en-us/webhelp/enterprise-application-access/enterprise-application-access/GUID-6B16172C-86CC-48E8-B30D-8E678BF3325F.html)

