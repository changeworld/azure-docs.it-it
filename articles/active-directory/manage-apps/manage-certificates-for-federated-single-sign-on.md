---
title: Gestione di certificati di federazione in Azure AD | Documentazione Microsoft
description: Informazioni su come personalizzare la data di scadenza per i certificati di federazione e su come rinnovare i certificati con scadenza imminente.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: iangithinji
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 594af54221dd242bd481fe3d2fc823f628c1f955
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379452"
---
# <a name="manage-certificates-for-federated-single-sign-on-in-azure-active-directory"></a>Gestione di certificati per accesso Single Sign-On federato in Azure Active Directory

In questo articolo vengono trattate domande e informazioni comuni relative ai certificati creati da Azure Active Directory (Azure AD) per stabilire l'accesso Single Sign-On federato (SSO) alle applicazioni SaaS (Software as a Service). Queste applicazioni possono essere aggiunte dalla raccolta di app di Azure AD o usando il modello di applicazione non inclusa nella raccolta. Configurare l'applicazione utilizzando l'opzione di SSO federato.

Questo articolo è pertinente solo per le app configurate per l'uso Azure AD SSO [tramite Security Assertion Markup Language](https://wikipedia.org/wiki/Security_Assertion_Markup_Language) (SAML).

## <a name="auto-generated-certificate-for-gallery-and-non-gallery-applications"></a>Certificato generato automaticamente per le applicazioni incluse e non incluse nella raccolta

