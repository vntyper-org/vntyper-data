# Release history

Reference data is released here with tags of the form `refs-vN`; VNtyper software is
released from [vntyper-org/VNtyper](https://github.com/vntyper-org/VNtyper). Each tag's
full notes are on its [GitHub Release page](https://github.com/vntyper-org/vntyper-data/releases).
`gh release list --repo vntyper-org/vntyper-data` lists the current set.

## refs-v3: standard MUC1 length model for GRCh38

Publishes `grch38-standard-length-model-v1.json` and its `.sha256` file. The asset is
byte-identical to [`seeds/grch38-standard-length-model-v1.json`](../seeds/grch38-standard-length-model-v1.json),
which is the copy VNtyper installs; the model is described in
[`seeds/LENGTH_MODEL.md`](../seeds/LENGTH_MODEL.md). The `.sha256` file holds the bare
digest, so check it with:

```bash
echo "$(cat grch38-standard-length-model-v1.json.sha256)  grch38-standard-length-model-v1.json" | sha256sum --check
```

## refs-v2: corrected GRCh38 adVNTR model

Consumed by [VNtyper v2.0.21](https://github.com/vntyper-org/VNtyper/releases/tag/v2.0.21)
and later. It corrects the GRCh38 adVNTR model, which described 840 bp of a repeat array
GRCh38 carries at 3,525 bp. adVNTR derives its read-fetch window from the model's own
content, so it saw 24% of the locus. Measured over 400 simulated samples, detection goes
from 132/200 carriers to 176/200. **Requires adVNTR >= 2.0.4.**

Only the MUC1 common asset changed. The six per-assembly bundles are the `refs-v1`
artifacts byte-for-byte, republished under the new tag keeping their original file names
and digests, so their own `BUILD_INFO.json` and manifests stay truthful about when and
from what they were built. `verification-report.json` records that check.

The models are now derived rather than shipped as an opaque blob:
[`seeds/derive_advntr_muc1_model.py`](../seeds/derive_advntr_muc1_model.py) builds them
from chr1 plus pinned array bounds, and reproduces the previous hg19 database content
byte-for-byte: 9 differing bytes, all SQLite header, zero content bytes. See
[vntyper-org/VNtyper#268](https://github.com/vntyper-org/VNtyper/issues/268) and
[vntyper-org/vntyper-data#1](https://github.com/vntyper-org/vntyper-data/issues/1).

`refs-v2` was cut published rather than as a draft, skipping the review step described in
[building a release](building.md). That deviation and the verification available for
post-hoc review are recorded in the release notes. `refs-v1` remains published and
untouched, so installations pinned to it are unaffected.

## refs-v1: first bundle release

Specified in [`releases/refs-v1.json`](../releases/refs-v1.json) and built by
[`.github/workflows/release-data.yml`](../.github/workflows/release-data.yml), which
dispatches VNtyper's reusable builder pinned at `83059ee` (the merge commit of
[vntyper-org/VNtyper#241](https://github.com/vntyper-org/VNtyper/pull/241), which follows
[#239](https://github.com/vntyper-org/VNtyper/pull/239) and
[#240](https://github.com/vntyper-org/VNtyper/pull/240)). Contains, per assembly (UCSC
hg19/hg38, NCBI GRCh37/GRCh38, Ensembl hg19/hg38), the chr1 FASTA with its BWA index and
`BUILD_INFO.json`, plus the common MUC1 bundle.
