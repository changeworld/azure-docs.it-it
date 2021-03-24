---
title: Frequenza di aggiornamento software VMware
description: Frequenza di aggiornamento software VMware supportata per la soluzione VMware di Azure.
ms.topic: include
ms.date: 03/22/2021
ms.openlocfilehash: a3290ed704b493fae3e86223857ff9f1c5617f1a
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869864"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

Microsoft è responsabile della gestione del ciclo di vita del software VMware (ESXi, vCenter, PSC e NXS) nel cloud privato della soluzione VMware di Azure.

Il software per il cloud privato viene aggiornato in base a una pianificazione che tiene traccia del rilascio del bundle software da VMware. Gli aggiornamenti non comportano tempi di inattività per il cloud privato.

Gli aggiornamenti del pacchetto software del cloud privato conservano il software entro una versione della versione più recente del bundle software da VMware. Le versioni del software del cloud privato possono differire dalle versioni più recenti dei singoli componenti software (ESXi, NSX-T, vCenter, rete VSAN).

Gli aggiornamenti software includono:

- **Patch** -patch di sicurezza o correzioni di bug rilasciati da VMware
- **Aggiornamenti** -modifica della versione secondaria di un componente dello stack VMware
- **Aggiornamenti** : modifica della versione principale di un componente dello stack VMware

Microsoft testa una patch di sicurezza critica non appena diventa disponibile da VMware.

Le soluzioni VMware documentate sono implementate invece di installare una patch corrispondente fino a quando non vengono distribuiti i successivi aggiornamenti pianificati. 
