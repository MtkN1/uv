# バージョニング

uvは、マイナーバージョン番号が破壊的変更のためにバンプされ、パッチバージョン番号がバグ修正、機能強化、およびその他の非破壊的変更のためにバンプされるカスタムバージョニングスキームを使用します。

uvはまだ安定したAPIを持っていません。uvのAPIが安定したら（v1.0.0）、バージョニングスキームは[セマンティックバージョニング](https://semver.org/)に準拠します。

uv's changelog can be [viewed on GitHub](https://github.com/astral-sh/uv/blob/main/CHANGELOG.md).

## Cache versioning

Cache versions are considered internal to uv, and so may be changed in a minor or patch release. See
[Cache versioning](../concepts/cache.md#cache-versioning) for more.

## Lockfile versioning

The `uv.lock` schema version is considered part of the public API, and so will only be incremented
in a minor release as a breaking change. See
[Lockfile versioning](../concepts/resolution.md#lockfile-versioning) for more.
