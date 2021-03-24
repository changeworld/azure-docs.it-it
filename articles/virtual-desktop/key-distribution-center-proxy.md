---
title: Configurare Kerberos Centro distribuzione chiavi proxy desktop virtuale Windows-Azure
description: Come configurare un pool host di desktop virtuali Windows per l'uso di un proxy Centro distribuzione chiavi Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 876564934b1ccbffa19c318a2d2c8393e5dca54e
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023980"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Configurare un proxy di Centro distribuzione chiavi Kerberos (anteprima)

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I clienti attenti alla sicurezza, ad esempio le organizzazioni finanziarie o governative, spesso possono accedere usando Smart Card. Le smart card rendono più sicure le distribuzioni, richiedendo l'autenticazione a più fattori. Per la parte RDP di una sessione di desktop virtuale di Windows, tuttavia, le smart card richiedono una connessione diretta o una "linea di visione" con un controller di dominio Active Directory (AD) per l'autenticazione Kerberos. Senza questa connessione diretta, gli utenti non possono accedere automaticamente alla rete dell'organizzazione da connessioni remote. Gli utenti in una distribuzione desktop virtuale di Windows possono usare il servizio proxy KDC per eseguire il proxy del traffico di autenticazione ed eseguire l'accesso in remoto. Il proxy KDC consente l'autenticazione per la Remote Desktop Protocol di una sessione desktop virtuale di Windows, consentendo all'utente di accedere in modo sicuro. In questo modo, è molto più facile lavorare da casa e alcuni scenari di ripristino di emergenza possono essere eseguiti in modo più agevole.

Tuttavia, la configurazione del proxy KDC comporta in genere l'assegnazione del ruolo Gateway Windows Server in Windows Server 2016 o versioni successive. Come si usa un ruolo di Servizi Desktop remoto per accedere a desktop virtuale di Windows? Per rispondere, è opportuno esaminare rapidamente i componenti.

Sono disponibili due componenti per il servizio desktop virtuale di Windows che devono essere autenticati:

- Il feed nel client desktop virtuale di Windows che fornisce agli utenti un elenco dei desktop o delle applicazioni disponibili a cui hanno accesso. Questo processo di autenticazione si verifica in Azure Active Directory, il che significa che questo componente non è l'obiettivo di questo articolo.
- Sessione RDP risultante dall'utente che seleziona una di queste risorse disponibili. Questo componente usa l'autenticazione Kerberos e richiede un proxy KDC per gli utenti remoti.

In questo articolo viene illustrato come configurare il feed nel client desktop virtuale di Windows nell'portale di Azure. Per informazioni su come configurare il ruolo Gateway Desktop remoto, vedere [distribuire il ruolo Gateway Desktop remoto](/windows-server/remote/rd-gateway-role).

## <a name="requirements"></a>Requisiti

Per configurare un host sessione desktop virtuale Windows con un proxy KDC, sono necessari gli elementi seguenti:

- Accesso all'portale di Azure e a un account amministratore di Azure.
- Per i computer client remoti è necessario che sia in esecuzione Windows 10 o Windows 7 e che il [client desktop di Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) sia installato.
- È necessario che nel computer sia già installato un proxy KDC. Per informazioni su come eseguire questa operazione, vedere [configurare il ruolo Gateway Desktop remoto per desktop virtuale di Windows](rd-gateway-role.md).
- Il sistema operativo del computer deve essere Windows Server 2016 o versione successiva.

Una volta soddisfatti questi requisiti, si è pronti per iniziare.

## <a name="how-to-configure-the-kdc-proxy"></a>Come configurare il proxy KDC

Per configurare il proxy KDC:

1. Accedre al portale di Azure come amministratore.

2. Passare alla pagina desktop virtuale di Windows.

3. Selezionare il pool di host per il quale si desidera abilitare il proxy KDC, quindi selezionare **Proprietà RDP**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della pagina portale di Azure che mostra un utente che seleziona pool host, quindi il nome del pool di host di esempio e le proprietà RDP.](media/rdp-properties.png)

4. Selezionare la scheda **Avanzate** , quindi immettere un valore nel formato seguente senza spazi:

    
    > kdcproxyname: s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![Screenshot che mostra la scheda Avanzate selezionata con il valore immesso come descritto nel passaggio 4.](media/advanced-tab-selected.png)

5. Selezionare **Salva**.

6. Il pool host selezionato dovrebbe ora iniziare a rilasciare i file di connessione RDP che includono il valore kdcproxyname immesso nel passaggio 4.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come gestire il lato Servizi Desktop remoto del proxy KDC e assegnare il ruolo Gateway Desktop remoto, vedere [distribuire il ruolo Gateway Desktop remoto](rd-gateway-role.md).

Se si è interessati a ridimensionare i server proxy KDC, vedere la pagina relativa alla configurazione della disponibilità elevata per il proxy KDC in [aggiungere la disponibilità elevata ai front-end Web e gateway Web Desktop remoto](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha).
