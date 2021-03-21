---
title: Versioni del layout di pagina
titleSuffix: Azure AD B2C
description: Cronologia delle versioni del layout di pagina per la personalizzazione dell'interfaccia utente nei criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b15c63545c71d4513abe9102b4de165e2ab5857a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499850"
---
# <a name="page-layout-versions"></a>Versioni del layout di pagina

I pacchetti del layout di pagina vengono aggiornati periodicamente per includere correzioni e miglioramenti negli elementi della pagina. Nel log delle modifiche seguente vengono specificate le modifiche introdotte in ogni versione.

## <a name="self-asserted-page-selfasserted"></a>Pagina autocertificata (selfasserted)

**2.1.2**
- Correzione del problema di codifica della localizzazione per le lingue, ad esempio spagnolo e francese.

**2.1.1**

- Aggiunta di un UXString `heading` , oltre a `intro` da visualizzare nella pagina come titolo. Questa operazione è nascosta per impostazione predefinita.
- Aggiunta del supporto per il salvataggio delle password nel keychain di iCloud.
- Aggiunta del supporto per l'utilizzo dei criteri o del parametro QueryString `pageFlavor` per selezionare il layout (classico, oceanBlue o GrigioArdesia).
- Sono state aggiunte dichiarazioni di non responsabilità nella pagina autocertificata.
- Lo stato attivo viene ora inserito nel primo campo modificabile quando viene caricata la pagina.
- Lo stato attivo viene ora inserito nel primo campo di errore se più campi contengono errori.
- Lo stato attivo viene ora inserito sul pulsante ' modifica ' dopo la verifica del codice di verifica tramite posta elettronica.

**2.1.0**

- Correzioni per la localizzazione e l'accessibilità.

**2.0.0**

- Aggiunta del supporto per la [visualizzazione dei controlli](display-controls.md) nei criteri personalizzati.

**1.2.0**

- I campi nome utente/indirizzo di posta elettronica e password ora usano l' `form` elemento HTML per consentire a Edge e Internet Explorer di salvare correttamente queste informazioni.
- È stato aggiunto un ritardo di convalida dell'input utente configurabile per migliorare l'esperienza utente.
-  Correzioni di accessibilità
- Correzione di un problema di accessibilità in modo che i messaggi di errore vengano ora letti dall'Assistente vocale. 
- Lo stato attivo viene ora inserito nel campo password dopo la verifica del messaggio di posta elettronica.
- Rimosso `autofocus` dal controllo CheckBox. 
- Aggiunta del supporto per un controllo di visualizzazione per la verifica del numero di telefono.
- È ora possibile aggiungere l' `data-preload="true"` attributo [nei tag HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content)
  - Caricare i file CSS collegati allo stesso tempo del modello HTML in modo che non venga "sfarfallio" tra il caricamento dei file.
  - Controllare l'ordine in cui i `script` tag vengono recuperati ed eseguiti prima del caricamento della pagina.
- Il campo posta elettronica è ora `type=email` e le tastiere per dispositivi mobili forniranno i suggerimenti corretti.
- Supporto per la conversione di Chrome.
- Aggiunta del supporto per la personalizzazione dell'azienda nelle pagine del flusso utente.

**1.1.0**

- Rimosso avviso di annullamento
- Classe CSS per gli elementi error
- Mostra/Nascondi la logica di errore migliorata
- CSS predefinito rimosso

**1.0.0**

- Versione iniziale

## <a name="unified-sign-in-sign-up-page-with-password-reset-link-unifiedssp"></a>Pagina di iscrizione dell'accesso unificato con collegamento per la reimpostazione della password (unifiedssp)

> [!TIP]
> Se si localizza la pagina per supportare più impostazioni locali o lingue in un flusso utente. L'articolo [ID localizzazione](localization-string-ids.md) fornisce l'elenco degli ID di localizzazione che è possibile usare per la versione della pagina selezionata.

