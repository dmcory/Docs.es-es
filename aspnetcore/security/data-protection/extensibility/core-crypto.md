---
title: "Extensibilidad de criptografía de núcleo"
author: rick-anderson
description: Explica IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer y el generador de nivel superior.
keywords: "Núcleo de ASP.NET, IAuthenticatedEncryptor, IAuthenticatedEncryptorDescriptor, IAuthenticatedEncryptorDescriptorDeserializer"
ms.author: riande
manager: wpickett
ms.date: 8/11/2017
ms.topic: article
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/data-protection/extensibility/core-crypto
ms.openlocfilehash: 8ee4e380b154db7f1736edc793b56258655ddd52
ms.sourcegitcommit: bd05f7ea8f87ad076ef6e8b704698ebcba5ca80c
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2017
---
# <a name="core-cryptography-extensibility"></a><span data-ttu-id="4699d-104">Extensibilidad de criptografía de núcleo</span><span class="sxs-lookup"><span data-stu-id="4699d-104">Core cryptography extensibility</span></span>

<a name=data-protection-extensibility-core-crypto></a>

>[!WARNING]
> <span data-ttu-id="4699d-105">Los tipos que implementan cualquiera de las interfaces siguientes deben ser seguro para subprocesos para distintos llamadores.</span><span class="sxs-lookup"><span data-stu-id="4699d-105">Types that implement any of the following interfaces should be thread-safe for multiple callers.</span></span>

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptor></a>

## <a name="iauthenticatedencryptor"></a><span data-ttu-id="4699d-106">IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="4699d-106">IAuthenticatedEncryptor</span></span>

<span data-ttu-id="4699d-107">El **IAuthenticatedEncryptor** interfaz es el bloque de creación básico del subsistema criptográfico.</span><span class="sxs-lookup"><span data-stu-id="4699d-107">The **IAuthenticatedEncryptor** interface is the basic building block of the cryptographic subsystem.</span></span> <span data-ttu-id="4699d-108">Por lo general, hay un IAuthenticatedEncryptor por clave y la instancia de IAuthenticatedEncryptor contiene todos los material de clave de cifrado y algoritmo información necesaria para realizar operaciones criptográficas.</span><span class="sxs-lookup"><span data-stu-id="4699d-108">There is generally one IAuthenticatedEncryptor per key, and the IAuthenticatedEncryptor instance wraps all cryptographic key material and algorithmic information necessary to perform cryptographic operations.</span></span>

<span data-ttu-id="4699d-109">Como sugiere su nombre, el tipo es responsable de proporcionar servicios de cifrado y descifrado autenticados.</span><span class="sxs-lookup"><span data-stu-id="4699d-109">As its name suggests, the type is responsible for providing authenticated encryption and decryption services.</span></span> <span data-ttu-id="4699d-110">Expone las siguientes API de dos.</span><span class="sxs-lookup"><span data-stu-id="4699d-110">It exposes the following two APIs.</span></span>

* <span data-ttu-id="4699d-111">Descifrar (ArraySegment<byte> texto cifrado, ArraySegment<byte> additionalAuthenticatedData): byte]</span><span class="sxs-lookup"><span data-stu-id="4699d-111">Decrypt(ArraySegment<byte> ciphertext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

* <span data-ttu-id="4699d-112">Cifrar (ArraySegment<byte> texto simple, ArraySegment<byte> additionalAuthenticatedData): byte]</span><span class="sxs-lookup"><span data-stu-id="4699d-112">Encrypt(ArraySegment<byte> plaintext, ArraySegment<byte> additionalAuthenticatedData) : byte[]</span></span>

<span data-ttu-id="4699d-113">El método Encrypt devuelve un blob que incluye el texto sin formato descifra y una etiqueta de autenticación.</span><span class="sxs-lookup"><span data-stu-id="4699d-113">The Encrypt method returns a blob that includes the enciphered plaintext and an authentication tag.</span></span> <span data-ttu-id="4699d-114">La etiqueta de autenticación debe incluir los datos adicionales autenticados (AAD), aunque el AAD propio no necesita poder recuperarse desde la carga final.</span><span class="sxs-lookup"><span data-stu-id="4699d-114">The authentication tag must encompass the additional authenticated data (AAD), though the AAD itself need not be recoverable from the final payload.</span></span> <span data-ttu-id="4699d-115">El método Decrypt valida la etiqueta de autenticación y devuelve la carga deciphered.</span><span class="sxs-lookup"><span data-stu-id="4699d-115">The Decrypt method validates the authentication tag and returns the deciphered payload.</span></span> <span data-ttu-id="4699d-116">Todos los errores (excepto ArgumentNullException y similar) deben homogeneizarse a CryptographicException.</span><span class="sxs-lookup"><span data-stu-id="4699d-116">All failures (except ArgumentNullException and similar) should be homogenized to CryptographicException.</span></span>

