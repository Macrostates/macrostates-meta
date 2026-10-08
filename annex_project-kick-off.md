# Annex: starting a new project

Read this guide when you, the definer, want to initialize a new project's
specification set from a composition manifest using an LLM implementer.

## Prepare the new project

1. Create a `specs/` directory in the new project.
2. Copy or write `composition.yaml` there. Select the packages for the new
   project and adapt its name, package sources, dependencies, authority, and
   reading order. `composition.yml` is also accepted; use only one filename.
3. Copy this package's [project-kickoff.md](resources/project-kickoff.md) resource to
   `./specs/project-kickoff.md`. Copy the file itself, not the complete package
   directory or another project's Git metadata.
4. Create `./specs/main.md` pointing the implementer to the copied resource.
   For example:

   ```markdown
   # My project specifications

   Initialize this project's specification set from `composition.yaml` by
   following [Project kickoff](project-kickoff.md).
   ```

5. Tell the LLM: "Read `specs/main.md` and perform the project kickoff."

The starting files are:

```text
specs/
  composition.yaml
  main.md
  project-kickoff.md
```

No Macrostates CLI installation is needed. The implementer needs filesystem and
command access, Git with subtree support, and access to the declared source
repositories. Private sources may require your existing Git authentication.

## Select sources deliberately

Each downloadable package needs a real source, for example:

```yaml
project:
  name: my-project
  entrypoint: main.md
packages:
  - name: meta
    version: 1.3.0
    path: 000_meta/
    entrypoint: README.md
    source:
      type: git-subtree
      repository: git@github.com:Macrostates/macrostates-meta.git
      branch: main
      tag: v1.3.0
      prefix: specs/000_meta
authority:
  - definer-latest-explicit-instruction
  - meta
reading_order:
  - 000_meta/README.md
```

This illustrates the manifest shape; choose a version actually available from
that source with a published `v<version>` release tag. `source.tag` selects
that release; `source.branch` identifies its development branch. Do not include
`source.commit`. Kickoff fetches the tag and verifies its package version; it
reports a missing tag instead of importing the latest branch contents.

Each published package version must have its own release tag in the package's
source repository. Publishing those tags is a release operation, not something
kickoff performs automatically. The example's tag must exist before use.

Do not carry another project's local package into the composition unless it is
intended for the new project. A `local` package requires its files or a separate
request to author its specifications. An entry with no source cannot be fetched.
Remove unwanted packages from dependencies, authority, and reading order too;
do not remove a package still required by another selected package.

## Expected result

The implementer establishes the project Git repository if necessary, imports
external packages as Git subtrees, validates package metadata, and expands
`specs/main.md` into the normal composition entrypoint. Subtree imports create
local commits; they do not create or publish new remote repositories.

The kickoff resource is a reusable instruction asset, not this package's README,
a core specification document, or a package selected in the composition. Its
copied instructions apply when project kickoff is requested. Ordinary package
reading does not trigger repository initialization.

After kickoff, `main.md` retains only a conditional reference to the resource.
Continue defining the new project's own specifications before requesting its
application implementation. If kickoff reports a source or version mismatch,
correct the manifest or supply the missing source and rerun; existing matching
imports are verified and skipped.
