---
title: "複数のプロジェクトの EF コアでの移行"
author: bricelam
ms.author: bricelam
ms.date: 10/30/2017
ms.technology: entity-framework-core
ms.openlocfilehash: e059deb6c7555a4f6732fe7942855e95b3f9a34c
ms.sourcegitcommit: b467368cc350e6059fdc0949e042a41cb11e61d9
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/15/2017
---
<a name="using-a-separate-project"></a><span data-ttu-id="196a2-102">別のプロジェクトを使用します。</span><span class="sxs-lookup"><span data-stu-id="196a2-102">Using a Separate Project</span></span>
========================
<span data-ttu-id="196a2-103">1 つ含まれているよりも別のアセンブリで使用して移行を格納することがあります、`DbContext`です。</span><span class="sxs-lookup"><span data-stu-id="196a2-103">You may want to store your migrations in a different assembly than the one containing your `DbContext`.</span></span> <span data-ttu-id="196a2-104">また、リリースごとにアップグレードとこの戦略などの移行には、複数のセットを維持するために、開発用の 1 つを使用することができます。</span><span class="sxs-lookup"><span data-stu-id="196a2-104">You can also use this strategy to maintain multiple sets of migrations, for example, one for development and another for release-to-release upgrades.</span></span>

<span data-ttu-id="196a2-105">目的</span><span class="sxs-lookup"><span data-stu-id="196a2-105">To do this...</span></span>

1. <span data-ttu-id="196a2-106">新しいクラス ライブラリを作成します。</span><span class="sxs-lookup"><span data-stu-id="196a2-106">Create a new class library.</span></span>

2. <span data-ttu-id="196a2-107">DbContext アセンブリへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="196a2-107">Add a reference to your DbContext assembly.</span></span>

3. <span data-ttu-id="196a2-108">クラス ライブラリに移行およびモデルのスナップショット ファイルを移動します。</span><span class="sxs-lookup"><span data-stu-id="196a2-108">Move the migrations and model snapshot files to the class library.</span></span>
   * <span data-ttu-id="196a2-109">いずれかを追加していない場合は、DbContext プロジェクトに追加しに移動します。</span><span class="sxs-lookup"><span data-stu-id="196a2-109">If you haven't added any, add one to the DbContext project then move it.</span></span>

4. <span data-ttu-id="196a2-110">移行アセンブリを構成します。</span><span class="sxs-lookup"><span data-stu-id="196a2-110">Configure the migrations assembly:</span></span>

   ``` csharp
   options.UseSqlServer(
       connectionString,
       x => x.MigrationsAssembly("MyApp.Migrations"));
   ```

5. <span data-ttu-id="196a2-111">スタートアップ アセンブリから、移行アセンブリへの参照を追加します。</span><span class="sxs-lookup"><span data-stu-id="196a2-111">Add a reference to your migrations assembly from the startup assembly.</span></span>
   * <span data-ttu-id="196a2-112">これにより、循環する依存関係場合、は、クラス ライブラリの出力パスを更新します。</span><span class="sxs-lookup"><span data-stu-id="196a2-112">If this causes a circular dependency, update the output path of the class library:</span></span>

     ``` xml
     <PropertyGroup>
       <OutputPath>..\MyStarupProject\bin\$(Configuration)\</OutputPath>
     </PropertyGroup>
     ```

<span data-ttu-id="196a2-113">削除できた場合すべて正しくは、新しい移行プロジェクトに追加することができます。</span><span class="sxs-lookup"><span data-stu-id="196a2-113">If you did everything correctly, you should be able to add new migrations to the project.</span></span>

``` powershell
Add-Migration NewMigration -Project MyApp.Migrations
```
``` Console
dotnet ef migraitons add NewMigration --project MyApp.Migrations
```