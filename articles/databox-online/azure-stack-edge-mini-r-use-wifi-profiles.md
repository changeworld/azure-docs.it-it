---
title: Usare i profili di Wi-Fi con i dispositivi Mini R Azure Stack Edge
description: Viene descritto come creare profili di Wi-Fi per i dispositivi Mini R Azure Stack Edge su reti aziendali e reti aziendali con sicurezza elevata.
services: databox
author: v-dalc@microsoft.com
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/24/2021
ms.author: alkohli
ms.openlocfilehash: 90c7c238cef104eae78618e51fa4b284adcc8f42
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105050545"
---
# <a name="use-wi-fi-profiles-with-azure-stack-edge-mini-r-devices"></a>Usare i profili di Wi-Fi con i dispositivi Mini R Azure Stack Edge

Questo articolo descrive come usare i profili di rete wireless (Wi-Fi) con i dispositivi Mini R Azure Stack Edge.

La modalità di preparazione del profilo Wi-Fi dipende dal tipo di rete wireless:

- In una rete personale Wi-Fi Protected Access 2 (WPA2), ad esempio una rete domestica o Wi-Fi hotspot aperto, potrebbe essere possibile scaricare e usare un profilo wireless esistente con la stessa password usata con altri dispositivi.

- In un ambiente aziendale con sicurezza elevata, si accederà al dispositivo tramite una rete WPA2-Enterprise. In questo tipo di rete, ogni computer client avrà un profilo di Wi-Fi distinto e verrà autenticato tramite certificati. È necessario collaborare con l'amministratore di rete per determinare la configurazione richiesta.

Verranno discussi i requisiti di profilo per entrambi i tipi di rete più avanti.

In entrambi i casi, è molto importante verificare che il profilo soddisfi i requisiti di sicurezza dell'organizzazione prima di testare o usare il profilo con il dispositivo.

## <a name="about-wi-fi-profiles"></a>Informazioni sui profili Wi-Fi

Un profilo di Wi-Fi contiene il SSID (identificatore del set di servizi o **nome di rete**), la chiave della password e le informazioni di sicurezza necessarie per connettere il dispositivo Mini R Azure stack Edge a una rete wireless.

L'esempio di codice seguente mostra le impostazioni di base per un profilo da usare con una rete wireless tipica:

* `SSID` nome di rete.

* `name` nome descrittivo per la connessione Wi-Fi. Ovvero il nome visualizzato dagli utenti quando esplorano le connessioni disponibili nel dispositivo.

* Il profilo è configurato per connettere automaticamente il computer alla rete wireless quando è entro l'intervallo della rete ( `connectionMode`  =  `auto` ).

```html
<?xml version="1.0"?>
<WLANProfile xmlns="http://www.contoso.com/networking/WLAN/profile/v1">
    <name>ContosoWIFICORP</name>
    <SSIDConfig>
        <SSID>
            <hex>1A234561234B5012</hex>
        </SSID>
        <nonBroadcast>false</nonBroadcast>
    </SSIDConfig>
    <connectionType>ESS</connectionType>
    <connectionMode>auto</connectionMode>
    <autoSwitch>false</autoSwitch>
```