> [!NOTE]
> <span data-ttu-id="4699d-117">La propia instancia IAuthenticatedEncryptor realmente no debe contener el material de clave.</span><span class="sxs-lookup"><span data-stu-id="4699d-117">The IAuthenticatedEncryptor instance itself doesn't actually need to contain the key material.</span></span> <span data-ttu-id="4699d-118">Por ejemplo, la implementación puede delegar a un HSM para todas las operaciones.</span><span class="sxs-lookup"><span data-stu-id="4699d-118">For example, the implementation could delegate to an HSM for all operations.</span></span>

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptorfactory></a>
<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor></a>

## <a name="how-to-create-an-iauthenticatedencryptor"></a><span data-ttu-id="4699d-119">Cómo crear un IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="4699d-119">How to create an IAuthenticatedEncryptor</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="4699d-120">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="4699d-120">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="4699d-121">El **IAuthenticatedEncryptorFactory** interfaz representa un tipo que sabe cómo crear un [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instancia.</span><span class="sxs-lookup"><span data-stu-id="4699d-121">The **IAuthenticatedEncryptorFactory** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="4699d-122">La API es como sigue.</span><span class="sxs-lookup"><span data-stu-id="4699d-122">Its API is as follows.</span></span>

* <span data-ttu-id="4699d-123">CreateEncryptorInstance (clave IKey): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="4699d-123">CreateEncryptorInstance(IKey key) : IAuthenticatedEncryptor</span></span>

