---
title: Code coverage di Microsoft Azure Maps Weather Services (anteprima)
description: Informazioni sulla copertura di Microsoft Azure Maps Weather Services (anteprima)
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: references_regions
manager: philmea
ms.openlocfilehash: 6c4e9eb765a72b7a0b495f81a954b484ef6aa2b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96905486"
---
# <a name="azure-maps-weather-services-preview-coverage"></a>Code coverage di servizi meteo di Azure Maps (anteprima)

> [!IMPORTANT]
> I servizi Meteo di Mappe di Azure sono attualmente disponibili in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


Questo articolo fornisce informazioni sulla copertura per i [Servizi Meteo](/rest/api/maps/weather)di Azure maps. Azure Maps Weather Data Services restituisce dettagli quali riquadri radar, condizioni meteo correnti, previsioni meteorologiche e il meteo lungo una route.

Le mappe di Azure non hanno lo stesso livello di informazioni e accuratezza per tutti i paesi e le aree geografiche.

La tabella seguente fornisce informazioni sul tipo di informazioni meteorologiche che è possibile richiedere da ogni paese/area geografica.

| Simbolo | Significato |
|--------|---------|
|*       |Vengono illustrate le condizioni correnti, le previsioni orarie, le previsioni trimestrali, le previsioni giornaliere, il meteo lungo il percorso e gli indici giornalieri. |


## <a name="americas"></a>Americhe

| Paese/Area geografica              |  Riquadri satellite | Previsioni del minuto, riquadri radar | Avvisi meteorologici gravi | Altri |
|-----------------------------|:----------------:|:-----------------:|:-----------------:|:--------:|  
| Anguilla                                 | ✓ |   | |  ✓| 
| Antartide                               | ✓ |   | |✓|
| Antigua e Barbuda                      | ✓ |   | |✓| 
| Argentina                                | ✓ |   | |✓| 
| Aruba                                    | ✓ |   | |✓| 
| Bahamas                                  | ✓ |   | |✓| 
| Barbados                                 | ✓ |   | |✓| 
| Belize                                   | ✓ |   | |✓| 
| Bermuda                                  | ✓ |   | |✓| 
| Bolivia                                  | ✓ |   | |✓| 
| Bonaire                                  | ✓ |   | |✓| 
| Brasile                                   | ✓ |   | ✓ |✓| 
| Isole Vergini Britanniche                   | ✓ |   | |✓| 
| Canada                                   | ✓ | ✓ | ✓ | ✓| 
| Isole Cayman                           | ✓ |   | |✓| 
| Cile                                    | ✓ |   | |✓| 
| Colombia                                 | ✓ |   | |✓| 
| Costa Rica                               | ✓ |   | |✓| 
| Cuba                                     | ✓ |   | |✓| 
| Curaçao                                  | ✓ |   | |✓| 
| Dominica                                 | ✓ |   | |✓| 
| Repubblica Dominicana                       | ✓ |   | |✓| 
| Ecuador                                  | ✓ |   | |✓| 
| El Salvador                              | ✓ |   | |✓| 
| Isole Falkland                         | ✓ |   | |✓| 
| Guayana francese                            | ✓ |   | |✓| 
| Groenlandia                                | ✓ |   | |✓| 
| Grenada                                  | ✓ |   | |✓| 
| Guadalupa                               | ✓ |   | |✓| 
| Guatemala                                | ✓ |   | |✓| 
| Guyana                                   | ✓ |   | |✓| 
| Haiti                                    | ✓ |   | |✓| 
| Honduras                                 | ✓ |   | |✓| 
| Giamaica                                  | ✓ |   | |✓| 
| Martinica                               | ✓ |   | |✓| 
| Messico                                   | ✓ |   | |✓| 
| Montserrat                               | ✓ |   | |✓| 
| Nicaragua                                | ✓ |   | |✓| 
| Panama                                   | ✓ |   | |✓| 
| Paraguay                                 | ✓ |   | |✓| 
| Perù                                     | ✓ |   | |✓| 
| Portorico                              | ✓ |   | ✓ |✓| 
| Saint Barthélemy                         | ✓ |   | |✓| 
| Saint Kitts e Nevis                    | ✓ |   | |✓| 
| Saint Lucia                              | ✓ |   | |✓| 
| Saint Martin                             | ✓ |   | |✓| 
| Saint Pierre e Miquelon                | ✓ |   | |✓| 
| Saint Vincent e Grenadine         | ✓ |   | |✓| 
| Sint Eustatius                           | ✓ |   | |✓|  
| Sint Maarten                             | ✓ |   | |✓| 
| Georgia del Sud e Isole Sandwich Australi | ✓ |   | |✓| 
| Suriname                                 | ✓ |   | |✓| 
| Trinidad e Tobago                      | ✓ |   | |✓| 
| Isole Turks e Caicos                 | ✓ |   | |✓| 
| Stati Uniti Altre isole americane del Pacifico                    | ✓ |   | |✓| 
| U.S. Vergini Americane                      | ✓ |   | ✓|✓| 
| Stati Uniti                            | ✓ | ✓ | ✓| ✓| 
| Uruguay                                  | ✓ |   | |✓| 
| Venezuela                                | ✓ |   | |✓| 


