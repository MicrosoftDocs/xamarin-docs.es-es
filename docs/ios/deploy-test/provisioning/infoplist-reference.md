---
title: Referencia de Info.plist para Xamarin.iOS
description: En este documento se describen varios pares clave-valor que se pueden establecer en el archivo Info.plist de una aplicación Xamarin.iOS. Estas claves son necesarias si la aplicación lleva a cabo tareas específicas, como obtener acceso a la ubicación, las fotos, el micrófono o la cámara.
ms.prod: xamarin
ms.assetid: 944DFDB5-ADBA-4D6E-984C-5AEC19A1CC57
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 01/18/2017
ms.openlocfilehash: 2a61a4a313fa23b5cdc629279bee39f1eed792cc
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70120502"
---
# <a name="infoplist-reference-for-xamarinios"></a>Referencia de Info.plist para Xamarin.iOS

Para obtener más información sobre cómo trabajar con claves de Info.Plist, lea la guía [Trabajar con seguridad y privacidad](~/ios/app-fundamentals/security-privacy.md). 

## <a name="location"></a>Ubicación 

Para obtener acceso a la ubicación del usuario, también se deben efectuar modificaciones en Info.plist. Se deben establecer las siguientes claves relacionadas con los datos de ubicación: 

- **NSLocationWhenInUseUsageDescription**: Al obtener acceso a la ubicación del usuario mientras interactúa con la aplicación. 
- **NSLocationAlwaysUsageDescription**: Al obtener acceso la aplicación a la ubicación del usuario en segundo plano.

## <a name="photos"></a>Fotos 

NSPhotoLibraryUsageDescription  

## <a name="contacts"></a>Contactos 

NSContactsUsageDescription 

## <a name="calendar-data"></a>Datos de calendario 
    
NSCalendarsUsageDescription 

## <a name="reminder-data"></a>Datos de aviso 
    
NSRemindersUsageDescription 

## <a name="bluetooth-peripherals"></a>Periféricos Bluetooth 
    
NSBluetoothPeripheralUsageDescription 

## <a name="microphone"></a>Micrófono 

NSMicrophoneUsageDescription 

## <a name="camera"></a>Cámara 
    
NSCameraUsageDescription 

## <a name="reading-healthkit"></a>Leer HealthKit  

NSHealthShareUsageDescription 

NSHealthUpdateUsageDescription 

## <a name="background-modes"></a>Modos en segundo plano 
    
UIBackgroundModes 

## <a name="homekit"></a>HomeKit 

NSHomeKitUsageDescription 


## <a name="related-links"></a>Vínculos relacionados

- [Guía de referencia de Apple.](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW10)
