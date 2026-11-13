# Building a release

## Why releases are built here

VNtyper used to assemble its references at Docker image build time: six chromosome-1
genomes downloaded from UCSC, NCBI and Ensembl and BWA-indexed in-image. That was the
single most expensive step of the build, it depended on four third-party hosts staying up
and byte-stable, and nothing pinned the *content* of what was downloaded: two images built
a year apart could contain different sequence under the same name.

Reference sets are now built once, verified, and published here as versioned release
bundles. `vntyper install-references` and `Dockerfile.base` fetch them. See
[vntyper-org/VNtyper#217](https://github.com/vntyper-org/VNtyper/issues/217).

## The release spec

Each committed JSON file under [`releases/`](../releases/) pins:

- every upstream source URL, with its SHA-256 digest, and Ensembl pinned to an explicit
  release rather than `current`;
- the SHA-256 of every seed in [`seeds/`](../seeds/);
- every derivation, as the command that produces it and the SHA-256 it must produce;
- the VNtyper commit the builder was taken from, and the BWA and samtools versions used.

**The trust anchor is VNtyper, not this repository.** Every source URL and digest in a spec
must match the values committed in VNtyper's `install_references_config.json`, and the
build refuses to start if they disagree. To move an upstream, change VNtyper first in a
reviewed commit, then match the spec to it.

## The workflow

[`.github/workflows/release-data.yml`](../.github/workflows/release-data.yml) runs on
manual dispatch only. It invokes a full-SHA-pinned reusable workflow from the VNtyper
repository; the same SHA appears in `uses:` and `source_commit:`, and the builder refuses
to run if they differ. Building from upstream with
`vntyper install-references --from-source` is the path this workflow runs, so it stays
exercised.

The workflow creates a **draft** release only.

## Publication policy

Every release must contain, per assembly, a bundle carrying the chromosome FASTA, its BWA
index and a `BUILD_INFO.json`; the common MUC1 bundle; a top-level `SHA256SUMS`;
`release-manifest.json`; and `verification-report.json`. Publish only after the full
artifact set and its remote checksums have been reviewed: a maintainer reads
`verification-report.json` and checks `SHA256SUMS` against the remote assets before
publishing the draft.

Published releases are immutable. A builder change or a correction means a new
`refs-vN`, never a re-cut of an existing tag.