## <a name="middle-east-and-africa"></a>Medio Oriente e Africa

| Paese/Area geografica              |  Riquadri satellite | Previsioni del minuto, riquadri radar | Avvisi meteorologici gravi | Altri | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:| 
| Algeria                     | ✓               |                              |     |  ✓| 
| Angola                      | ✓               |                              |     |  ✓| 
| Bahrein                     | ✓               |                              |     |  ✓| 
| Benin                       | ✓               |                              |     |  ✓| 
| Botswana                    | ✓               |                              |     |  ✓| 
| Isola Bouvet               | ✓               |                              |     |  ✓| 
| Burkina Faso                | ✓               |                              |     |  ✓| 
| Burundi                     | ✓               |                              |     |  ✓| 
| Camerun                    | ✓               |                              |     |  ✓| 
| Cabo Verde                  | ✓               |                              |     |  ✓| 
| Repubblica Centrafricana    | ✓               |                              |     |  ✓| 
| Ciad                        | ✓               |                              |     |  ✓| 
| Comore                     | ✓               |                              |     |  ✓| 
| Congo (RDC)                 | ✓               |                              |     |  ✓|
| Côte d'Ivoire (Costa d'Avorio)               | ✓               |                              |     |  ✓| 
| Gibuti                    | ✓               |                              |     |  ✓| 
| Egitto                       | ✓               |                              |     |  ✓| 
| Guinea Equatoriale           | ✓               |                              |     |  ✓| 
| Eritrea                     | ✓               |                              |     |  ✓| 
| eSwatini                    | ✓               |                              |     |  ✓| 
| Etiopia                    | ✓               |                              |     |  ✓| 
| Terre Australi Francesi | ✓               |                              |     |  ✓| 
| Gabon                       | ✓               |                              |     |  ✓| 
| Gambia                      | ✓               |                              |     |  ✓| 
| Ghana                       | ✓               |                              |     |  ✓| 
| Guinea                      | ✓               |                              |     |  ✓| 
| Guinea-Bissau               | ✓               |                              |     |  ✓| 
| Iran                        | ✓               |                              |     |  ✓| 
| Iraq                        | ✓               |                              |     |  ✓| 
| Israele                      | ✓               |                              |   ✓   |  ✓| 
| Giordania                      | ✓               |                              |     |  ✓| 
| Kenya                       | ✓               |                              |     |  ✓| 
| Kuwait                      | ✓               |                              |     |  ✓| 
| Libano                     | ✓               |                              |     |  ✓| 
| Lesotho                     | ✓               |                              |     |  ✓| 
| Liberia                     | ✓               |                              |     |  ✓| 
| Libia                       | ✓               |                              |     |  ✓| 
| Madagascar                  | ✓               |                              |     |  ✓| 
| Malawi                      | ✓               |                              |     |  ✓| 
| Mali                        | ✓               |                              |     |  ✓| 
| Mauritania                  | ✓               |                              |     |  ✓| 
| Mauritius                   | ✓               |                              |     |  ✓| 
| Mayotte                     | ✓               |                              |     |  ✓| 
| Marocco                     | ✓               |                              |     |  ✓| 
| Mozambico                  | ✓               |                              |     |  ✓| 
| Namibia                     | ✓               |                              |     |  ✓| 
| Niger                       | ✓               |                              |     |  ✓| 
| Nigeria                     | ✓               |                              |     |  ✓| 
| Oman                        | ✓               |                              |     |  ✓| 
| Autorità Palestinese       | ✓               |                              |     |  ✓| 
| Qatar                       | ✓               |                              |     |  ✓| 
| Riunione                     | ✓               |                              |     |  ✓| 
| Ruanda                      | ✓               |                              |     |  ✓| 
| Sant'Elena, Ascensione, Tristan da Cunha        | ✓               |            |     |  ✓| 
| São Tomé e Príncipe       | ✓               |                              |     |  ✓| 
| Arabia Saudita                | ✓               |                              |     |  ✓| 
| Senegal                     | ✓               |                              |     |  ✓| 
| Seychelles                  | ✓               |                              |     |  ✓| 
| Sierra Leone                | ✓               |                              |     |  ✓| 
| Somalia                     | ✓               |                              |     |  ✓| 
| Sudafrica                | ✓               |                              |     |  ✓| 
| Sud Sudan                 | ✓               |                              |     |  ✓| 
| Sudan                       | ✓               |                              |     |  ✓| 
| Siria                       | ✓               |                              |     |  ✓| 
| Tanzania                    | ✓               |                              |     |  ✓| 
| Togo                        | ✓               |                              |     |  ✓| 
| Tunisia                     | ✓               |                              |     |  ✓| 
| Uganda                      | ✓               |                              |     |  ✓| 
| Emirati Arabi Uniti        | ✓               |                              |     |  ✓| 
| Yemen                       | ✓               |                              |     |  ✓| 
| Zambia                      | ✓               |                              |     |  ✓| 
| Zimbabwe                    | ✓               |                              |     |  ✓| 


