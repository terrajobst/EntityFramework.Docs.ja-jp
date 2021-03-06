---
title: キー-EF Core
description: Entity Framework Core を使用する場合のエンティティ型のキーの構成方法
author: AndriySvyryd
ms.author: ansvyryd
ms.date: 11/06/2019
uid: core/modeling/keys
ms.openlocfilehash: abd65a5ea079a49fd7a3bbc84a9337f6ee19fab1
ms.sourcegitcommit: cc0ff36e46e9ed3527638f7208000e8521faef2e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2020
ms.locfileid: "78413991"
---
# <a name="keys"></a>[キー]

キーは、各エンティティインスタンスの一意の識別子として機能します。 EF のほとんどのエンティティには、リレーショナルデータベースの*主キー*の概念にマップされる1つのキーがあります (キーのないエンティティについては、「[キーなしエンティティ](xref:core/modeling/keyless-entity-types)」を参照してください)。 エンティティには、主キーを超える追加のキーを含めることができます (詳細については、「[代替キー](#alternate-keys) 」を参照してください)。

慣例により、`Id` または `<type name>Id` という名前のプロパティは、エンティティの主キーとして構成されます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/KeyId.cs?name=KeyId&highlight=3,11)]

> [!NOTE]
> 所有されている[エンティティ型](xref:core/modeling/owned-entities)は、キーを定義するために異なる規則を使用します。

次のように、1つのプロパティをエンティティの主キーとして構成できます。

## <a name="data-annotations"></a>[データの注釈](#tab/data-annotations)

[!code-csharp[Main](../../../samples/core/Modeling/DataAnnotations/KeySingle.cs?name=KeySingle&highlight=3)]

## <a name="fluent-api"></a>[Fluent API](#tab/fluent-api)

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeySingle.cs?name=KeySingle&highlight=4)]

***

また、エンティティのキーとして複数のプロパティを構成することもできます。これは、複合キーと呼ばれます。 複合キーは、Fluent API を使用してのみ構成できます。規則は複合キーを設定するのではなく、データ注釈を使用して構成することはできません。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyComposite.cs?name=KeyComposite&highlight=4)]

## <a name="primary-key-name"></a>主キー名

慣例により、リレーショナルデータベースでは、主キーが `PK_<type name>`という名前で作成されます。 次のように、primary key 制約の名前を構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/KeyName.cs?name=KeyName&highlight=5)]

## <a name="key-types-and-values"></a>キーの種類と値

EF Core は、`string`、`Guid`、`byte[]` など、主キーとして任意のプリミティブ型のプロパティの使用をサポートしていますが、すべてのデータベースがキーとしてすべての型をサポートするわけではありません。 場合によっては、キー値がサポートされている型に自動的に変換されることがあります。それ以外の場合は、[手動で](xref:core/modeling/value-conversions)変換を指定する必要があります。

新しいエンティティをコンテキストに追加する場合、キープロパティには既定値以外の値が常に設定されている必要がありますが、[データベースによって生成](xref:core/modeling/generated-properties)される型もあります。 その場合、EF は、追跡のためにエンティティが追加されると、一時値の生成を試みます。 [SaveChanges](/dotnet/api/Microsoft.EntityFrameworkCore.DbContext.SaveChanges)が呼び出された後、一時的な値はデータベースによって生成される値に置き換えられます。

> [!Important]
> キープロパティの値がデータベースによって生成され、エンティティの追加時に既定値以外の値が指定されている場合、EF は、そのエンティティがデータベースに既に存在していると見なし、新しいエンティティを挿入する代わりに更新しようとします。 これを回避するには、値の生成をオフにするか、[生成されたプロパティに明示的な値を指定する方法](../saving/explicit-values-generated-properties.md)を参照してください。

## <a name="alternate-keys"></a>代替キー

代替キーは、主キーに加えて、各エンティティインスタンスの代替一意識別子として機能します。これは、リレーションシップのターゲットとして使用できます。 リレーショナルデータベースを使用する場合、これは代替キー列の一意のインデックス/制約の概念と、その列を参照する1つ以上の foreign key 制約にマップされます。

> [!TIP]
> 列に一意性を適用するだけの場合は、代替キーではなく一意のインデックスを定義します (「[インデックス](indexes.md)」を参照してください)。 EF では、代替キーは読み取り専用で、一意のインデックスに対する追加のセマンティクスを提供します。これは、外部キーのターゲットとして使用できるためです。

通常、代替キーは必要に応じて導入され、手動で構成する必要はありません。 慣例により、リレーションシップのターゲットとして主キーではないプロパティを特定するときに、代替キーが導入されます。

[!code-csharp[Main](../../../samples/core/Modeling/Conventions/AlternateKey.cs?name=AlternateKey&highlight=12)]

また、1つのプロパティを別のキーにするように構成することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeySingle.cs?name=AlternateKeySingle&highlight=4)]

また、複数のプロパティを別のキー (複合代替キーと呼ばれます) として構成することもできます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyComposite.cs?name=AlternateKeyComposite&highlight=4)]

最後に、規則により、代替キーに対して導入されるインデックスと制約には `AK_<type name>_<property name>` という名前が付けられます (複合代替キーの場合 `<property name>` は、アンダースコアで区切られたプロパティ名の一覧になります)。 代替キーのインデックスと unique 制約の名前を構成できます。

[!code-csharp[Main](../../../samples/core/Modeling/FluentAPI/AlternateKeyName.cs?name=AlternateKeyName&highlight=5)]
