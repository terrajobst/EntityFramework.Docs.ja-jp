---
title: "RTM - する 1.0 RC2 EF からアップグレードするコア EF コア"
author: rowanmiller
ms.author: divega
ms.date: 10/27/2016
ms.assetid: c3c1940b-136d-45d8-aa4f-cb5040f8980a
ms.technology: entity-framework-core
uid: core/miscellaneous/rc2-rtm-upgrade
ms.openlocfilehash: 7a1d85949a5f9e1ad7efdbf585a608d815e8ce63
ms.sourcegitcommit: 01a75cd483c1943ddd6f82af971f07abde20912e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/27/2017
---
# <a name="upgrading-from-ef-core-10-rc2-to-rtm"></a><span data-ttu-id="1f9b0-102">EF Core 1.0 RC2 から RTM にアップグレードします。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-102">Upgrading from EF Core 1.0 RC2 to RTM</span></span>

<span data-ttu-id="1f9b0-103">この記事 1.0.0 の RC2 パッケージにビルドされたアプリケーションの移動のガイダンスを提供する RTM です。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-103">This article provides guidance for moving an application built with the RC2 packages to 1.0.0 RTM.</span></span>

## <a name="package-versions"></a><span data-ttu-id="1f9b0-104">パッケージのバージョン</span><span class="sxs-lookup"><span data-stu-id="1f9b0-104">Package Versions</span></span>

<span data-ttu-id="1f9b0-105">アプリケーションをインストールするには通常最上位のパッケージの名前は、RC2 と RTM の間で変わらないです。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-105">The names of the top level packages that you would typically install into an application did not change between RC2 and RTM.</span></span>

<span data-ttu-id="1f9b0-106">**RTM バージョンにインストールされているパッケージをアップグレードする必要があります。**</span><span class="sxs-lookup"><span data-stu-id="1f9b0-106">**You need to upgrade the installed packages to the RTM versions:**</span></span>

* <span data-ttu-id="1f9b0-107">実行時のパッケージ (例: `Microsoft.EntityFrameworkCore.SqlServer`) から変更`1.0.0-rc2-final`に`1.0.0`です。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-107">Runtime packages (e.g. `Microsoft.EntityFrameworkCore.SqlServer`) changed from `1.0.0-rc2-final` to `1.0.0`.</span></span>

* <span data-ttu-id="1f9b0-108">`Microsoft.EntityFrameworkCore.Tools`からパッケージが変更された`1.0.0-preview1-final`に`1.0.0-preview2-final`です。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-108">The `Microsoft.EntityFrameworkCore.Tools` package changed from `1.0.0-preview1-final` to `1.0.0-preview2-final`.</span></span> <span data-ttu-id="1f9b0-109">ツールがまだプレリリース版であることを注意してください。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-109">Note that tooling is still pre-release.</span></span>

## <a name="existing-migrations-may-need-maxlength-added"></a><span data-ttu-id="1f9b0-110">既存の移行は、追加 maxLength を必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-110">Existing migrations may need maxLength added</span></span>

<span data-ttu-id="1f9b0-111">RC2 では、移行では、列定義と同様に参照`table.Column<string>(nullable: true)`列の長さが、移行の分離コードの格納のメタデータの一部で検索したとします。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-111">In RC2, the column definition in a migration looked like `table.Column<string>(nullable: true)` and the length of the column was looked up in some metadata we store in the code behind the migration.</span></span> <span data-ttu-id="1f9b0-112">RTM では、長さが今すぐスキャフォールディング コードにインクルード`table.Column<string>(maxLength: 450, nullable: true)`です。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-112">In RTM, the length is now included in the scaffolded code `table.Column<string>(maxLength: 450, nullable: true)`.</span></span>

<span data-ttu-id="1f9b0-113">RTM を使用する前にスキャフォールディングされましたが、既存の移行はありません、`maxLength`引数が指定されています。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-113">Any existing migrations that were scaffolded prior to using RTM will not have the `maxLength` argument specified.</span></span> <span data-ttu-id="1f9b0-114">つまり、データベースでサポートされている最大の長さが使用されます (`nvarchar(max)` SQL Server 上)。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-114">This means the maximum length supported by the database will be used (`nvarchar(max)` on SQL Server).</span></span> <span data-ttu-id="1f9b0-115">一部の列がキー、外部キーの一部である列の可能性があります。 またはインデックスは、最大長を含まれるように更新する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-115">This may be fine for some columns, but columns that are part of a key, foreign key, or index need to be updated to include a maximum length.</span></span> <span data-ttu-id="1f9b0-116">規則では、450 は、最大長キーについては、外部キーは、使用されて、列のインデックスを作成します。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-116">By convention, 450 is the maximum length used for keys, foreign keys, and indexed columns.</span></span> <span data-ttu-id="1f9b0-117">モデルで、長さが明示的に構成されている場合する必要がありますを使用してその長さ。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-117">If you have explicitly configured a length in the model, then you should use that length instead.</span></span>

<span data-ttu-id="1f9b0-118">**ASP.NET Id**</span><span class="sxs-lookup"><span data-stu-id="1f9b0-118">**ASP.NET Identity**</span></span>