## <a name="asia-pacific"></a>Asia Pacifico

| Paese/Area geografica              |  Riquadri satellite | Previsioni del minuto, riquadri radar | Avvisi meteorologici gravi | Altri |
|-----------------------------|:----------------:|:-----------------:|:--------:|  :--------:|
| Afghanistan                       | ✓ |   | | ✓| 
| Samoa americane                    | ✓ |   |  ✓| ✓| 
| Australia                         | ✓ | ✓ |  ✓ | ✓| 
| Bangladesh                        | ✓ |   | | ✓| 
| Bhutan                            | ✓ |   | | ✓| 
| Territorio britannico dell'Oceano indiano    | ✓ |   | | ✓| 
| Brunei                            | ✓ |   | | ✓| 
| Cambogia                          | ✓ |   | | ✓| 
| Cina                             | ✓ | ✓ |  ✓ | ✓| 
| Isola Christmas                  | ✓ |   | | ✓| 
| Isole Cocos (Keeling)           | ✓ |   | | ✓| 
| Isole Cook                      | ✓ |   | | ✓| 
| Figi                              | ✓ |   | | ✓| 
| Polinesia Francese                  | ✓ |   | | ✓| 
| Guam                              | ✓ |   |  ✓| ✓| 
| Heard e McDonald | ✓ |   | | ✓| 
| RAS di Hong Kong                     | ✓ |   | | ✓| 
| India                             | ✓ |   | | ✓| 
| Indonesia                         | ✓ |   | | ✓| 
| Giappone                             | ✓ | ✓ |  ✓| ✓| 
| Kazakhstan                        | ✓ |   | | ✓| 
| Kiribati                          | ✓ |   | | ✓| 
| Corea del Sud                             | ✓ | ✓ | ✓ |  ✓| 
| Kirghizistan                        | ✓ |   | | ✓| 
| Laos                              | ✓ |   | | ✓| 
| RAS di Macao                         | ✓ |   | | ✓| 
| Malaysia                          | ✓ |   | | ✓| 
| Maldive                          | ✓ |   | | ✓| 
| Isole Marshall                  | ✓ |   | ✓ | ✓| 
| Micronesia                        | ✓ |   | ✓ | ✓| 
| Mongolia                          | ✓ |   | | ✓| 
| Myanmar                           | ✓ |   | | ✓| 
| Nauru                             | ✓ |   | | ✓| 
| Nepal                             | ✓ |   | | ✓| 
| Nuova Caledonia                     | ✓ |   | | ✓| 
| Nuova Zelanda                       | ✓ |   | ✓ | ✓| 
| Niue                              | ✓ |   | | ✓| 
| Isola Norfolk                    | ✓ |   | | ✓| 
| Corea del Nord                       | ✓ |   | | ✓| 
| Isole Marianne Settentrionali          | ✓ |   | ✓ | ✓| 
| Pakistan                          | ✓ |   | | ✓| 
| Palau                             | ✓ |   | ✓ | ✓| 
| Papua Nuova Guinea                  | ✓ |   | | ✓| 
| Filippine                       | ✓ |   | ✓ | ✓| 
| Isole Pitcairn                  | ✓ |   | | ✓| 
| Samoa                             | ✓ |   | | ✓| 
| Singapore                         | ✓ |   | | ✓| 
| Isole Salomone                   | ✓ |   | | ✓| 
| Sri Lanka                         | ✓ |   | | ✓| 
| Taiwan                            | ✓ |   | | ✓| 
| Tagikistan                        | ✓ |   | | ✓| 
| Thailandia                          | ✓ |   | | ✓| 
| Timor Leste                       | ✓ |   | | ✓| 
| Tokelau                           | ✓ |   | | ✓| 
| Tonga                             | ✓ |   | | ✓| 
| Turkmenistan                      | ✓ |   | | ✓| 
| Tuvalu                            | ✓ |   | | ✓| 
| Uzbekistan                        | ✓ |   | | ✓| 
| Vanuatu                           | ✓ |   | | ✓| 
| Vietnam                           | ✓ |   | | ✓| 
| Wallis e Futuna                 | ✓ |   | | ✓| 


