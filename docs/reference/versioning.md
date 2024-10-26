# バージョニング

uvは、マイナーバージョン番号が破壊的変更のためにバンプされ、パッチバージョン番号がバグ修正、機能強化、およびその他の非破壊的変更のためにバンプされるカスタムバージョニングスキームを使用します。

uvはまだ安定したAPIを持っていません。uvのAPIが安定したら（v1.0.0）、バージョニングスキームは[セマンティックバージョニング](https://semver.org/)に準拠します。

uv's changelog can be [viewed on GitHub](https://github.com/astral-sh/uv/blob/main/CHANGELOG.md).

## キャッシュバージョニング

キャッシュバージョンは uv の内部と見なされるため、マイナーリリースまたはパッチリリースで変更されることがあります。詳細は [キャッシュバージョニング](../concepts/cache.md#cache-versioning) を参照してください。

## ロックファイルバージョニング

`uv.lock` スキーマバージョンはパブリック API の一部と見なされるため、破壊的変更としてマイナーリリースでのみインクリメントされます。詳細は [ロックファイルバージョニング](../concepts/resolution.md#lockfile-versioning) を参照してください。