**2.1.2**
- Correzione del problema di codifica della localizzazione per le lingue, ad esempio spagnolo e francese.
- Consentire il collegamento "password dimenticata" da utilizzare come scambio di attestazioni. Per altre informazioni, vedere [reimpostazione della password self-service](add-password-reset-policy.md#self-service-password-reset-recommended).

**2.1.1**
- Aggiunta di un UXString `heading` , oltre a `intro` da visualizzare nella pagina come titolo. Questa operazione è nascosta per impostazione predefinita.
- Aggiunta del supporto per l'utilizzo dei criteri o del parametro QueryString `pageFlavor` per selezionare il layout (classico, oceanBlue o GrigioArdesia).
- Aggiunta del supporto per il salvataggio delle password nel keychain di iCloud.
- Lo stato attivo viene ora inserito nel primo campo di errore se più campi contengono errori.
- Lo stato attivo viene ora inserito nel primo campo modificabile quando viene caricata la pagina.
- È stato aggiunto un nuovo percorso per il collegamento di selezione del provider di attestazioni `bottomUnderFormClaimsProviderSelections` .
- Rimossi UXStrings che non vengono più utilizzati.

**2.1.0**

- Aggiunta del supporto per più collegamenti di iscrizione.
- Aggiunta del supporto per la convalida dell'input dell'utente in base alle regole del predicato definite nei criteri.

**1.2.0**

- I campi nome utente/indirizzo di posta elettronica e password ora usano l' `form` elemento HTML per consentire a Edge e Internet Explorer di salvare correttamente queste informazioni.
-  Correzioni di accessibilità
- È ora possibile aggiungere l' `data-preload="true"` attributo [nei tag HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) per controllare l'ordine di caricamento per CSS e JavaScript.
  - Caricare i file CSS collegati allo stesso tempo del modello HTML in modo che non venga "sfarfallio" tra il caricamento dei file.
  - Controllare l'ordine in cui i `script` tag vengono recuperati ed eseguiti prima del caricamento della pagina.
- Il campo posta elettronica è ora `type=email` e le tastiere per dispositivi mobili forniranno i suggerimenti corretti.
- Supporto per la conversione di Chrome.
- Aggiunta del supporto per la personalizzazione del tenant nelle pagine del flusso utente.

**1.1.0**

- Aggiunto il controllo Mantieni l'accesso (KMSI)

**1.0.0**

- Versione iniziale

## <a name="mfa-page-multifactor"></a>Pagina multi-factor authentication (a più fattori)

**1.2.2**
- Correzione di un problema relativo al completamento automatico del codice di verifica quando si utilizza iOS.
- Correzione di un problema relativo al reindirizzamento di un token al relying party da Android WebView. 
- Aggiunta di un UXString `heading` , oltre a `intro` da visualizzare nella pagina come titolo. Questa operazione è nascosta per impostazione predefinita.  
- Aggiunta del supporto per l'utilizzo dei criteri o del parametro QueryString `pageFlavor` per selezionare il layout (classico, oceanBlue o GrigioArdesia).

**1.2.1**

- Correzioni di accessibilità sui modelli predefiniti

**1.2.0**

-  Correzioni di accessibilità
- È ora possibile aggiungere l' `data-preload="true"` attributo [nei tag HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) per controllare l'ordine di caricamento per CSS e JavaScript.
  - Caricare i file CSS collegati allo stesso tempo del modello HTML in modo che non venga "sfarfallio" tra il caricamento dei file.
  - Controllare l'ordine in cui i `script` tag vengono recuperati ed eseguiti prima del caricamento della pagina.
- Il campo posta elettronica è ora `type=email` e le tastiere per dispositivi mobili forniranno i suggerimenti corretti
- Supporto per la conversione di Chrome.
- Aggiunta del supporto per la personalizzazione del tenant nelle pagine del flusso utente.

**1.1.0**

- Pulsante ' conferma codice ' rimosso
- Il campo di input per il codice ora accetta solo un input composto da sei (6) caratteri
- La pagina tenterà automaticamente di verificare il codice immesso quando viene immesso un codice di 6 cifre, senza che sia necessario fare clic su un pulsante
- Se il codice è errato, il campo di input viene cancellato automaticamente
- Dopo tre (3) tentativi con codice errato, B2C Invia un errore all'relying party
-  Correzioni di accessibilità
- CSS predefinito rimosso

**1.0.0**

- Versione iniziale

## <a name="exception-page-globalexception"></a>Pagina eccezione (globalexception)

**1.2.0**

-  Correzioni di accessibilità
- È ora possibile aggiungere l' `data-preload="true"` attributo [nei tag HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) per controllare l'ordine di caricamento per CSS e JavaScript.
  - Caricare i file CSS collegati allo stesso tempo del modello HTML in modo che non venga "sfarfallio" tra il caricamento dei file.
  - Controllare l'ordine in cui i `script` tag vengono recuperati ed eseguiti prima del caricamento della pagina.
- Il campo posta elettronica è ora `type=email` e le tastiere per dispositivi mobili forniranno i suggerimenti corretti
- Supporto per la conversione di Chrome

**1.1.0**

- Correzione accessibilità
- Rimosso il messaggio predefinito quando non è presente alcun contatto dal criterio
- CSS predefinito rimosso

**1.0.0**

- Versione iniziale

## <a name="other-pages-providerselection-claimsconsent-unifiedssd"></a>Altre pagine (ProviderSelection, ClaimsConsent, UnifiedSSD)

**1.2.0**

-  Correzioni di accessibilità
- È ora possibile aggiungere l' `data-preload="true"` attributo [nei tag HTML](customize-ui-with-html.md#guidelines-for-using-custom-page-content) per controllare l'ordine di caricamento per CSS e JavaScript.
  - Caricare i file CSS collegati allo stesso tempo del modello HTML in modo che non venga "sfarfallio" tra il caricamento dei file.
  - Controllare l'ordine in cui i `script` tag vengono recuperati ed eseguiti prima del caricamento della pagina.
- Il campo posta elettronica è ora `type=email` e le tastiere per dispositivi mobili forniranno i suggerimenti corretti
- Supporto per la conversione di Chrome

**1.0.0**

- Versione iniziale

## <a name="next-steps"></a>Passaggi successivi

Per informazioni dettagliate su come personalizzare l'interfaccia utente delle applicazioni in criteri personalizzati, vedere [personalizzare l'interfaccia utente dell'applicazione usando un criterio personalizzato](customize-ui-with-html.md).