## <a name="europe"></a>Europa

| Paese/Area geografica              |  Riquadri satellite | Previsioni del minuto, riquadri radar | Avvisi meteorologici gravi | Altri | 
|-----------------------------|:----------------:|:-----------------:|:--------:|:--------:|
| Albania                | ✓ |   | | ✓| 
| Andorra                | ✓ |   | ✓ | ✓| 
| Armenia                | ✓ |   | | ✓| 
| Austria                | ✓ | ✓ | ✓ | ✓|
| Azerbaigian             | ✓ |   | | ✓| 
| Bielorussia                | ✓ |   | | ✓| 
| Belgio                | ✓ | ✓ |  ✓| ✓| 
| Bosnia ed Erzegovina | ✓ | ✓ | ✓ | ✓| 
| Bulgaria               | ✓ |   |  ✓| ✓| 
| Croazia                | ✓ | ✓ |  ✓| ✓| 
| Cipro                 | ✓ |   | ✓ | ✓| 
| Cechia                | ✓ | ✓ | ✓ | ✓| 
| Danimarca                | ✓ | ✓ | ✓ | ✓| 
| Estonia                | ✓ | ✓ |  ✓| ✓| 
| Isole Fær Øer          | ✓ |   | | ✓| 
| Finlandia                | ✓ | ✓ | ✓ | ✓| 
| Francia                 | ✓ | ✓ | ✓ | ✓| 
| Georgia                | ✓ |   | | ✓| 
| Germania                | ✓ | ✓ | ✓ | ✓| 
| Gibilterra              | ✓ | ✓ | | ✓| 
| Grecia                 | ✓ |   |  ✓| ✓| 
| Guernsey               | ✓ |   | | ✓| 
| Ungheria                | ✓ | ✓ |  ✓| ✓| 
| Islanda                | ✓ |   | ✓ | ✓| 
| Irlanda                | ✓ | ✓ |  ✓| ✓| 
| Italia                  | ✓ |   |  ✓| ✓|
| Isola di Man            | ✓ |   | | ✓| 
| Jan Mayen              | ✓ |   | | ✓| 
| Jersey                 | ✓ |   | | ✓| 
| Kosovo                 | ✓ |   |  ✓| ✓| 
| Lettonia                 | ✓ |   | ✓ | ✓| 
| Liechtenstein          | ✓ | ✓ |  ✓| ✓| 
| Lituania              | ✓ |   | ✓ | ✓| 
| Lussemburgo             | ✓ | ✓ |  ✓| ✓| 
| Macedonia del Nord        | ✓ |   | ✓ | ✓| 
| Malta                  | ✓ |   | ✓ | ✓| 
| Moldova                | ✓ | ✓ | ✓ | ✓| 
| Monaco                 | ✓ | ✓ |  ✓| ✓| 
| Montenegro             | ✓ | ✓ |  ✓| ✓| 
| Paesi Bassi            | ✓ | ✓ |  ✓| ✓| 
| Norvegia                 | ✓ | ✓ |  ✓| ✓| 
| Polonia                 | ✓ | ✓ |  ✓| ✓| 
| Portogallo               | ✓ | ✓ |  ✓| ✓| 
| Romania                | ✓ | ✓ |  ✓| ✓| 
| Russia                 | ✓ |   |  ✓| ✓| 
| San Marino             | ✓ |   |  ✓| ✓| 
| Serbia                 | ✓ | ✓ |  ✓| ✓| 
| Slovacchia               | ✓ | ✓ |  ✓| ✓| 
| Slovenia               | ✓ | ✓ |  ✓| ✓| 
| Spagna                  | ✓ | ✓ |  ✓| ✓| 
| Svalbard               | ✓ |   | | ✓|
| Svezia                 | ✓ | ✓ |  ✓| ✓| 
| Svizzera            | ✓ | ✓ | ✓| ✓| 
| Turchia                 | ✓ |   | | ✓| 
| Ucraina                | ✓ |   | | ✓| 
| Regno Unito         | ✓ | ✓ | ✓| ✓| 
| Città del Vaticano           | ✓ |   |✓ | ✓|