<span data-ttu-id="4699d-124">Para cualquier instancia de IKey determinada, los sistemas de cifrado autenticados creados por el método CreateEncryptorInstance deben considerarse equivalentes, como en el ejemplo de código siguiente.</span><span class="sxs-lookup"><span data-stu-id="4699d-124">For any given IKey instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorFactory instance and an IKey instance
IAuthenticatedEncryptorFactory factory = ...;
IKey key = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = factory.CreateEncryptorInstance(key);
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = factory.CreateEncryptorInstance(key);
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="4699d-125">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="4699d-125">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="4699d-126">El **IAuthenticatedEncryptorDescriptor** interfaz representa un tipo que sabe cómo crear un [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instancia.</span><span class="sxs-lookup"><span data-stu-id="4699d-126">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to create an [IAuthenticatedEncryptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptor) instance.</span></span> <span data-ttu-id="4699d-127">La API es como sigue.</span><span class="sxs-lookup"><span data-stu-id="4699d-127">Its API is as follows.</span></span>

* <span data-ttu-id="4699d-128">CreateEncryptorInstance(): IAuthenticatedEncryptor</span><span class="sxs-lookup"><span data-stu-id="4699d-128">CreateEncryptorInstance() : IAuthenticatedEncryptor</span></span>

* <span data-ttu-id="4699d-129">ExportToXml(): XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="4699d-129">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

<span data-ttu-id="4699d-130">Al igual que IAuthenticatedEncryptor, una instancia de IAuthenticatedEncryptorDescriptor se supone que va a contener una clave específica.</span><span class="sxs-lookup"><span data-stu-id="4699d-130">Like IAuthenticatedEncryptor, an instance of IAuthenticatedEncryptorDescriptor is assumed to wrap one specific key.</span></span> <span data-ttu-id="4699d-131">Esto significa que para cualquier instancia de IAuthenticatedEncryptorDescriptor determinada, los sistemas de cifrado autenticados creados por el método CreateEncryptorInstance deben considerarse equivalentes, como en el ejemplo de código siguiente.</span><span class="sxs-lookup"><span data-stu-id="4699d-131">This means that for any given IAuthenticatedEncryptorDescriptor instance, any authenticated encryptors created by its CreateEncryptorInstance method should be considered equivalent, as in the below code sample.</span></span>

```csharp
// we have an IAuthenticatedEncryptorDescriptor instance
IAuthenticatedEncryptorDescriptor descriptor = ...;

// get an encryptor instance and perform an authenticated encryption operation
ArraySegment<byte> plaintext = new ArraySegment<byte>(Encoding.UTF8.GetBytes("plaintext"));
ArraySegment<byte> aad = new ArraySegment<byte>(Encoding.UTF8.GetBytes("AAD"));
var encryptor1 = descriptor.CreateEncryptorInstance();
byte[] ciphertext = encryptor1.Encrypt(plaintext, aad);

// get another encryptor instance and perform an authenticated decryption operation
var encryptor2 = descriptor.CreateEncryptorInstance();
byte[] roundTripped = encryptor2.Decrypt(new ArraySegment<byte>(ciphertext), aad);


// the 'roundTripped' and 'plaintext' buffers should be equivalent
```

---

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor></a>

## <a name="iauthenticatedencryptordescriptor-aspnet-core-2x-only"></a><span data-ttu-id="4699d-132">IAuthenticatedEncryptorDescriptor (ASP.NET Core solo 2.x)</span><span class="sxs-lookup"><span data-stu-id="4699d-132">IAuthenticatedEncryptorDescriptor (ASP.NET Core 2.x only)</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="4699d-133">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="4699d-133">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="4699d-134">El **IAuthenticatedEncryptorDescriptor** interfaz representa un tipo que sabe cómo exportar a XML.</span><span class="sxs-lookup"><span data-stu-id="4699d-134">The **IAuthenticatedEncryptorDescriptor** interface represents a type that knows how to export itself to XML.</span></span> <span data-ttu-id="4699d-135">La API es como sigue.</span><span class="sxs-lookup"><span data-stu-id="4699d-135">Its API is as follows.</span></span>

* <span data-ttu-id="4699d-136">ExportToXml(): XmlSerializedDescriptorInfo</span><span class="sxs-lookup"><span data-stu-id="4699d-136">ExportToXml() : XmlSerializedDescriptorInfo</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="4699d-137">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="4699d-137">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

---

## <a name="xml-serialization"></a><span data-ttu-id="4699d-138">Serialización XML</span><span class="sxs-lookup"><span data-stu-id="4699d-138">XML Serialization</span></span>

<span data-ttu-id="4699d-139">La diferencia principal entre IAuthenticatedEncryptor y IAuthenticatedEncryptorDescriptor es que el descriptor sabe cómo crear el sistema de cifrado y proporcionarle argumentos válidos.</span><span class="sxs-lookup"><span data-stu-id="4699d-139">The primary difference between IAuthenticatedEncryptor and IAuthenticatedEncryptorDescriptor is that the descriptor knows how to create the encryptor and supply it with valid arguments.</span></span> <span data-ttu-id="4699d-140">Tenga en cuenta un IAuthenticatedEncryptor cuya implementación se basa en SymmetricAlgorithm y KeyedHashAlgorithm.</span><span class="sxs-lookup"><span data-stu-id="4699d-140">Consider an IAuthenticatedEncryptor whose implementation relies on SymmetricAlgorithm and KeyedHashAlgorithm.</span></span> <span data-ttu-id="4699d-141">Trabajo del sistema de cifrado es consumen estos tipos, pero no conoce necesariamente estos tipos de proceden, por lo que realmente no se puede escribir una descripción de cómo volver a sí mismo si se reinicia la aplicación adecuada.</span><span class="sxs-lookup"><span data-stu-id="4699d-141">The encryptor's job is to consume these types, but it doesn't necessarily know where these types came from, so it can't really write out a proper description of how to recreate itself if the application restarts.</span></span> <span data-ttu-id="4699d-142">El descriptor de actúa como un nivel más alto a partir de esto.</span><span class="sxs-lookup"><span data-stu-id="4699d-142">The descriptor acts as a higher level on top of this.</span></span> <span data-ttu-id="4699d-143">Puesto que el descriptor sabe cómo crear la instancia de sistema de cifrado (p. ej., sabe cómo crear los algoritmos necesarios), puede serializar esa información en forma de XML para que la instancia de sistema de cifrado se puede volver a crear después de restablece una aplicación.</span><span class="sxs-lookup"><span data-stu-id="4699d-143">Since the descriptor knows how to create the encryptor instance (e.g., it knows how to create the required algorithms), it can serialize that knowledge in XML form so that the encryptor instance can be recreated after an application reset.</span></span>

<a name=data-protection-extensibility-core-crypto-exporttoxml></a>

<span data-ttu-id="4699d-144">El descriptor de se puede serializar a través de su rutina de ExportToXml.</span><span class="sxs-lookup"><span data-stu-id="4699d-144">The descriptor can be serialized via its ExportToXml routine.</span></span> <span data-ttu-id="4699d-145">Esta rutina devuelve un XmlSerializedDescriptorInfo que contiene dos propiedades: la representación de XElement de descriptor y el tipo que representa un [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) que puede ser se usa para restablecerse este descriptor dada la XElement correspondiente.</span><span class="sxs-lookup"><span data-stu-id="4699d-145">This routine returns an XmlSerializedDescriptorInfo which contains two properties: the XElement representation of the descriptor and the Type which represents an [IAuthenticatedEncryptorDescriptorDeserializer](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer) which can be used to resurrect this descriptor given the corresponding XElement.</span></span>

<span data-ttu-id="4699d-146">El descriptor serializado puede contener información confidencial como material de clave de cifrado.</span><span class="sxs-lookup"><span data-stu-id="4699d-146">The serialized descriptor may contain sensitive information such as cryptographic key material.</span></span> <span data-ttu-id="4699d-147">El sistema de protección de datos tiene compatibilidad integrada para cifrar la información antes de que se conservan en el almacenamiento.</span><span class="sxs-lookup"><span data-stu-id="4699d-147">The data protection system has built-in support for encrypting information before it's persisted to storage.</span></span> <span data-ttu-id="4699d-148">Para aprovechar estas características, el descriptor debería marcar el elemento que contiene información confidencial con el nombre del atributo "requiresEncryption" (xmlns "http://schemas.asp.net/2015/03/dataProtection"), valor "true".</span><span class="sxs-lookup"><span data-stu-id="4699d-148">To take advantage of this, the descriptor should mark the element which contains sensitive information with the attribute name "requiresEncryption" (xmlns "http://schemas.asp.net/2015/03/dataProtection"), value "true".</span></span>

>[!TIP]
> <span data-ttu-id="4699d-149">Hay una API auxiliar para establecer este atributo.</span><span class="sxs-lookup"><span data-stu-id="4699d-149">There's a helper API for setting this attribute.</span></span> <span data-ttu-id="4699d-150">Llame al método de extensión que XElement.markasrequiresencryption() ubicado en el espacio de nombres Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span><span class="sxs-lookup"><span data-stu-id="4699d-150">Call the extension method XElement.MarkAsRequiresEncryption() located in namespace Microsoft.AspNetCore.DataProtection.AuthenticatedEncryption.ConfigurationModel.</span></span>

<span data-ttu-id="4699d-151">También puede haber casos donde el descriptor serializado no contiene información confidencial.</span><span class="sxs-lookup"><span data-stu-id="4699d-151">There can also be cases where the serialized descriptor doesn't contain sensitive information.</span></span> <span data-ttu-id="4699d-152">Considere la posibilidad de nuevo el caso de una clave criptográfica que se almacenan en un HSM.</span><span class="sxs-lookup"><span data-stu-id="4699d-152">Consider again the case of a cryptographic key stored in an HSM.</span></span> <span data-ttu-id="4699d-153">El material de clave no se puede escribir el descriptor al serializar a sí mismo porque el HSM no expondrá el material en formato de texto simple.</span><span class="sxs-lookup"><span data-stu-id="4699d-153">The descriptor cannot write out the key material when serializing itself since the HSM will not expose the material in plaintext form.</span></span> <span data-ttu-id="4699d-154">En su lugar, puede escribir el descriptor de la versión de clave ajusta de la clave (si el HSM permite la exportación de este modo) o el identificador único del HSM para la clave.</span><span class="sxs-lookup"><span data-stu-id="4699d-154">Instead, the descriptor might write out the key-wrapped version of the key (if the HSM allows export in this fashion) or the HSM's own unique identifier for the key.</span></span>

<a name=data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptordeserializer></a>

## <a name="iauthenticatedencryptordescriptordeserializer"></a><span data-ttu-id="4699d-155">IAuthenticatedEncryptorDescriptorDeserializer</span><span class="sxs-lookup"><span data-stu-id="4699d-155">IAuthenticatedEncryptorDescriptorDeserializer</span></span>

<span data-ttu-id="4699d-156">El **IAuthenticatedEncryptorDescriptorDeserializer** interfaz representa un tipo que sabe cómo deserializar una instancia de IAuthenticatedEncryptorDescriptor desde un XElement.</span><span class="sxs-lookup"><span data-stu-id="4699d-156">The **IAuthenticatedEncryptorDescriptorDeserializer** interface represents a type that knows how to deserialize an IAuthenticatedEncryptorDescriptor instance from an XElement.</span></span> <span data-ttu-id="4699d-157">Expone un único método:</span><span class="sxs-lookup"><span data-stu-id="4699d-157">It exposes a single method:</span></span>

* <span data-ttu-id="4699d-158">ImportFromXml (elemento de XElement): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="4699d-158">ImportFromXml(XElement element) : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="4699d-159">El método ImportFromXml toma el XElement que devolvió [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) y crea un equivalente de la IAuthenticatedEncryptorDescriptor original.</span><span class="sxs-lookup"><span data-stu-id="4699d-159">The ImportFromXml method takes the XElement that was returned by [IAuthenticatedEncryptorDescriptor.ExportToXml](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-exporttoxml) and creates an equivalent of the original IAuthenticatedEncryptorDescriptor.</span></span>

<span data-ttu-id="4699d-160">Tipos que implementan IAuthenticatedEncryptorDescriptorDeserializer deben tener uno de los dos constructores públicos siguientes:</span><span class="sxs-lookup"><span data-stu-id="4699d-160">Types which implement IAuthenticatedEncryptorDescriptorDeserializer should have one of the following two public constructors:</span></span>

* <span data-ttu-id="4699d-161">.ctor(IServiceProvider)</span><span class="sxs-lookup"><span data-stu-id="4699d-161">.ctor(IServiceProvider)</span></span>

* <span data-ttu-id="4699d-162">.ctor()</span><span class="sxs-lookup"><span data-stu-id="4699d-162">.ctor()</span></span>

> [!NOTE]
> <span data-ttu-id="4699d-163">IServiceProvider pasado al constructor puede ser null.</span><span class="sxs-lookup"><span data-stu-id="4699d-163">The IServiceProvider passed to the constructor may be null.</span></span>

## <a name="the-top-level-factory"></a><span data-ttu-id="4699d-164">El generador de nivel superior</span><span class="sxs-lookup"><span data-stu-id="4699d-164">The top-level factory</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="4699d-165">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="4699d-165">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="4699d-166">El **AlgorithmConfiguration** clase representa un tipo que sabe cómo crear [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instancias.</span><span class="sxs-lookup"><span data-stu-id="4699d-166">The **AlgorithmConfiguration** class represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="4699d-167">Expone una sola API.</span><span class="sxs-lookup"><span data-stu-id="4699d-167">It exposes a single API.</span></span>

* <span data-ttu-id="4699d-168">CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="4699d-168">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="4699d-169">Considerar AlgorithmConfiguration como el generador de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="4699d-169">Think of AlgorithmConfiguration as the top-level factory.</span></span> <span data-ttu-id="4699d-170">La configuración actúa como una plantilla.</span><span class="sxs-lookup"><span data-stu-id="4699d-170">The configuration serves as a template.</span></span> <span data-ttu-id="4699d-171">Encapsula información algorítmica (p. ej., esta configuración produce descriptores con una clave maestra de AES-128-GCM), pero todavía no está asociado a una clave específica.</span><span class="sxs-lookup"><span data-stu-id="4699d-171">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it is not yet associated with a specific key.</span></span>

<span data-ttu-id="4699d-172">Cuando se llama a CreateNewDescriptor, material de clave nueva se crea únicamente para esta llamada y se genera un nuevo IAuthenticatedEncryptorDescriptor que ajusta este material de clave y la información algorítmica necesarios para consumir el material.</span><span class="sxs-lookup"><span data-stu-id="4699d-172">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="4699d-173">El material de clave podría creó en software (y se mantienen en la memoria), podría ser crea y mantiene dentro de un HSM y así sucesivamente.</span><span class="sxs-lookup"><span data-stu-id="4699d-173">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="4699d-174">El punto fundamental es que las dos llamadas a CreateNewDescriptor nunca deben crearse instancias de IAuthenticatedEncryptorDescriptor equivalente.</span><span class="sxs-lookup"><span data-stu-id="4699d-174">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="4699d-175">El tipo de AlgorithmConfiguration actúa como punto de entrada para las rutinas de creación de claves como [reversión de clave automática](../implementation/key-management.md#data-protection-implementation-key-management).</span><span class="sxs-lookup"><span data-stu-id="4699d-175">The AlgorithmConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#data-protection-implementation-key-management).</span></span> <span data-ttu-id="4699d-176">Para cambiar la implementación de todas las claves futuras, establezca la propiedad AuthenticatedEncryptorConfiguration en KeyManagementOptions.</span><span class="sxs-lookup"><span data-stu-id="4699d-176">To change the implementation for all future keys, set the AuthenticatedEncryptorConfiguration property in KeyManagementOptions.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="4699d-177">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="4699d-177">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="4699d-178">El **IAuthenticatedEncryptorConfiguration** interfaz representa un tipo que sabe cómo crear [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instancias.</span><span class="sxs-lookup"><span data-stu-id="4699d-178">The **IAuthenticatedEncryptorConfiguration** interface represents a type which knows how to create [IAuthenticatedEncryptorDescriptor](xref:security/data-protection/extensibility/core-crypto#data-protection-extensibility-core-crypto-iauthenticatedencryptordescriptor) instances.</span></span> <span data-ttu-id="4699d-179">Expone una sola API.</span><span class="sxs-lookup"><span data-stu-id="4699d-179">It exposes a single API.</span></span>

* <span data-ttu-id="4699d-180">CreateNewDescriptor(): IAuthenticatedEncryptorDescriptor</span><span class="sxs-lookup"><span data-stu-id="4699d-180">CreateNewDescriptor() : IAuthenticatedEncryptorDescriptor</span></span>

<span data-ttu-id="4699d-181">Considerar IAuthenticatedEncryptorConfiguration como el generador de nivel superior.</span><span class="sxs-lookup"><span data-stu-id="4699d-181">Think of IAuthenticatedEncryptorConfiguration as the top-level factory.</span></span> <span data-ttu-id="4699d-182">La configuración actúa como una plantilla.</span><span class="sxs-lookup"><span data-stu-id="4699d-182">The configuration serves as a template.</span></span> <span data-ttu-id="4699d-183">Encapsula información algorítmica (p. ej., esta configuración produce descriptores con una clave maestra de AES-128-GCM), pero todavía no está asociado a una clave específica.</span><span class="sxs-lookup"><span data-stu-id="4699d-183">It wraps algorithmic information (e.g., this configuration produces descriptors with an AES-128-GCM master key), but it is not yet associated with a specific key.</span></span>

<span data-ttu-id="4699d-184">Cuando se llama a CreateNewDescriptor, material de clave nueva se crea únicamente para esta llamada y se genera un nuevo IAuthenticatedEncryptorDescriptor que ajusta este material de clave y la información algorítmica necesarios para consumir el material.</span><span class="sxs-lookup"><span data-stu-id="4699d-184">When CreateNewDescriptor is called, fresh key material is created solely for this call, and a new IAuthenticatedEncryptorDescriptor is produced which wraps this key material and the algorithmic information required to consume the material.</span></span> <span data-ttu-id="4699d-185">El material de clave podría creó en software (y se mantienen en la memoria), podría ser crea y mantiene dentro de un HSM y así sucesivamente.</span><span class="sxs-lookup"><span data-stu-id="4699d-185">The key material could be created in software (and held in memory), it could be created and held within an HSM, and so on.</span></span> <span data-ttu-id="4699d-186">El punto fundamental es que las dos llamadas a CreateNewDescriptor nunca deben crearse instancias de IAuthenticatedEncryptorDescriptor equivalente.</span><span class="sxs-lookup"><span data-stu-id="4699d-186">The crucial point is that any two calls to CreateNewDescriptor should never create equivalent IAuthenticatedEncryptorDescriptor instances.</span></span>

<span data-ttu-id="4699d-187">El tipo de IAuthenticatedEncryptorConfiguration actúa como punto de entrada para las rutinas de creación de claves como [reversión de clave automática](../implementation/key-management.md#data-protection-implementation-key-management).</span><span class="sxs-lookup"><span data-stu-id="4699d-187">The IAuthenticatedEncryptorConfiguration type serves as the entry point for key creation routines such as [automatic key rolling](../implementation/key-management.md#data-protection-implementation-key-management).</span></span> <span data-ttu-id="4699d-188">Para cambiar la implementación de todas las claves futuras, registrar un singleton IAuthenticatedEncryptorConfiguration en el contenedor de servicios.</span><span class="sxs-lookup"><span data-stu-id="4699d-188">To change the implementation for all future keys, register a singleton IAuthenticatedEncryptorConfiguration in the service container.</span></span>

---