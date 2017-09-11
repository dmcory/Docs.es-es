---
title: "Limita la duración de cargas protegidos"
author: rick-anderson
description: 
keywords: "Núcleo de ASP.NET,"
ms.author: riande
manager: wpickett
ms.date: 10/14/2016
ms.topic: article
ms.assetid: 000175e2-10fc-43dd-bfc2-51e004b97b44
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/consumer-apis/limited-lifetime-payloads
ms.openlocfilehash: 4ff13803b328c1e9dd2934c38c88b43f5798de03
ms.sourcegitcommit: 0b6c8e6d81d2b3c161cd375036eecbace46a9707
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/11/2017
---
# <a name="limiting-the-lifetime-of-protected-payloads"></a><span data-ttu-id="dbdc2-103">Limita la duración de cargas protegidos</span><span class="sxs-lookup"><span data-stu-id="dbdc2-103">Limiting the lifetime of protected payloads</span></span>

<span data-ttu-id="dbdc2-104">Existen escenarios donde desea que el desarrollador de aplicaciones para crear una carga protegida que expira tras un período de tiempo establecido.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-104">There are scenarios where the application developer wants to create a protected payload that expires after a set period of time.</span></span> <span data-ttu-id="dbdc2-105">Por ejemplo, la carga protegida podría representar un token de restablecimiento de contraseña que solo debe ser válido durante una hora.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-105">For instance, the protected payload might represent a password reset token that should only be valid for one hour.</span></span> <span data-ttu-id="dbdc2-106">Es posible para el desarrollador puede crear su propio formato de carga que contiene una fecha de expiración incrustados, y los desarrolladores avanzados que desee hacerlo de todos modos, pero para la mayoría de los desarrolladores administrar estos caducidad puede crecer tedioso.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-106">It is certainly possible for the developer to create their own payload format that contains an embedded expiration date, and advanced developers may wish to do this anyway, but for the majority of developers managing these expirations can grow tedious.</span></span>

<span data-ttu-id="dbdc2-107">Para facilitar esta tarea para la audiencia de desarrollador, el paquete Microsoft.AspNetCore.DataProtection.Extensions contiene API de la utilidad para crear cargas de expiran automáticamente tras un período de tiempo establecido.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-107">To make this easier for our developer audience, the package Microsoft.AspNetCore.DataProtection.Extensions contains utility APIs for creating payloads that automatically expire after a set period of time.</span></span> <span data-ttu-id="dbdc2-108">Estas API de bloqueo en el tipo de ITimeLimitedDataProtector.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-108">These APIs hang off of the ITimeLimitedDataProtector type.</span></span>

## <a name="api-usage"></a><span data-ttu-id="dbdc2-109">Uso de la API</span><span class="sxs-lookup"><span data-stu-id="dbdc2-109">API usage</span></span>

<span data-ttu-id="dbdc2-110">La interfaz de ITimeLimitedDataProtector es la interfaz básica para proteger y desproteger tiempo limitado / caduca automáticamente cargas.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-110">The ITimeLimitedDataProtector interface is the core interface for protecting and unprotecting time-limited / self-expiring payloads.</span></span> <span data-ttu-id="dbdc2-111">Para crear una instancia de un ITimeLimitedDataProtector, necesitará primero una instancia de una normal [IDataProtector](overview.md) construido con un propósito específico.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-111">To create an instance of an ITimeLimitedDataProtector, you'll first need an instance of a regular [IDataProtector](overview.md) constructed with a specific purpose.</span></span> <span data-ttu-id="dbdc2-112">Una vez que la instancia IDataProtector está disponible, llame al método de extensión IDataProtector.ToTimeLimitedDataProtector para devolver un protector con capacidades integradas de expiración.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-112">Once the IDataProtector instance is available, call the IDataProtector.ToTimeLimitedDataProtector extension method to get back a protector with built-in expiration capabilities.</span></span>

<span data-ttu-id="dbdc2-113">ITimeLimitedDataProtector expone los siguientes métodos de superficie y extensión de API:</span><span class="sxs-lookup"><span data-stu-id="dbdc2-113">ITimeLimitedDataProtector exposes the following API surface and extension methods:</span></span>

* <span data-ttu-id="dbdc2-114">CreateProtector (propósito de cadena): ITimeLimitedDataProtector esta API es similar a la existente IDataProtectionProvider.CreateProtector en que se puede utilizar para crear [finalidad cadenas](purpose-strings.md) desde un protector de tiempo limitado de raíz.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-114">CreateProtector(string purpose) : ITimeLimitedDataProtector This API is similar to the existing IDataProtectionProvider.CreateProtector in that it can be used to create [purpose chains](purpose-strings.md) from a root time-limited protector.</span></span>

* <span data-ttu-id="dbdc2-115">Proteger (byte [] texto simple, expiración de DateTimeOffset): byte]</span><span class="sxs-lookup"><span data-stu-id="dbdc2-115">Protect(byte[] plaintext, DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="dbdc2-116">Proteger (texto simple de byte [], duración del intervalo de tiempo): byte]</span><span class="sxs-lookup"><span data-stu-id="dbdc2-116">Protect(byte[] plaintext, TimeSpan lifetime) : byte[]</span></span>

* <span data-ttu-id="dbdc2-117">Proteger (como texto simple de byte []): byte]</span><span class="sxs-lookup"><span data-stu-id="dbdc2-117">Protect(byte[] plaintext) : byte[]</span></span>

