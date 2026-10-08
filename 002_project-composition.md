# Project composition

A specification project combines multiple specification packages into one
project-specific specification set.

The project composition lives at the root of `specs/` and should contain:

- `main.md`: the human-readable project specification entrypoint.
- `composition.yaml`: the machine-readable package composition.

`composition.yaml` is the conventional filename. `composition.yml` is also
accepted; use exactly one manifest per project. References to `composition.yaml`
in these rules also apply to that alternative spelling.

## Project kickoff

For a new project whose packages have not yet been imported, read
[Project kickoff](annex_project-kick-off.md). It explains how the definer copies
the `resources/project-kickoff.md` resource to `specs/project-kickoff.md` and
points to the copied file from `main.md`.
The initial entrypoint may contain just that kickoff instruction and any known
project intent. After kickoff, it must satisfy the normal composition rules
below. This temporary starting state does not require copying package folders.

## Repository agent entrypoint

A repository may include a root `AGENTS.md` file for tools or agents that look
for repository-level instructions before reading project files.

When present, root `AGENTS.md` should be extremely small and reusable. It should
point implementers to `specs/main.md` and tell them to follow the specification
composition, reading order, and authority rules defined there.

Root `AGENTS.md` should not describe the project, duplicate package rules, or
contain project-specific implementation instructions. Those belong in the
appropriate specification package or implementation documentation.

## README authority

`README.md` authority depends on where the file lives and what role metadata
assigns to it.

A package-level `README.md` is authoritative for that specification package
when the package's `package.yaml` declares it as the package entrypoint.

A repository-root `README.md` is repository documentation for people and tools
encountering the repository. It should not be treated as a specification
document merely because specification packages use package-level `README.md`
entrypoints.

If a project needs a root `README.md` update because package entrypoints changed,
that update should follow the repository documentation rules selected by the
project.

## Project entrypoint

The root `specs/main.md` describes the project-specific specification set. It
should be readable by humans and implementers before they enter individual
packages.

It should include:

- The project name or purpose.
- The specification packages used by the project.
- The selected version of each package.
- The selected local path of each package.
- The source or provenance of each package when relevant.
- The authority order between packages.
- The project-level reading order.
- Any project-specific vocabulary or interpretation rules.

The root `specs/main.md` may summarize package selection, but it should not
duplicate the detailed rules inside each package. When package details matter,
link or point to the package entrypoint.

## Composition metadata

The root `specs/composition.yaml` records the same package composition in a
structured form for validation, automation, and comparison across projects.

It should include:

- Project metadata.
- Package names.
- Package versions.
- Package paths.
- Package entrypoints.
- Package sources when relevant.
- Package dependency requirements when relevant.
- Package optional dependency requirements when relevant.
- Project authority order when it should be machine-readable.
- Project reading order when it should be machine-readable.

Package entries should use this shape:

```yaml
packages:
  - name: process
    version: 1.0.0
    path: 001_process/
    entrypoint: README.md
    dependencies:
      - name: meta
        version: 1.0.0
        constraint: compatible
```

`composition.yaml` should not replace package-level `package.yaml` files. It
records which package versions this project selected; each package still owns
its own metadata.

The package `path` may include ordering prefixes, grouping prefixes, or any
other project-local browsing convention. The path is the only field needed to
record where the package lives in the project.

## Package sources

A package entry may include `source` metadata that records where the local copy
comes from and how it should be synchronized.

Use `source` in `composition.yaml`, not in package-level `package.yaml`, because
source is a relationship between a specific project and its local package copy.
The same package version may be obtained from different places in different
projects.

Use `source.type: local` for packages owned only by the current project:

```yaml
packages:
  - name: project-local-specs
    version: 1.0.0
    path: 900_project-local-specs/
    entrypoint: README.md
    source:
      type: local
```

Use `source.type: git-subtree` for packages distributed from their own Git
repository and vendored into the current project:

```yaml
packages:
  - name: process
    version: 1.0.0
    path: 001_process/
    entrypoint: README.md
    source:
      type: git-subtree
      repository: https://github.com/example/specs-process.git
      branch: main
      tag: v1.0.0
      prefix: specs/001_process
```

For `git-subtree` sources, `repository`, `branch`, `tag`, and `prefix` are
required. The `prefix` identifies the subtree path used by Git and should usually
match the package path under `specs/`.

`tag` selects the release to import and must be `v<version>`, matching the
package's selected version. Resolve it explicitly as `refs/tags/<tag>` in the
source repository, then verify the tagged `package.yaml` name and version.
`branch` records the development branch used for checking future updates and
publishing authorized changes; it does not select the installed release.

Do not include `source.commit` in composition metadata. Commit IDs may be used
transiently to verify a fetched tag or perform a subtree import, but must not be
written back as an additional selector. A missing tag or mismatched package
version is a release metadata gap: report it and stop the affected import rather
than falling back to a branch or inventing a release tag.

During an approved update, import the selected release tag, validate the result,
and update `version` and `source.tag` together. Changing a selected tag is a
deliberate synchronization operation, not an ordinary kickoff rerun.

When migrating a legacy composition, replace `source.commit` or a branch-only
selection with the corresponding verified release tag. If the release has not
yet been tagged, record the pending publication in workflow documentation; do
not claim the declared tag is available or importable. Local package edits may
select the next planned version and tag during explicit specification work, but
release publication must verify and create that tag before consumers import it.

Package source and package version are separate concepts. The source describes
where the local package files came from and how to update or push them. The
version describes the semantic specification package version selected by the
project.

If a package is intended to be distributed from an external source but that
source does not exist yet, omit `source` until the source is real. Do not invent
placeholder repositories.

When a package has optional dependencies, `composition.yaml` should record them
under `optional_dependencies` with the same entry shape used for required
dependencies. A project may select both the package and its optional dependency
when it wants their integration rules to apply.

## Consistency

The root `specs/main.md`, root `specs/composition.yaml`, and each package's
`package.yaml` should agree about package names, versions, entrypoints, and
dependency constraints, including optional dependency constraints. Package
paths are owned by the project composition and should point to the local
directory that contains the matching `package.yaml`.

Package source metadata is owned only by `composition.yaml`; it is not expected
to appear in package-level `package.yaml`.

When changing a package version, update:

- The package's own `package.yaml`.
- The package entry in root `specs/main.md`.
- The package entry in root `specs/composition.yaml`.
- Any dependency requirement that intentionally changes as a result.

If these files disagree, treat it as a specification metadata gap. Ask the
definer or repair it during explicit specification work before relying on the
inconsistent metadata.

## Package independence

Project composition may combine independent packages, but it should not make a
package depend on another package implicitly.

If one package needs concepts, authority, or rules from another package, record
that dependency in the dependent package's `package.yaml` with a version
constraint.

If packages are merely used together by the project, list them together in
`composition.yaml` without adding package-level dependencies between them.
