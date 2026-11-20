# openresty-release-mirror

OpenResty source bundles used to build [VeeCode APIP](https://github.com/veecode-gateway/kong), published as GitHub release assets.

The [kong fork](https://github.com/veecode-gateway/kong)'s Bazel build fetches OpenResty from:

```pre
https://github.com/veecode-gateway/openresty-release-mirror/releases/download/<version>/openresty-<version>.tar.gz
```

That is the same layout as `Kong/openresty-release-mirror`. The build pins the expected SHA256 of each tarball in `.requirements`.

## What gets published here

- **Pre-release bundles (`<version>-alpha`).** OpenResty source bundles built by VeeCode from an upstream [openresty/openresty](https://github.com/openresty/openresty) commit, before OpenResty publishes the official release. We only do this when an APIP release needs a security fix that is already on OpenResty `master` but not yet released. The `-alpha` suffix keeps these apart from official tarballs. It shows up in the tarball name, its top-level directory, and nginx's version string (`openresty/<version>-alpha`). **These are not official OpenResty releases.**
- **Copies of official OpenResty tarballs** (no suffix), if we mirror them. These are byte-identical to the files on `openresty.org/download/`.

## How a pre-release bundle is built

Each bundle is generated with OpenResty's own `util/mirror-tarballs`. It is built from a pinned openresty/openresty commit, inside a digest-pinned `ubuntu:24.04` container, with `-alpha` added to the version in `util/ver` and no other changes. Each release's notes record its provenance: the OpenResty commit, the build image, the bundled component versions, and the tarball's SHA256.

Rebuilding is not byte-reproducible, because tar and gzip embed timestamps. The published tarball is the reference artifact. To check a rebuild, compare the unpacked trees (`diff -r`), not the SHAs.

## Verifying a download

Each pre-release bundle comes with a detached, ASCII-armored GPG signature (`.asc`) made with VeeCode's OpenResty bundle signing key:

```bash
gpg --verify openresty-<version>.tar.gz.asc openresty-<version>.tar.gz
shasum -a 256 openresty-<version>.tar.gz   # compare with the release notes
```

The signing key is "VeeCode OpenResty Bundle Signing Key" `<openresty@veecode.codes>`, with fingerprint:

```pre
E818 FA0B 5E48 6143 93FA  D520 C474 277C 1719 50D4
```

Its public key is [`openresty-bundle.pub.asc`](openresty-bundle.pub.asc) in this repository. Import it with `gpg --import openresty-bundle.pub.asc`, then check that the fingerprint matches before trusting a signature.

## License

The bundles contain OpenResty and its bundled components, under their own licenses (OpenResty and nginx are BSD-licensed). See the `COPYRIGHT` and per-component license files inside each tarball.
