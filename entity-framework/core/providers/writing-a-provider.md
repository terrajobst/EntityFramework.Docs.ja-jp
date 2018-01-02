---
title: "プロバイダーの作成、データベースの EF コア"
author: anmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: 1165e2ec-e421-43fc-92ab-d92f9ab3c494
ms.technology: entity-framework-core
uid: core/providers/writing-a-provider
ms.openlocfilehash: 9d6d3748a9097b3b8eeee2a8a516c53f3b2afa58
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="writing-a-database-provider"></a><span data-ttu-id="a0504-102">データベース プロバイダーの作成</span><span class="sxs-lookup"><span data-stu-id="a0504-102">Writing a Database Provider</span></span>

<span data-ttu-id="a0504-103">Entity Framework のコア データベース プロバイダーの作成方法の詳細については、次を参照してください。 [EF コア プロバイダーを記述するように](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/)によって[Arthur ヴィッカース](https://github.com/ajcvickers)です。</span><span class="sxs-lookup"><span data-stu-id="a0504-103">For information about writing an Entity Framework Core database provider, see [So you want to write an EF Core provider](https://blog.oneunicorn.com/2016/11/11/so-you-want-to-write-an-ef-core-provider/) by [Arthur Vickers](https://github.com/ajcvickers).</span></span>

<span data-ttu-id="a0504-104">コード ベースを EF Core はオープン ソースであるため、参照として使用できるいくつかのデータベース プロバイダーを含んでいます。</span><span class="sxs-lookup"><span data-stu-id="a0504-104">The EF Core code base is open source and contains several database providers that can be used as a reference.</span></span> <span data-ttu-id="a0504-105">Https://github.com/aspnet/EntityFramework でソース コードを検索することができます。</span><span class="sxs-lookup"><span data-stu-id="a0504-105">You can find the source code at https://github.com/aspnet/EntityFramework.</span></span>

## <a name="the-providers-beware-label"></a><span data-ttu-id="a0504-106">プロバイダー ラベルに注意してください</span><span class="sxs-lookup"><span data-stu-id="a0504-106">The providers-beware label</span></span>

<span data-ttu-id="a0504-107">プロバイダーでの作業を開始すると、監視、 [ `providers-beware` ](https://github.com/aspnet/EntityFramework/labels/providers-beware) GitHub の問題やプル要求に対するラベルです。</span><span class="sxs-lookup"><span data-stu-id="a0504-107">Once you begin work on a provider, watch for the [`providers-beware`](https://github.com/aspnet/EntityFramework/labels/providers-beware) label on our GitHub issues and pull requests.</span></span> <span data-ttu-id="a0504-108">プロバイダーの作成者に影響を与える可能性の変更を特定するのにこのラベルを使用します。</span><span class="sxs-lookup"><span data-stu-id="a0504-108">We use this label to identify changes that may impact provider writers.</span></span>

## <a name="suggested-naming-of-third-party-providers"></a><span data-ttu-id="a0504-109">サード パーティ プロバイダーの名前付けを推奨</span><span class="sxs-lookup"><span data-stu-id="a0504-109">Suggested naming of third party providers</span></span>

<span data-ttu-id="a0504-110">NuGet パッケージの次の名前付けを使用することをお勧めします。</span><span class="sxs-lookup"><span data-stu-id="a0504-110">We suggest using the following naming for NuGet packages.</span></span> <span data-ttu-id="a0504-111">これは、同じ EF コア チームによって配信されるパッケージの名前です。</span><span class="sxs-lookup"><span data-stu-id="a0504-111">This is consistent with the names of packages delivered by the EF Core team.</span></span>

`<Optional project/company name>.EntityFrameworkCore.<Database engine name>`

<span data-ttu-id="a0504-112">例:</span><span class="sxs-lookup"><span data-stu-id="a0504-112">For example:</span></span>
* `Microsoft.EntityFrameworkCore.SqlServer`
* `Npgsql.EntityFrameworkCore.PostgreSQL`
* `EntityFrameworkCore.SqlServerCompact40`