<span data-ttu-id="1f9b0-119">この変更に影響を ASP.NET の Id を使用し、以前から作成されたプロジェクトのプロジェクト テンプレートを RTM です。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-119">This change impacts projects that use ASP.NET Identity and were created from a pre-RTM project template.</span></span> <span data-ttu-id="1f9b0-120">プロジェクト テンプレートには、データベースの作成に使用される移行が含まれています。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-120">The project template includes a migration used to create the database.</span></span> <span data-ttu-id="1f9b0-121">最大長を指定するこの移行を編集する必要があります`256`次の列にします。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-121">This migration must be edited to specify a maximum length of `256` for the following columns.</span></span>

*  <span data-ttu-id="1f9b0-122">**AspNetRoles**</span><span class="sxs-lookup"><span data-stu-id="1f9b0-122">**AspNetRoles**</span></span>

    * <span data-ttu-id="1f9b0-123">名前</span><span class="sxs-lookup"><span data-stu-id="1f9b0-123">Name</span></span>

    * <span data-ttu-id="1f9b0-124">NormalizedName</span><span class="sxs-lookup"><span data-stu-id="1f9b0-124">NormalizedName</span></span>

*  <span data-ttu-id="1f9b0-125">**AspNetUsers**</span><span class="sxs-lookup"><span data-stu-id="1f9b0-125">**AspNetUsers**</span></span>

   * <span data-ttu-id="1f9b0-126">電子メール</span><span class="sxs-lookup"><span data-stu-id="1f9b0-126">Email</span></span>

   * <span data-ttu-id="1f9b0-127">NormalizedEmail</span><span class="sxs-lookup"><span data-stu-id="1f9b0-127">NormalizedEmail</span></span>

   * <span data-ttu-id="1f9b0-128">NormalizedUserName</span><span class="sxs-lookup"><span data-stu-id="1f9b0-128">NormalizedUserName</span></span>

   * <span data-ttu-id="1f9b0-129">UserName</span><span class="sxs-lookup"><span data-stu-id="1f9b0-129">UserName</span></span>

<span data-ttu-id="1f9b0-130">最初の移行をデータベースに適用すると、この変更を行うには、次の例外になります。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-130">Failure to make this change will result in the following exception when the initial migration is applied to a database.</span></span>

    System.Data.SqlClient.SqlException (0x80131904): Column 'NormalizedName' in table 'AspNetRoles' is of a type that is invalid for use as a key column in an index.

## <a name="net-core-remove-imports-in-projectjson"></a><span data-ttu-id="1f9b0-131">.NET core: project.json に「インポート」を削除します。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-131">.NET Core: Remove "imports" in project.json</span></span>

<span data-ttu-id="1f9b0-132">追加する必要がある RC2 を使用して .NET Core をターゲットとするが場合、 `imports` EF コアの依存関係をサポートしていない .NET 標準の一部の一時的な回避策として project.json にします。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-132">If you were targeting .NET Core with RC2, you needed to add `imports` to project.json as a temporary workaround for some of EF Core's dependencies not supporting .NET Standard.</span></span> <span data-ttu-id="1f9b0-133">ここで削除できます。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-133">These can now be removed.</span></span>

``` json
{
  "frameworks": {
    "netcoreapp1.0": {
      "imports": ["dnxcore50", "portable-net451+win8"]
    }
  }
}
```

## <a name="uwp-add-binding-redirects"></a><span data-ttu-id="1f9b0-134">UWP: は、バインド リダイレクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-134">UWP: Add binding redirects</span></span>

<span data-ttu-id="1f9b0-135">ユニバーサル Windows プラットフォーム (UWP) プロジェクトの結果、次のエラーで EF コマンドを実行しようとしています。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-135">Attempting to run EF commands on Universal Windows Platform (UWP) projects results in the following error:</span></span>

    System.IO.FileLoadException: Could not load file or assembly 'System.IO.FileSystem.Primitives, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a' or one of its dependencies. The located assembly's manifest definition does not match the assembly reference.

<span data-ttu-id="1f9b0-136">UWP プロジェクトにバインド リダイレクトを手動で追加する必要があります。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-136">You need to manually add binding redirects to the UWP project.</span></span> <span data-ttu-id="1f9b0-137">という名前のファイルを作成する`App.config`プロジェクトのルート フォルダーと、正しいバージョンのアセンブリにリダイレクトを追加します。</span><span class="sxs-lookup"><span data-stu-id="1f9b0-137">Create a file named `App.config` in the project root folder and add redirects to the correct assembly versions.</span></span>

``` xml
<configuration>
 <runtime>
   <assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
     <dependentAssembly>
       <assemblyIdentity name="System.IO.FileSystem.Primitives"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
     <dependentAssembly>
       <assemblyIdentity name="System.Threading.Overlapped"
                         publicKeyToken="b03f5f7f11d50a3a"
                         culture="neutral" />
       <bindingRedirect oldVersion="4.0.0.0"
                        newVersion="4.0.1.0"/>
     </dependentAssembly>
   </assemblyBinding>
 </runtime>
</configuration>
```