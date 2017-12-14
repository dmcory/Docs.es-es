---
uid: web-forms/overview/ajax-control-toolkit/animation/animating-in-response-to-user-interaction-vb
title: "Animar en respuesta a la interacción del usuario (VB) | Documentos de Microsoft"
author: wenz
description: "El control de animación en el Kit de herramientas de Control de AJAX de ASP.NET no es simplemente un control sino un marco completo para agregar animaciones a un control. Las animaciones pueden estrellas..."
ms.author: aspnetcontent
manager: wpickett
ms.date: 06/02/2008
ms.topic: article
ms.assetid: c8204c05-ec27-40fe-933d-88e4e727a482
ms.technology: dotnet-webforms
ms.prod: .net-framework
msc.legacyurl: /web-forms/overview/ajax-control-toolkit/animation/animating-in-response-to-user-interaction-vb
msc.type: authoredcontent
ms.openlocfilehash: 3219e9d126b3225bfc78d08fb3ac7ef4cc3dca75
ms.sourcegitcommit: 9a9483aceb34591c97451997036a9120c3fe2baf
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
<a name="animating-in-response-to-user-interaction-vb"></a><span data-ttu-id="2cba4-104">Animar en respuesta a la interacción del usuario (VB)</span><span class="sxs-lookup"><span data-stu-id="2cba4-104">Animating in Response To User Interaction (VB)</span></span>
====================
<span data-ttu-id="2cba4-105">por [Christian Wenz](https://github.com/wenz)</span><span class="sxs-lookup"><span data-stu-id="2cba4-105">by [Christian Wenz](https://github.com/wenz)</span></span>

<span data-ttu-id="2cba4-106">[Descargar código](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation6.vb.zip) o [descarga de PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation6VB.pdf)</span><span class="sxs-lookup"><span data-stu-id="2cba4-106">[Download Code](http://download.microsoft.com/download/f/9/a/f9a26acd-8df4-4484-8a18-199e4598f411/Animation6.vb.zip) or [Download PDF](http://download.microsoft.com/download/6/7/1/6718d452-ff89-4d3f-a90e-c74ec2d636a3/animation6VB.pdf)</span></span>

> <span data-ttu-id="2cba4-107">El control de animación en el Kit de herramientas de Control de AJAX de ASP.NET no es simplemente un control sino un marco completo para agregar animaciones a un control.</span><span class="sxs-lookup"><span data-stu-id="2cba4-107">The Animation control in the ASP.NET AJAX Control Toolkit is not just a control but a whole framework to add animations to a control.</span></span> <span data-ttu-id="2cba4-108">Las animaciones pueden iniciar automáticamente o se pueden desencadenar mediante la interacción del usuario, por ejemplo, haciendo clic con el mouse.</span><span class="sxs-lookup"><span data-stu-id="2cba4-108">The animations can start automatically or may be triggered by user interaction, e.g. by clicking with the mouse.</span></span>


## <a name="overview"></a><span data-ttu-id="2cba4-109">Información general</span><span class="sxs-lookup"><span data-stu-id="2cba4-109">Overview</span></span>

<span data-ttu-id="2cba4-110">El control de animación en el Kit de herramientas de Control de AJAX de ASP.NET no es simplemente un control sino un marco completo para agregar animaciones a un control.</span><span class="sxs-lookup"><span data-stu-id="2cba4-110">The Animation control in the ASP.NET AJAX Control Toolkit is not just a control but a whole framework to add animations to a control.</span></span> <span data-ttu-id="2cba4-111">Las animaciones pueden iniciar automáticamente o se pueden desencadenar mediante la interacción del usuario, por ejemplo, haciendo clic con el mouse.</span><span class="sxs-lookup"><span data-stu-id="2cba4-111">The animations can start automatically or may be triggered by user interaction, e.g. by clicking with the mouse.</span></span>

## <a name="steps"></a><span data-ttu-id="2cba4-112">Pasos</span><span class="sxs-lookup"><span data-stu-id="2cba4-112">Steps</span></span>

<span data-ttu-id="2cba4-113">En primer lugar, se incluyen el `ScriptManager` en la página; a continuación, AJAX de ASP.NET se carga la biblioteca, lo que permite usar el Kit de herramientas de Control:</span><span class="sxs-lookup"><span data-stu-id="2cba4-113">First of all, include the `ScriptManager` in the page; then, the ASP.NET AJAX library is loaded, making it possible to use the Control Toolkit:</span></span>

[!code-aspx[Main](animating-in-response-to-user-interaction-vb/samples/sample1.aspx)]

<span data-ttu-id="2cba4-114">La animación se aplicará a un panel de texto que el siguiente aspecto:</span><span class="sxs-lookup"><span data-stu-id="2cba4-114">The animation will be applied to a panel of text which looks like this:</span></span>

[!code-aspx[Main](animating-in-response-to-user-interaction-vb/samples/sample2.aspx)]

<span data-ttu-id="2cba4-115">En la clase CSS asociada para el panel, definir un color de fondo "nice" y también establece un ancho fijo para el panel:</span><span class="sxs-lookup"><span data-stu-id="2cba4-115">In the associated CSS class for the panel, define a nice background color and also set a fixed width for the panel:</span></span>

[!code-css[Main](animating-in-response-to-user-interaction-vb/samples/sample3.css)]

<span data-ttu-id="2cba4-116">A continuación, agregue el `AnimationExtender` a la página, proporciona un `ID`, el `TargetControlID` atributo y el obligatoria `runat="server"`:</span><span class="sxs-lookup"><span data-stu-id="2cba4-116">Then, add the `AnimationExtender` to the page, providing an `ID`, the `TargetControlID` attribute and the obligatory `runat="server"`:</span></span>

[!code-aspx[Main](animating-in-response-to-user-interaction-vb/samples/sample4.aspx)]

<span data-ttu-id="2cba4-117">En el `<Animations>` nodo, hay cinco formas de iniciar la animación a través de la interacción del usuario (el elemento que falta es `<OnLoad>` que se ejecuta una vez que se ha cargado completamente toda la página):</span><span class="sxs-lookup"><span data-stu-id="2cba4-117">Within the `<Animations>` node, there are five ways to start the animation via user interaction (the missing element is `<OnLoad>` which is executed once the whole page has been fully loaded):</span></span>

- <span data-ttu-id="2cba4-118">`<OnClick>`(el mouse (ratón) haga clic en el control)</span><span class="sxs-lookup"><span data-stu-id="2cba4-118">`<OnClick>` (mouse click on the control)</span></span>
- <span data-ttu-id="2cba4-119">`<OnHoverOut>`(mouse deja el control)</span><span class="sxs-lookup"><span data-stu-id="2cba4-119">`<OnHoverOut>` (mouse leaves the control)</span></span>
- <span data-ttu-id="2cba4-120">`<OnHoverOver>`(se sitúa el mouse sobre un control, detener la `<OnHoverOut>` animación)</span><span class="sxs-lookup"><span data-stu-id="2cba4-120">`<OnHoverOver>` (mouse hovers over a control, stopping the `<OnHoverOut>` animation)</span></span>
- <span data-ttu-id="2cba4-121">`<OnMouseOut>`(el mouse sale de un control)</span><span class="sxs-lookup"><span data-stu-id="2cba4-121">`<OnMouseOut>` (mouse leaves a control)</span></span>
- <span data-ttu-id="2cba4-122">`<OnMouseOver>`(se sitúa el mouse sobre un control, no se detiene la `<OnMouseOut>` animación)</span><span class="sxs-lookup"><span data-stu-id="2cba4-122">`<OnMouseOver>` (mouse hovers over a control, not stopping the `<OnMouseOut>` animation)</span></span>

<span data-ttu-id="2cba4-123">En este escenario, `<OnClick>` se utiliza.</span><span class="sxs-lookup"><span data-stu-id="2cba4-123">In this scenario, `<OnClick>` is used.</span></span> <span data-ttu-id="2cba4-124">Cuando el usuario hace clic en el panel, se cambia el tamaño y fundido de salida al mismo tiempo.</span><span class="sxs-lookup"><span data-stu-id="2cba4-124">When the user clicks on the panel, it is resized and fades out at the same time.</span></span>

[!code-aspx[Main](animating-in-response-to-user-interaction-vb/samples/sample5.aspx)]


<span data-ttu-id="2cba4-125">[![La animación inicia un clic del mouse](animating-in-response-to-user-interaction-vb/_static/image2.png)](animating-in-response-to-user-interaction-vb/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="2cba4-125">[![A mouse click starts the animation](animating-in-response-to-user-interaction-vb/_static/image2.png)](animating-in-response-to-user-interaction-vb/_static/image1.png)</span></span>

<span data-ttu-id="2cba4-126">La animación inicia un clic del mouse ([haga clic aquí para ver la imagen a tamaño completo](animating-in-response-to-user-interaction-vb/_static/image3.png))</span><span class="sxs-lookup"><span data-stu-id="2cba4-126">A mouse click starts the animation ([Click to view full-size image](animating-in-response-to-user-interaction-vb/_static/image3.png))</span></span>

>[!div class="step-by-step"]
<span data-ttu-id="2cba4-127">[Anterior](picking-one-animation-out-of-a-list-vb.md)
[Siguiente](disabling-actions-during-animation-vb.md)</span><span class="sxs-lookup"><span data-stu-id="2cba4-127">[Previous](picking-one-animation-out-of-a-list-vb.md)
[Next](disabling-actions-during-animation-vb.md)</span></span>