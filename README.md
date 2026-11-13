# vntyper-data

[![Latest release](https://img.shields.io/github/v/release/vntyper-org/vntyper-data)](https://github.com/vntyper-org/vntyper-data/releases/latest)
[![License: MIT](https://img.shields.io/github/license/vntyper-org/vntyper-data)](LICENSE)
[![VNtyper documentation](https://img.shields.io/badge/docs-VNtyper-blue)](https://vntyper-org.github.io/VNtyper/)

Versioned, checksummed MUC1 reference bundles for
[VNtyper](https://github.com/vntyper-org/VNtyper), published as GitHub Releases tagged
`refs-vN`. Research use only.

No reference data is committed to git: no chromosome FASTAs, BWA indexes or generated
motif files. The repository holds the release specifications in [`releases/`](releases/),
the hand-curated files that have no upstream in [`seeds/`](seeds/), and the workflow that
builds a release. Each spec pins every upstream URL and its SHA-256, the SHA-256 of every
seed, every derivation and the digest it must produce, the VNtyper builder commit, and the
BWA and samtools versions. Upstream sources and their terms are listed in
[ATTRIBUTION.md](ATTRIBUTION.md).

## Releases

| Tag | Contents | Used by |
|---|---|---|
| `refs-v1` | chr1 FASTA + BWA index for UCSC hg19/hg38, NCBI GRCh37/GRCh38, Ensembl hg19/hg38; MUC1 common bundle | bundle-installing VNtyper before 2.0.21 |
| `refs-v2` | `refs-v1` assembly bundles byte-for-byte; MUC1 bundle with corrected GRCh38 adVNTR model (needs adVNTR >= 2.0.4) | VNtyper 2.0.21 and later |
| `refs-v3` | standard MUC1 length model for GRCh38, the same file as `seeds/grch38-standard-length-model-v1.json` | VNtyper installs the copy in `seeds/` |

Release notes and the reasons behind each tag are in the [release history](docs/releases.md).

## Install

VNtyper resolves this repository by default:

```bash
vntyper install-references --output-dir reference --references hg38
```

To build from the upstream sources instead of fetching a bundle:

```bash
vntyper install-references --output-dir reference --from-source
```

## Verify a download

The installer checks every asset against a SHA-256 committed in VNtyper's
`install_references_config.json`, not against the `SHA256SUMS` published beside the
assets: a checksum file hosted next to the files it describes cannot be its own root of
trust. The published checksums are for people reviewing a release:

```bash
gh release download refs-v2 --repo vntyper-org/vntyper-data --pattern SHA256SUMS --pattern '*-muc1.tar.gz'
sha256sum --check --ignore-missing SHA256SUMS
```

Expected output: `vntyper-references-refs-v2-muc1.tar.gz: OK`. Drop the
`*-muc1.tar.gz` pattern to fetch all seven assets (about 2 GB).

## Versioning

Published releases are immutable. A correction or a builder change gets a new `refs-vN`
tag; an existing tag is never re-cut, so installations pinned to it keep working. How a
release is specified, built and reviewed is described in
[building a release](docs/building.md). This repository has no test suite; the build
workflow verifies every digest a spec declares.

## License

The MIT [license](LICENSE) covers this repository's own scripts and metadata. It does not
extend to the upstream sequence data, which stays under the terms listed in
[ATTRIBUTION.md](ATTRIBUTION.md).

## Citing

Cite VNtyper using its [citation file](https://github.com/vntyper-org/VNtyper/blob/main/CITATION.cff)
and name the `refs-vN` tag you used. Cite adVNTR as given in [ATTRIBUTION.md](ATTRIBUTION.md).