Per altre informazioni sulle impostazioni del profilo Wi-Fi, vedere **profilo Enterprise** in [aggiungere impostazioni Wi-Fi per i dispositivi Windows 10 e versioni successive](/mem/intune/configuration/wi-fi-settings-windows#enterprise-profile)e vedere [configurare il profilo Cisco Wi-Fi](azure-stack-edge-mini-r-manage-wifi.md#configure-cisco-wi-fi-profile).

Per abilitare le connessioni wireless in un dispositivo Mini R Azure Stack Edge, configurare la porta Wi-Fi sul dispositivo e quindi aggiungere i profili di Wi-Fi al dispositivo. In una rete aziendale verranno caricati anche i certificati nel dispositivo. È quindi possibile connettersi a una rete wireless dall'interfaccia utente Web locale per il dispositivo. Per altre informazioni, vedere [Manage wireless Connectivity on your Azure stack Edge Mini R](./azure-stack-edge-mini-r-manage-wifi.md).

## <a name="profile-for-wpa2---personal-network"></a>Profilo per la rete WPA2-Personal

In una rete personale Wi-Fi Protected Access 2 (WPA2), ad esempio una rete domestica o Wi-Fi hotspot aperto, più dispositivi possono usare lo stesso profilo e la stessa password. Nella rete domestica, il telefono cellulare e il computer portatile utilizzano lo stesso profilo wireless e la stessa password per connettersi alla rete.

Un client Windows 10, ad esempio, può generare automaticamente un profilo di Runtime. Quando si accede alla rete wireless, viene richiesta la password Wi-Fi e, una volta fornita tale password, si è connessi. Nessun certificato è necessario in questo ambiente.

In questo tipo di rete potrebbe essere possibile esportare un profilo di Wi-Fi dal computer portatile e quindi aggiungerlo al dispositivo Mini R Azure Stack Edge. Per istruzioni, vedere [esportare un profilo di Wi-Fi](#export-a-wi-fi-profile), più avanti.

> [!IMPORTANT]
> Prima di creare un profilo di Wi-Fi per il dispositivo Mini R Azure Stack Edge, contattare l'amministratore di rete per individuare i requisiti di sicurezza dell'organizzazione per la rete wireless. Non è necessario testare o usare alcun profilo di Wi-Fi nel dispositivo finché non si conosce la rete wireless che soddisfa i requisiti.

## <a name="profiles-for-wpa2---enterprise-network"></a>Profili per la rete WPA2-Enterprise

In una rete Wireless Protected Access 2 (WPA2)-Enterprise è necessario collaborare con l'amministratore di rete per ottenere il profilo Wi-Fi e il certificato necessari per connettere il dispositivo Mini R Azure Stack Edge alla rete.

Per le reti altamente sicure, il dispositivo Azure può usare PEAP (Protected Extensible Authentication Protocol) con Extensible Authentication Protocol-Transport Layer Security (EAP-TLS). PEAP con EAP-TLS usa l'autenticazione del computer: il client e il server usano i certificati per verificare le rispettive identità.

> [!NOTE]
> * L'autenticazione utente tramite PEAP Microsoft Challenge Handshake Authentication Protocol versione 2 (PEAP MSCHAPv2) non è supportata nei dispositivi Mini R Azure Stack Edge.
> * Per accedere alla funzionalità Mini R di Azure Stack Edge, è necessaria l'autenticazione EAP-TLS. Una connessione wireless configurata con Active Directory non funzionerà.

L'amministratore di rete genererà un profilo Wi-Fi univoco e un certificato client per ogni computer. L'amministratore di rete decide se usare un certificato separato per ogni dispositivo o un certificato condiviso.

Se si lavora in più di una posizione fisica nell'area di lavoro, l'amministratore di rete potrebbe dover fornire più di un profilo di Wi-Fi specifico del sito e di un certificato per le connessioni wireless.

In una rete aziendale, è consigliabile non modificare le impostazioni nei profili di Wi-Fi forniti dall'amministratore di rete. L'unica modifica che è possibile apportare consiste nelle impostazioni di connessione automatica. Per altre informazioni, vedere [profilo di base](/mem/intune/configuration/wi-fi-settings-windows#basic-profile) in impostazioni Wi-Fi per Windows 10 e dispositivi più recenti.

In un ambiente aziendale con sicurezza elevata, è possibile usare un profilo di rete wireless esistente come modello:

* È possibile scaricare il profilo di rete wireless aziendale dal computer di lavoro. Per istruzioni, vedere [esportare un profilo di Wi-Fi](#export-a-wi-fi-profile), più avanti.

* Se altri utenti dell'organizzazione si connettono già ai dispositivi Azure Stack Edge Mini R su una rete wireless, possono scaricare il profilo di Wi-Fi dal dispositivo. Per istruzioni, vedere [scaricare Wi-Fi profilo](azure-stack-edge-mini-r-manage-wifi.md#download-wi-fi-profile).

## <a name="export-a-wi-fi-profile"></a>Esportare un profilo Wi-Fi

Per esportare un profilo per l'interfaccia Wi-Fi nel computer, seguire questa procedura:

1. Per visualizzare i profili wireless nel computer, nel menu **Start** aprire il prompt dei **comandi** (cmd.exe) e immettere il comando seguente:

   `netsh wlan show profiles`

   Verrà visualizzato un risultato simile al seguente:

   ```dos
   Profiles on interface Wi-Fi:

   Group policy profiles (read only)
   ---------------------------------
       <None>

   User profiles
   -------------
       All User Profile     : ContosoCORP
       All User Profile     : ContosoFTINET
       All User Profile     : GusIS2809
       All User Profile     : GusGuests
       All User Profile     : SeaTacGUEST
       All User Profile     : Boat
   ```

2. Per esportare un profilo, immettere il comando seguente:

   `netsh wlan export profile name=”<profileName>” folder=”<path>\<profileName>"`

   Ad esempio, il comando seguente salva il profilo ContosoFTINET in formato XML nella cartella Downloads per l'utente denominato `gusp` .

   ```dos
   C:\Users\gusp>netsh wlan export profile name="ContosoFTINET" folder=c:Downloads

   Interface profile "ContosoFTINET" is saved in file "c:Downloads\ContosoFTINET.xml" successfully.
   ```

## <a name="add-certificate-wi-fi-profile-to-device"></a>Aggiungi certificato, Wi-Fi profilo al dispositivo

Quando si hanno i profili e i certificati Wi-Fi necessari, seguire questa procedura per configurare il dispositivo Mini R Azure Stack Edge per le connessioni wireless:

1. Per una rete WPA2-Enterprise, caricare i certificati necessari nel dispositivo seguendo le istruzioni riportate in [caricare i certificati](./azure-stack-edge-gpu-manage-certificates.md#upload-certificates).

1. Caricare i profili di Wi-Fi nel dispositivo Mini R e quindi connettersi a esso seguendo le istruzioni riportate in [aggiungere, connettersi a Wi-Fi profilo](./azure-stack-edge-mini-r-manage-wifi.md#add-connect-to-wi-fi-profile).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [configurare la rete per il mini R di Azure stack Edge](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md).
- Informazioni su come [gestire Wi-Fi sul mini R di Azure stack Edge](azure-stack-edge-mini-r-manage-wifi.md).
