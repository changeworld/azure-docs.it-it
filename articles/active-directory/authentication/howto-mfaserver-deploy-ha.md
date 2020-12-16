---
title: Disponibilità elevata per il server Azure MFA - Azure Active Directory
description: Distribuire più istanze del server Microsoft Azure Multi-Factor Authentication in configurazioni che offrono disponibilità elevata.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c1853a4784e3098ecbcefa94d5512b4877ac4dc4
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584477"
---
# <a name="configure-azure-multi-factor-authentication-server-for-high-availability"></a>Configurare il server Azure MFA per la disponibilità elevata

Per ottenere disponibilità elevata con la distribuzione del server Azure MFA, è necessario distribuire più server MFA. Questa sezione contiene informazioni su una progettazione con bilanciamento del carico per ottenere destinazioni a disponibilità elevata nella distribuzione di server Azure MFS.

> [!IMPORTANT]
> A partire dal 1 ° luglio 2019, Microsoft non offre più il server multi-factor authentication per le nuove distribuzioni. I nuovi clienti che vogliono richiedere l'autenticazione a più fattori durante gli eventi di accesso devono usare Multi-Factor Authentication di Azure AD basati sul cloud.
>
> Per iniziare a usare l'autenticazione a più fattori basata sul cloud, vedere [esercitazione: proteggere gli eventi di accesso utente con Azure AD multi-factor authentication](tutorial-enable-azure-mfa.md).
>
> I clienti esistenti che hanno attivato il server multi-factor authentication prima del 1 ° luglio 2019 possono scaricare la versione più recente, gli aggiornamenti futuri e generare le credenziali di attivazione come di consueto.

## <a name="mfa-server-overview"></a>Panoramica del server MFA

