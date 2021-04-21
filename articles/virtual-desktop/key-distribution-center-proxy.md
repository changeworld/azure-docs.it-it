---
title: Configurare Il proxy Kerberos Centro distribuzione chiavi Desktop virtuale Windows - Azure
description: Come configurare un pool di host di Desktop virtuale Windows per l'uso di un proxy Centro distribuzione chiavi Kerberos.
author: Heidilohr
ms.topic: how-to
ms.date: 03/20/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 9dce264b7f2c88aed11f5b82a61f83cbac6c9697
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785110"
---
# <a name="configure-a-kerberos-key-distribution-center-proxy-preview"></a>Configurare un proxy Centro distribuzione chiavi Kerberos (anteprima)

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I clienti con esigenze di sicurezza, ad esempio le organizzazioni finanziarie o governative, spesso a eseguire l'accesso usando smart card. Le smart card rendono più sicure le distribuzioni richiedendo l'autenticazione a più fattori (MFA). Tuttavia, per la parte RDP di una sessione di Desktop virtuale Windows, le smart card richiedono una connessione diretta, o "linea di vista", con un controller di dominio active directory (AD) per l'autenticazione Kerberos. Senza questa connessione diretta, gli utenti non possono accedere automaticamente alla rete dell'organizzazione da connessioni remote. Gli utenti in una distribuzione di Desktop virtuale Windows possono usare il servizio proxy KDC per eseguire il proxy del traffico di autenticazione e accedere in remoto. Il proxy KDC consente l'autenticazione per Remote Desktop Protocol di una sessione di Desktop virtuale Windows, consentendo all'utente di accedere in modo sicuro. In questo modo è molto più semplice lavorare da casa e alcuni scenari di ripristino di emergenza possono essere eseguiti in modo più semplice.

Tuttavia, la configurazione del proxy KDC comporta in genere l'assegnazione del ruolo Gateway Windows Server in Windows Server 2016 o versioni successive. Come si usa un ruolo Servizi Desktop remoto per accedere a Desktop virtuale Windows? Per rispondere a questa domanda, si esaminino rapidamente i componenti.

Esistono due componenti del servizio Desktop virtuale Windows che devono essere autenticati:

- Feed nel client desktop virtuale Windows che fornisce agli utenti un elenco di desktop o applicazioni disponibili a cui hanno accesso. Questo processo di autenticazione si verifica Azure Active Directory, il che significa che questo componente non è l'obiettivo di questo articolo.
- Sessione RDP che risulta da un utente che seleziona una di queste risorse disponibili. Questo componente usa l'autenticazione Kerberos e richiede un proxy KDC per gli utenti remoti.

Questo articolo illustra come configurare il feed nel client Desktop virtuale Windows nel portale di Azure. Per informazioni su come configurare il ruolo Gateway Desktop remoto, vedere [Distribuire il ruolo Gateway Desktop remoto](/azure/virtual-desktop/rd-gateway-role).

## <a name="requirements"></a>Requisiti

Per configurare un host sessione di Desktop virtuale Windows con un proxy KDC, sono necessari gli elementi seguenti:

- Accesso al portale di Azure un account amministratore di Azure.
- Nei computer client remoti deve essere in esecuzione Windows 10 o Windows 7 e il [client Desktop di Windows deve essere](/windows-server/remote/remote-desktop-services/clients/windowsdesktop) installato. Attualmente, il client Web non è supportato.
- È necessario che nel computer sia già installato un proxy KDC. Per informazioni su come eseguire questa operazione, vedere [Configurare il ruolo Gateway Desktop remoto per Desktop virtuale Windows.](rd-gateway-role.md)
- Il sistema operativo del computer deve essere Windows Server 2016 o versione successiva.

Dopo aver verificato di soddisfare questi requisiti, si è pronti per iniziare.

## <a name="how-to-configure-the-kdc-proxy"></a>Come configurare il proxy KDC

Per configurare il proxy KDC:

1. Accedre al portale di Azure come amministratore.

2. Passare alla pagina Desktop virtuale Windows.

3. Selezionare il pool di host per cui si vuole abilitare il proxy KDC, quindi selezionare **Proprietà RDP**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della pagina portale di Azure che mostra un utente che seleziona pool di host, quindi il nome del pool di host di esempio e quindi le proprietà RDP.](media/rdp-properties.png)

4. Selezionare la **scheda** Avanzate e quindi immettere un valore nel formato seguente senza spazi:

    
    > kdcproxyname:s:\<fqdn\>
    

    > [!div class="mx-imgBorder"]
    > ![Screenshot che mostra la scheda Avanzate selezionata, con il valore immesso come descritto nel passaggio 4.](media/advanced-tab-selected.png)

5. Selezionare **Salva**.

6. Il pool di host selezionato dovrebbe ora iniziare a rilasciare file di connessione RDP che includono il valore kdcproxyname immesso nel passaggio 4.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come gestire il Servizi Desktop remoto del proxy KDC e assegnare il ruolo Gateway Desktop [remoto,](rd-gateway-role.md)vedere Distribuire il ruolo Gateway Desktop remoto .

Se si è interessati al ridimensionamento dei server proxy KDC, vedere Come configurare la disponibilità elevata per il proxy KDC in Aggiungere disponibilità elevata al front-end Web Gateway [e Web Desktop remoto.](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)