Quando si aggiunge una nuova applicazione dalla raccolta e si configura un accesso basato su SAML (selezionando **Single Sign-On** SAML nella pagina di panoramica dell'applicazione), Azure AD genera un certificato per l'applicazione valido per tre  >   anni. Per scaricare il certificato attivo come file di certificato di sicurezza (con estensione **cer),** tornare a tale pagina (accesso basato su **SAML)** e selezionare un collegamento di download nell'intestazione Certificato di firma **SAML.** È possibile scegliere tra il certificato non elaborato (binario) o il certificato Base64 (testo con codifica Base 64). Per le applicazioni della raccolta, in questa sezione potrebbe essere visualizzato anche un collegamento per scaricare il certificato come XML dei metadati della federazione (un file con estensione **xml),** a seconda dei requisiti dell'applicazione.

![Opzioni di download del certificato di firma attivo SAML](./media/manage-certificates-for-federated-single-sign-on/active-certificate-download-options.png)

È anche possibile scaricare un certificato attivo o inattivo  selezionando l'icona Modifica (matita) dell'intestazione Certificato di firma **SAML,** che visualizza la pagina Certificato di firma **SAML.** Selezionare i puntini di sospensione (**...**) accanto al certificato da scaricare e quindi scegliere il formato del certificato desiderato. È possibile scaricare il certificato in formato PEM (Privacy-Enhanced Mail). Questo formato è identico a Base64, ma con estensione **pem,** che non è riconosciuto in Windows come formato certificato.

![Opzioni di download del certificato di firma SAML (attive e inattive)](./media/manage-certificates-for-federated-single-sign-on/all-certificate-download-options.png)

## <a name="customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate"></a>Personalizzare la data di scadenza per il certificato di federazione ed eseguire il rollover di un nuovo certificato

Per impostazione predefinita, Azure configura un certificato in modo che scada dopo tre anni quando viene creato automaticamente durante la configurazione dell'accesso Single Sign-On SAML. Poiché non è possibile modificare la data di un certificato dopo il salvataggio, è necessario:

1. Creare un nuovo certificato con la data desiderata.
1. Salvare il nuovo certificato.
1. Scaricare il nuovo certificato nel formato corretto.
1. Caricare il nuovo certificato nell'applicazione.
1. Rendere attivo il nuovo certificato nel portale Azure Active Directory.

Le due sezioni seguenti consentono di eseguire questi passaggi.

### <a name="create-a-new-certificate"></a>Creare un nuovo certificato

Per prima cosa, creare e salvare un nuovo certificato con una data di scadenza diversa:

1. Accedere al [portale di Azure Active Directory](https://aad.portal.azure.com/). Viene **visualizzata Azure Active Directory'interfaccia di amministrazione.**
1. Nel riquadro sinistro selezionare **Applicazioni aziendali**. Viene visualizzato un elenco delle applicazioni aziendali nell'account.
1. Selezionare l'applicazione interessata. Viene visualizzata una pagina di panoramica per l'applicazione.
1. Nel riquadro sinistro della pagina di panoramica dell'applicazione selezionare **Single Sign-On.**
1. Se viene visualizzata la pagina Selezionare un metodo di accesso Single **Sign-On,** selezionare **SAML.**
1. Nella pagina **Configura singolo Sign-On con SAML - Anteprima** individuare l'intestazione Certificato di firma **SAML** e selezionare l'icona **Modifica** (una matita). Viene **visualizzata la pagina Certificato** di firma SAML che visualizza lo stato ( Attivo o **Inattivo**), la data di scadenza e l'identificazione personale (una stringa hash) di ogni certificato.
1. Selezionare **Nuovo certificato**. Sotto l'elenco dei certificati viene visualizzata una nuova riga, in cui la data di scadenza viene impostata esattamente su tre anni dopo la data corrente. Le modifiche non sono ancora state salvate, quindi è comunque possibile modificare la data di scadenza.
1. Nella nuova riga del certificato passare il mouse sulla colonna della data di scadenza e selezionare **l'icona Seleziona** data (un calendario). Viene visualizzato un controllo calendario che visualizza i giorni di un mese della data di scadenza corrente della nuova riga.
1. Usare il controllo calendario per impostare una nuova data. È possibile impostare qualsiasi data compresa tra la data corrente e tre anni dopo la data corrente.
1. Selezionare **Salva**. Il nuovo certificato viene ora visualizzato con lo stato **Inattivo**, la data di scadenza scelta e un'identificazione personale.
1. Selezionare la **X** per tornare alla **pagina Set up Single Sign-On with SAML - Preview (Configura single Sign-On con SAML - Anteprima).**

### <a name="upload-and-activate-a-certificate"></a>Caricare e attivare un certificato

Scaricare quindi il nuovo certificato nel formato corretto, caricarlo nell'applicazione e attivarlo in Azure Active Directory:

1. Visualizzare le istruzioni aggiuntive per la configurazione dell'accesso SAML dell'applicazione:

   - Selezione del **collegamento alla guida** di configurazione da visualizzare in una finestra o in una scheda separata del browser oppure
   - Andare **all'intestazione di** configurazione e **selezionare Visualizza istruzioni** dettagliate da visualizzare in una barra laterale.

1. Nelle istruzioni prendere nota del formato di codifica necessario per il caricamento del certificato.
1. Seguire le istruzioni riportate in [precedenza nella sezione Certificato](#auto-generated-certificate-for-gallery-and-non-gallery-applications) generato automaticamente per le applicazioni della raccolta e non della raccolta. Questo passaggio scarica il certificato nel formato di codifica richiesto per il caricamento da parte dell'applicazione.
1. Quando si vuole eseguire il roll over al nuovo certificato, tornare alla pagina Certificato di firma **SAML** e nella riga del certificato appena salvato selezionare i puntini di sospensione (**...**) e selezionare Rendi attivo **il certificato.** Lo stato del nuovo certificato viene modificato in **Attivo** e il certificato precedentemente attivo viene modificato in **Inattivo.**
1. Continuare a seguire le istruzioni di configurazione dell'accesso SAML dell'applicazione visualizzate in precedenza, in modo da poter caricare il certificato di firma SAML nel formato di codifica corretto.

## <a name="add-email-notification-addresses-for-certificate-expiration"></a>Aggiungere indirizzi di notifica di posta elettronica per la scadenza del certificato

Azure AD invierà una notifica tramite posta elettronica 60, 30 e 7 giorni prima della scadenza del certificato SAML. È possibile aggiungere più di un indirizzo di posta elettronica per ricevere le notifiche. Per specificare gli indirizzi di posta elettronica a cui inviare le notifiche:

1. Nella pagina **Certificato di firma SAML** passare all'intestazione degli indirizzi di posta elettronica **di** notifica. Per impostazione predefinita, questa intestazione usa solo l'indirizzo di posta elettronica dell'amministratore che ha aggiunto l'applicazione.
1. Sotto l'indirizzo di posta elettronica finale digitare l'indirizzo di posta elettronica che deve ricevere l'avviso di scadenza del certificato e quindi premere INVIO.
1. Ripetere il passaggio precedente per ogni indirizzo di posta elettronica da aggiungere.
1. Per ogni indirizzo di posta elettronica da eliminare, selezionare **l'icona** Elimina (un garbage can) accanto all'indirizzo di posta elettronica.
1. Selezionare **Salva**.

È possibile aggiungere fino a 5 indirizzi di posta elettronica all'elenco Di notifica (incluso l'indirizzo di posta elettronica dell'amministratore che ha aggiunto l'applicazione). Se sono necessarie più persone per ricevere una notifica, usare i messaggi di posta elettronica della lista di distribuzione.

Si riceverà la notifica tramite posta elettronica da aadnotification@microsoft.com. Per evitare che il messaggio di posta elettronica sia inviato alla posizione della posta indesiderata, aggiungere questo messaggio di posta elettronica ai contatti.

## <a name="renew-a-certificate-that-will-soon-expire"></a>Rinnovare un certificato con scadenza imminente

Se un certificato sta per scadere, è possibile rinnovarlo usando una procedura che non comporta tempi di inattività significativi per gli utenti. Per rinnovare un certificato in scadenza:

1. Seguire le istruzioni nella sezione [Creare un nuovo certificato](#create-a-new-certificate) in precedenza, usando una data che si sovrappone al certificato esistente. Tale data limita il tempo di inattività causato dalla scadenza del certificato.
1. Se l'applicazione può eseguire automaticamente il roll over di un certificato, impostare il nuovo certificato su attivo seguendo questa procedura:
   1. Tornare alla pagina **Certificato di firma SAML.**
   1. Nella riga del certificato appena salvato selezionare i puntini di sospensione (**...**) e quindi **selezionare Rendi attivo il certificato**.
   1. Ignorare i due passaggi successivi.

1. Se l'app può gestire un solo certificato alla volta, selezionare un intervallo di tempo di inattività per eseguire il passaggio successivo. In caso contrario, se l'applicazione non preleva automaticamente il nuovo certificato ma può gestire più di un certificato di firma, è possibile eseguire il passaggio successivo in qualsiasi momento.
1. Prima della scadenza del certificato precedente, seguire le istruzioni nella sezione Caricare e [attivare un](#upload-and-activate-a-certificate) certificato in precedenza.
1. Accedere all'applicazione per assicurarsi che il certificato funzioni correttamente.

## <a name="related-articles"></a>Articoli correlati

- [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](../saas-apps/tutorial-list.md)
- [Gestione delle applicazioni con Azure Active Directory](what-is-application-management.md)
- [Accesso Single Sign-On alle applicazioni in Azure Active Directory](what-is-single-sign-on.md)
- [Eseguire il debug dell'accesso Single Sign-On basato su SAML su applicazioni in Azure Active Directory](./debug-saml-sso-issues.md)
