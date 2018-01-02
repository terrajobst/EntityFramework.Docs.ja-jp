---
title: "複数のモデルと同じ DbContext 型 - EF コア間で切り替える"
author: AndriySvyryd
uid: core/modeling/dynamic-model
ms.openlocfilehash: e6828c62c55ae6f48d46a20528268264c3f22b26
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="alternating-between-multiple-models-with-the-same-dbcontext-type"></a><span data-ttu-id="8ea1b-102">同じ DbContext 型を持つ複数のモデル間で切り替える</span><span class="sxs-lookup"><span data-stu-id="8ea1b-102">Alternating between multiple models with the same DbContext type</span></span>

<span data-ttu-id="8ea1b-103">組み込まれているモデル`OnModelCreating`でした、プロパティを使用して、コンテキストのモデルの構築方法を変更します。</span><span class="sxs-lookup"><span data-stu-id="8ea1b-103">The model built in `OnModelCreating` could use a property on the context to change how the model is built.</span></span> <span data-ttu-id="8ea1b-104">たとえばを除外する特定のプロパティに使用できます。</span><span class="sxs-lookup"><span data-stu-id="8ea1b-104">For example it could be used to exclude a certain property:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicContext.cs?name=Class)]

## <a name="imodelcachekeyfactory"></a><span data-ttu-id="8ea1b-105">IModelCacheKeyFactory</span><span class="sxs-lookup"><span data-stu-id="8ea1b-105">IModelCacheKeyFactory</span></span>
<span data-ttu-id="8ea1b-106">ただし追加変更なしには、上記を実行しようとした場合は、得られる、同じモデルのすべての値の新しいコンテキストが作成されるたびに`IgnoreIntProperty`です。</span><span class="sxs-lookup"><span data-stu-id="8ea1b-106">However if you tried doing the above without additional changes you would get the same model every time a new context is created for any value of `IgnoreIntProperty`.</span></span> <span data-ttu-id="8ea1b-107">これは、モデルのキャッシュのみを呼び出すことによって、パフォーマンスを向上させるために使用して EF メカニズムにより発生`OnModelCreating`とモデルをキャッシュします。</span><span class="sxs-lookup"><span data-stu-id="8ea1b-107">This is caused by the model caching mechanism EF uses to improve the performance by only invoking `OnModelCreating` once and caching the model.</span></span>

<span data-ttu-id="8ea1b-108">既定では、EF は、任意の指定したコンテキストに対して、モデルの種類が同じであることと仮定します。</span><span class="sxs-lookup"><span data-stu-id="8ea1b-108">By default EF assumes that for any given context type the model will be the same.</span></span> <span data-ttu-id="8ea1b-109">これを実現する既定の実装`IModelCacheKeyFactory`のみ、コンテキストの種類を含むキーを返します。</span><span class="sxs-lookup"><span data-stu-id="8ea1b-109">To accomplish this the default implementation of `IModelCacheKeyFactory` returns a key that just contains the context type.</span></span> <span data-ttu-id="8ea1b-110">置換する必要があります。 これを変更する、`IModelCacheKeyFactory`サービス。</span><span class="sxs-lookup"><span data-stu-id="8ea1b-110">To change this you need to replace the `IModelCacheKeyFactory` service.</span></span> <span data-ttu-id="8ea1b-111">新しい実装を使用して他のモデルのキーを比較できるオブジェクトを返す必要があります、`Equals`モデルに影響するすべての変数を考慮したメソッド。</span><span class="sxs-lookup"><span data-stu-id="8ea1b-111">The new implementation needs to return an object that can be compared to other model keys using the `Equals` method that takes into account all the variables that affect the model:</span></span>

[!code-csharp[Main](../../../samples/core/DynamicModel/DynamicModelCacheKeyFactory.cs?name=Class)]