* <span data-ttu-id="dbdc2-118">Proteger (texto simple de cadena, expiración de DateTimeOffset): cadena</span><span class="sxs-lookup"><span data-stu-id="dbdc2-118">Protect(string plaintext, DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="dbdc2-119">Proteger (texto simple de cadena, duración del intervalo de tiempo): cadena</span><span class="sxs-lookup"><span data-stu-id="dbdc2-119">Protect(string plaintext, TimeSpan lifetime) : string</span></span>

* <span data-ttu-id="dbdc2-120">Proteger (texto simple de la cadena): cadena</span><span class="sxs-lookup"><span data-stu-id="dbdc2-120">Protect(string plaintext) : string</span></span>

<span data-ttu-id="dbdc2-121">Además de los métodos de proteger principales que tienen sólo el texto no cifrado, hay nuevas sobrecargas que permiten especificar la fecha de expiración de la carga.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-121">In addition to the core Protect methods which take only the plaintext, there are new overloads which allow specifying the payload's expiration date.</span></span> <span data-ttu-id="dbdc2-122">La fecha de expiración puede especificarse como una fecha absoluta (a través de un valor DateTimeOffset) o como una hora relativa (desde la hora actual del sistema, a través de un intervalo de tiempo).</span><span class="sxs-lookup"><span data-stu-id="dbdc2-122">The expiration date can be specified as an absolute date (via a DateTimeOffset) or as a relative time (from the current system time, via a TimeSpan).</span></span> <span data-ttu-id="dbdc2-123">Si se llama a una sobrecarga que no toma una expiración, se supone la carga nunca a punto de expirar.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-123">If an overload which doesn't take an expiration is called, the payload is assumed never to expire.</span></span>

* <span data-ttu-id="dbdc2-124">Desproteger (byte [] protectedData, out expiración DateTimeOffset): byte]</span><span class="sxs-lookup"><span data-stu-id="dbdc2-124">Unprotect(byte[] protectedData, out DateTimeOffset expiration) : byte[]</span></span>

* <span data-ttu-id="dbdc2-125">Desproteger (byte [] protectedData): byte]</span><span class="sxs-lookup"><span data-stu-id="dbdc2-125">Unprotect(byte[] protectedData) : byte[]</span></span>

* <span data-ttu-id="dbdc2-126">Desproteger (cadena protectedData, out expiración DateTimeOffset): cadena</span><span class="sxs-lookup"><span data-stu-id="dbdc2-126">Unprotect(string protectedData, out DateTimeOffset expiration) : string</span></span>

* <span data-ttu-id="dbdc2-127">Desproteger (cadena protectedData): cadena</span><span class="sxs-lookup"><span data-stu-id="dbdc2-127">Unprotect(string protectedData) : string</span></span>

<span data-ttu-id="dbdc2-128">Los métodos de Unprotect devuelven los datos protegidos originales.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-128">The Unprotect methods return the original unprotected data.</span></span> <span data-ttu-id="dbdc2-129">Si aún no ha expirado la carga, la expiración absoluta se devuelve como un parámetro junto con los datos protegidos originales de salida opcionales.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-129">If the payload hasn't yet expired, the absolute expiration is returned as an optional out parameter along with the original unprotected data.</span></span> <span data-ttu-id="dbdc2-130">Si la carga ha expirado, todas las sobrecargas del método Unprotect producirá CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-130">If the payload is expired, all overloads of the Unprotect method will throw CryptographicException.</span></span>

>[!WARNING]
> <span data-ttu-id="dbdc2-131">No se recomienda usar estas API para proteger las cargas que requieren la persistencia a largo plazo o indefinida.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-131">It is not advised to use these APIs to protect payloads which require long-term or indefinite persistence.</span></span> <span data-ttu-id="dbdc2-132">"¿Permitirme para que las cargas protegidas ser irrecuperables permanentemente después de un mes?"</span><span class="sxs-lookup"><span data-stu-id="dbdc2-132">"Can I afford for the protected payloads to be permanently unrecoverable after a month?"</span></span> <span data-ttu-id="dbdc2-133">puede actuar como una buena regla general; Si la respuesta es no a los desarrolladores a continuación, deben considerar las API alternativas.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-133">can serve as a good rule of thumb; if the answer is no then developers should consider alternative APIs.</span></span>

<span data-ttu-id="dbdc2-134">El ejemplo siguiente utiliza la [las rutas de código no DI](../configuration/non-di-scenarios.md) para crear instancias del sistema de protección de datos.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-134">The sample below uses the [non-DI code paths](../configuration/non-di-scenarios.md) for instantiating the data protection system.</span></span> <span data-ttu-id="dbdc2-135">Para ejecutar este ejemplo, asegúrese de que ha agregado una referencia al paquete Microsoft.AspNetCore.DataProtection.Extensions en primer lugar.</span><span class="sxs-lookup"><span data-stu-id="dbdc2-135">To run this sample, ensure that you have first added a reference to the Microsoft.AspNetCore.DataProtection.Extensions package.</span></span>

<span data-ttu-id="dbdc2-136">[!code-none[Main](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]</span><span class="sxs-lookup"><span data-stu-id="dbdc2-136">[!code-none[Main](limited-lifetime-payloads/samples/limitedlifetimepayloads.cs)]</span></span>