L'architettura di servizio del server Azure MFA include diversi componenti, come illustrato nel diagramma seguente:

 ![Componenti dell'architettura di servizio del server MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-architecture.png)

Un server MFA è un'istanza di Windows Server in cui è installato il software Azure Multi-Factor Authentication. Per funzionare, l'istanza del server MFA deve essere attivata dal servizio MFA in Azure. È possibile installare più di un server MFA locale.

Il primo server di autenticazione a più fattori installato è il server di autenticazione a più fattori primario dopo l'attivazione del Servizio autenticazione a più fattori di Azure per impostazione predefinita Il server di autenticazione a più fattori primario dispone di una copia scrivibile del database PhoneFactor. pfdata. Le installazioni successive delle istanze del Server MFA sono note come subordinate. Le subordinate di MFA sono una copia replicata di sola lettura del database PhoneFactor.pfdata. I server MFA replicano le informazioni mediante Remote Procedure Call (RPC). Tutti i server MFA devono essere collettivamente aggiunti a un dominio o autonomi per replicare informazioni.

Quando è richiesta l'autenticazione a due fattori, entrambi i server multi-factor authentication primari e subordinati comunicano con il servizio multi-factor Ad esempio, quando un utente tenta di accedere a un'applicazione che richiede l'autenticazione a due fattori, l'utente verrà prima autenticato da un provider di identità, ad esempio Active Directory (AD).

In seguito all'autenticazione con AD, il server MFA comunicherà con il servizio MFA. Il server MFA attende la notifica dal servizio MFA per consentire o negare l'accesso dell'utente all'applicazione.

Se il server primario dell'autenticazione a più fattori passa alla modalità offline, le autenticazioni possono essere ancora elaborate, ma non è possibile elaborare le operazioni che richiedono modifiche al database multi-factor authentication. Alcuni esempi includono l'aggiunta di utenti, le modifiche PIN self-service, la modifica delle informazioni utente o l'accesso al portale per gli utenti.

## <a name="deployment"></a>Distribuzione

Considerare gli aspetti fondamentali seguenti per eseguire il bilanciamento del carico del server Azure MFA e dei componenti correlati.

* **Uso dello standard RADIUS per conseguire disponibilità elevata**. Se si usano server Azure MFA come server RADIUS, è possibile configurare un server MFA come destinazione di autenticazione RADIUS primaria e altri server Azure MFA come destinazioni di autenticazione secondarie. Questo metodo per conseguire la disponibilità elevata, tuttavia, potrebbe non risultare pratico, poiché è necessario attendere che si verifichi un timeout quando l'autenticazione ha esito negativo nella destinazione di autenticazione primaria prima che sia possibile eseguire l'autenticazione con la destinazione di autenticazione secondaria. È più efficiente eseguire il bilanciamento del carico del traffico RADIUS tra il client RADIUS e i server RADIUS (in questo caso, le istanze del server Azure MFA che operano come server RADIUS) in modo da poter configurare i client RADIUS con un singolo URL a cui possano puntare.
* **Necessità di alzare manualmente di livello le istanze subordinate di MFA**. Se il server di autenticazione a più fattori di Azure primario passa offline, i server di autenticazione a più fattori di Azure secondari continuano a elaborare le richieste di Tuttavia, fino a quando non è disponibile un server di autenticazione a più fattori primario, gli amministratori non possono aggiungere utenti o modificare le impostazioni dell'autenticazione a più fattori e gli utenti non possono apportare modifiche tramite il portale utenti. La promozione di un multi-factor authentication subordinato al ruolo primario è sempre un processo manuale.
* **Separabilità dei componenti**. Il server Azure MFA è costituito da diversi componenti che possono essere installati nella stessa istanza di Windows Server o in istanze diverse. Questi componenti includono il portale per gli utenti, il servizio Web per app per dispositivi mobili e la scheda AD FS (agente). Questa separabilità permette di usare il proxy applicazione Web per pubblicare il portale per gli utenti e il server Web per app per dispositivi mobili dalla rete perimetrale. Questo tipo di configurazione contribuisce alla sicurezza complessiva delle progettazioni, come illustrato nel diagramma seguente. È anche possibile distribuire il portale per gli utenti MFA e il server Web per app per dispositivi mobili in configurazioni con bilanciamento del carico a disponibilità elevata.

   ![Server MFA con una rete perimetrale](./media/howto-mfaserver-deploy-ha/mfasecurity.png)

* **La password monouso (OTP) su SMS, nota anche come SMS unidirezionale, richiede l'uso di sessioni permanenti se il traffico viene sottoposto a bilanciamento del carico**. L'opzione di autenticazione con SMS unidirezionale determina l'invio agli utenti di un SMS contenente un messaggio OTP da parte del server MFA. L'utente immette l'OTP in una finestra del prompt dei comandi per completare la richiesta di autenticazione a più fattori. Se si esegue il bilanciamento del carico delle istanze del server Azure MFA, il server che ha gestito la richiesta di autenticazione iniziale deve essere quello che riceve il messaggio OTP dall'utente. Se un'altra istanza del server MFA riceve la risposta OTP, la richiesta di autenticazione ha esito negativo. Per altre informazioni, vedere [One Time Password over SMS Added to Azure MFA Server](https://blogs.technet.microsoft.com/enterprisemobility/2015/03/02/one-time-password-over-sms-added-to-azure-mfa-server) (Aggiunta di One Time Password con SMS al server Azure MFA).
* **Le distribuzioni con bilanciamento del carico del portale per gli utenti e del servizio Web per app per dispositivi mobili richiedono sessioni permanenti**. Se si esegue il bilanciamento del carico del portale per utenti MFA e del servizio Web per app per dispositivi mobili, ogni sessione deve rimanere nello stesso server.

## <a name="high-availability-deployment"></a>Distribuzione a disponibilità elevata

Il diagramma seguente illustra un'implementazione completa con bilanciamento del carico a disponibilità elevata di Azure MFA e dei relativi componenti, oltre ad AD FS per riferimento.

 ![Implementazione a disponibilità elevata del server Azure MFA](./media/howto-mfaserver-deploy-ha/mfa-ha-deployment.png)

Tenere presente gli elementi seguenti per l'area numerata corrispondente del diagramma precedente.

1. Le due istanze del server Azure MFA (MFA1 e MFA2) sono sottoposte a bilanciamento del carico (mfaapp.contoso.com) e configurate per l'uso di una porta statica (4443) per replicare il database PhoneFactor.pfdata. L'SDK del servizio Web è installato in ogni istanza del server MFA per consentire la comunicazione sulla porta TCP 443 con i server AD FS. Le istanze del server MFA sono distribuite in una configurazione con bilanciamento del carico senza stato. Tuttavia, se si intende usare OTP con SMS, è necessario usare il bilanciamento del carico con stato.
   ![Server Azure MFA - Elevata disponibilità del server app](./media/howto-mfaserver-deploy-ha/mfaapp.png)

   > [!NOTE]
   > Poiché RPC usa porte dinamiche, non è consigliabile aprire i firewall fino all'intervallo di porte dinamiche che RPC può usare. Se si dispone di un firewall **tra** i server applicazioni multi-factor authentication, è necessario configurare il server di autenticazione a più fattori per comunicare su una porta statica per il traffico di replica tra server subordinati e primari e aprire la porta nel firewall. È possibile forzare la porta statica creando un valore del Registro di sistema DWORD in ```HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Positive Networks\PhoneFactor``` denominato ```Pfsvc_ncan_ip_tcp_port``` e impostando il valore su una porta statica disponibile. Le connessioni vengono sempre avviate dai server di autenticazione a più fattori subordinati al database primario, la porta statica è obbligatoria solo sul database primario, ma poiché è possibile alzare di livello un subordinato come primario in qualsiasi momento, è necessario impostare la porta statica su tutti i server multi-factor authentication.

2. I due server del portale per gli utenti e delle app per dispositivi mobili MFA (MFA-UP-MAS1 e MFA-UP-MAS2) sono sottoposti a bilanciamento del carico in una configurazione **con stato** (mfa.contoso.com). Tenere presente che le sessioni permanenti sono un requisito necessario per il bilanciamento del carico del portale per gli utenti MFA e del servizio per app per dispositivi mobili.
   ![Server Azure MFA - Disponibilità elevata per il portale per gli utenti e per il servizio per app per dispositivi mobili](./media/howto-mfaserver-deploy-ha/mfaportal.png)
3. La server farm AD FS è sottoposta a bilanciamento del carico e pubblicata in Internet tramite proxy AD FS con bilanciamento del carico nella rete perimetrale. Ogni server AD FS usa l'agente AD FS per comunicare con i server Azure MFA mediante un unico URL con bilanciamento del carico (mfaapp.contoso.com) sulla porta TCP 443.

## <a name="next-steps"></a>Passaggi successivi

* [Installare e configurare il server Azure MFA](howto-mfaserver-deploy.md)
