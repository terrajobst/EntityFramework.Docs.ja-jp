---
title: EF Core を使ったコンポーネントのテスト - EF Core
description: EF Core を使用するアプリケーションをテストするさまざまな方法
author: ajcvickers
ms.date: 03/23/2020
uid: core/miscellaneous/testing/index
ms.openlocfilehash: b1ab37ebb0a3aae09d5d5b225f746cf83dfba170
ms.sourcegitcommit: 9b562663679854c37c05fca13d93e180213fb4aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/07/2020
ms.locfileid: "80634248"
---
# <a name="testing-code-that-uses-ef-core"></a>EF Core を使用するコードのテスト

データベースにアクセスするコードをテストするには、次のいずれかを実行する必要があります。
* 運用環境で使用されているのと同じデータベース システムに対して、クエリと更新を実行する。
* 管理がより容易な他のデータベース システムに、クエリと更新プログラムを実行する。
* データベースをまったく使用しない、テスト ダブルまたはその他のメカニズムを使用する。

このドキュメントでは、これらの各選択肢とのトレードオフについて概説し、各方法で EF Core を使用する方法を示します。  

## <a name="all-database-providers-are-not-equal"></a>すべてのデータベース プロバイダーが同一ではない

EF Core は、基になるデータベース システムのすべての側面を抽象化するように設計されていないことを理解しておくことが非常に重要です。
EF Core はむしろ、すべてのデータベース システムで使用できるパターンと概念のセットです。
EF Core データベース プロバイダーは、この共通のフレームワーク上に、データベース固有の動作と機能をレイヤー化します。
これにより、各データベース システムは、必要に応じて他のデータベース システムと同じ機能を果たしながら、それが最も得意とする操作を実行します。 

これは基本的に、データベース プロバイダーを切り替えた場合、EF Core の動作が変わってしまい、別のすべての動作に明確に対応するとされていない限り、そのアプリケーションの正常な動作は期待できないことを意味します。
ただし、リレーショナル データベース同士には共通性が多いため、多くの場合、これで機能します。
これには良い点と悪い点があります。
良い点は、データベースを比較的簡単に切り替えることができることです。
悪い点は、アプリケーションが新しいデータベース システムで完全にテストされていない場合、セキュリティについて誤った意識を与える可能性があるということです。  

## <a name="approach-1-production-database-system"></a>アプローチ 1: 運用データベース システム

前のセクションで説明したように、運用環境で実行される内容が確実にテストされるには、同じデータベース システムを使用する必要があります。
たとえば、展開されているアプリケーションが SQL Azure を使用する場合、SQL Azure に対してもテストを実行する必要があります。

ただし、コードを書き進めながらすべての開発者が SQL Azure でテストを実行するには、時間もコストもかかります。
テスト効率の向上には、どのタイミングで実稼働データベース システムから離れるのが適しているかというのが、これらの全アプローチにおける主なトレードオフです。

さいわいにも、この場合の答えは非常に簡単です。開発者は、ローカルまたはオンプレミスの SQL Server を使用してテストすればよいのです。
SQL Azure と SQL Server は非常に似ているため、SQL Server でテストをすることは通常、妥当なトレードオフになります。
ただし、それでも実稼働環境に移行する前に、SQL Azure 自体でもテストをした方が賢明です。
 
### <a name="localdb"></a>LocalDb 

すべての主要なデータベース システムには、ローカルでのテスト用の何らかの "開発者向けのバージョン" があります。
SQL Server にも、[LocalDb](/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-ver15) と呼ばれる機能があります。
LocalDb の主な利点は、必要に応じてデータベース インスタンスを起動できることです。
これにより、テストを実行していないときも、お使いのコンピューター上でデータベース サービスが実行されることを回避できます。

LocalDb に問題がないわけではありません。
* これでは、[SQL Server Developer エディション](/sql/sql-server/editions-and-components-of-sql-server-2016?view=sql-server-ver15)がサポートするものをすべてサポートしていません。
* Linux では利用できません。
* サービスの起動後の最初のテスト実行では、遅延が発生する可能性があります。

個人的には、私は自分の開発用コンピューターでデータベース サービスの実行が問題になったことはなく、開発者向けバージョンの一般使用をお勧めしています。
ただし、これは特にあまり高性能ではない開発用コンピューターを使用している一部のユーザーには適していると言えるでしょう。  

## <a name="approach-2-sqlite"></a>アプローチ 2: SQLite

EF Core では、SQL Server プロバイダーを、主にローカルの SQL Server インスタンスに対して実行することでテストします。
高速なコンピューターでは、これらのテストでは数千のクエリが数分で実行されます。
これは、実際のデータベース システムを使用することは、パフォーマンスに優れたソリューションになる可能性があることを示しています。
一部の軽量データベースの使用のみが、すばやくテストを実行できるというのは神話です。

そうは言っても、ご自分の実稼働データベース システムに近いものでテストを実行できない理由がある場合はどうしたらよいでしょうか。
次によい選択肢は、同様の機能を持つものを使用することです。
これは通常、別のリレーショナル データベースを使用することを意味し、[SQLite](https://sqlite.org/index.html) が選択肢であることは明らかです。

SQLite が選択肢として適切であるのは次のためです。
* お使いのアプリケーションでインプロセスで実行されるため、オーバーヘッドが低いです。
* 単純で自動作成されたファイルをデータベースに使用するため、データベース管理が不要です。
* ファイルの作成さえをも回避するインメモリ モードがあります。

ただし、次の点に注意してください。
* SQLite は当然、お使いの実稼働データベース システムで実行されるすべての機能をサポートしているわけではありません。
* SQLite は、一部のクエリではお使いの実稼働データベース システムと異なる動作をします。

そのため、何らかのテストに SQLite を使用する場合は、お使いの実稼働データベース システムでも必ずテストをする必要があります。

EF Core 固有のガイダンスについては、「[SQLite を使用したテスト](xref:core/miscellaneous/testing/sqlite)」を参照してください。 

## <a name="approach-3-the-ef-core-in-memory-database"></a>アプローチ 3:EF Core のインメモリ データベース

EF Core には、EF Core 自体の内部テストに使用するインメモリ データベースが付属しています。
このデータベースは、**EF Core を使用するテスト アプリケーションの代わりには一般に適していません**。 具体的には、次のように使用します。
* リレーショナル データベースでない
* トランザクションをサポートしない
* パフォーマンスが最適化されていない

EF Core 内部をテストする際には、これをテストにデータベースが関係ないときに特に使用するため、これらのいずれも重要ではありません。
一方、EF Core を使用するアプリケーションをテストする場合、これらが非常に重要になる場合があります。

## <a name="unit-testing"></a>単体テスト

データベースの一部のデータは使用するが、データベースの相互作用を実際にはテストしないビジネス ロジックをテストするとします。
これの方法の 1 つに、モックやフェイクなど、[テスト ダブル](https://en.wikipedia.org/wiki/Test_double)を使用する方法があります。

テスト ダブルは、EF Core の内部テストで使用します。
ただし、DbContext または IQueryable をモックすることはありません。
これを行うのは困難で、煩雑で、不安定です。
**これは行わないでください。**

代わりに、DbContext を使用するものの単体テストには、インメモリ データベースを使用します。
この場合、テストがデータベースの動作に依存しないため、インメモリ データベースを使用するのが適切です。
実際のデータベース クエリや更新プログラムをテストには、これを行わないでください。   

単体テストでのインメモリ データベースの使用に関する EF Core 固有のガイダンスについては、[インメモリ プロバイダーを使用したテスト](xref:core/miscellaneous/testing/in-memory)に関する記事を参照してください。
