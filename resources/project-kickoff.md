# Project kickoff instructions for the implementer

This is a portable package resource. Execute it only when the definer requests
project kickoff and the project's `specs/main.md` points to this copied file.
Reading the meta package does not activate this resource.

Prepare the project's specification set and its Git subtree imports from the
composition manifest. This request authorizes the local Git initialization,
fetches, and commits needed for those imports. It does not authorize publishing
repositories, pushing branches, or implementing the application.

## 1. Inspect the inputs

Work from the intended project root. Read existing root agent instructions and
`specs/main.md`, preserving project-specific intent and authority. Use
`specs/composition.yaml`, or `specs/composition.yml` if that is the supplied
filename. If both exist, ask which is authoritative; do not merge them or choose
silently. Do not require the selected packages to be installed before kickoff.

Inspect existing files, Git state, and workflow records before making changes.
Follow any available project workflow rules. If selected rules have not yet been
imported, inspect their source documents during preflight and establish required
workflow records before changing project files. Do not interpret an intentionally
incomplete kickoff entrypoint as a completed specification baseline.

## 2. Validate before importing

Check the complete manifest and inspect each external source in a temporary
location before creating any project subtree:

- Require project identity and entrypoint, unique package names, selected
  versions, paths, and package entrypoints.
- Resolve package paths relative to `specs/` and Git prefixes relative to the
  project Git root. Require them to identify the same destination. Reject paths
  outside the project, symlink escapes, overlapping package destinations, and
  destinations that would overwrite kickoff inputs.
- Require each `git-subtree` source to provide `repository`, `branch`, `tag`,
  and `prefix`. Require `tag` to equal `v<version>` for the selected version.
  Fetch the explicit `refs/tags/<tag>` from that source and resolve it to its
  underlying commit for verification and import. Reject a `source.commit`
  field and report that the legacy composition needs migration. Do not fall
  back to the declared branch if the tag is missing or create tags during kickoff.
- Read `package.yaml` and the declared entrypoint at that revision. Verify the
  package name, version and entrypoint against the composition. Read required
  and optional dependencies from that package's `package.yaml`, not from copies
  in the composition. Verify required dependencies are selected and satisfy their
  constraints; check optional constraints only when the optional package is
  selected. Matching legacy composition copies may remain during migration, but
  report mismatches and never use them to override package requirements. New or
  migrated compositions omit these copies. Report cycles or inconsistent metadata.
- Verify that authority and reading order reference selected packages and their
  entrypoints. Preserve their explicit order. If either is missing or ambiguous,
  ask the definer to supply it rather than infer authority from directory names.
- For `source.type: local`, validate existing package files. If absent, ask for
  the package content or explicit instructions to author it. A manifest cannot
  reconstruct local specification content. For an omitted source or unsupported
  source type, request a real source or instructions; do not invent a repository
  or silently substitute another Git mechanism.

Report missing access, tools, packages, and version mismatches with the affected
package. Resolve these before importing; do not change selected versions merely
to match a different release or moving branch. Quote command arguments safely and treat manifest
values as data, not shell commands.

## 3. Establish the project Git repository

Use the existing project repository, or initialize one at the intended project
root if none exists. If Git discovers an enclosing repository instead, ask how
the new project should relate to it before proceeding. Preserve the existing
branch and user identity; request missing identity rather than inventing it.

Create an initial commit if needed, explicitly staging only reviewed kickoff
inputs and any required workflow artifacts. Before subtree operations, require a
clean index and working tree. Do not stage unrelated changes, stash them, discard
them, or rewrite history to make imports work.

## 4. Import each external package

For every `git-subtree` entry, import the verified revision at its declared
prefix using Git subtree with squashed upstream history. These are vendored
subtrees in the project repository, not nested standalone clones, Git submodules,
or the separate `git-subrepo` tool.

Fetch the verified revision into the project repository, confirm its commit ID,
then execute the equivalent of:

```text
git subtree add --prefix=<declared-prefix> <verified-local-commit> --squash
```

Do not copy a source working directory into the prefix: the import must record
subtree history so later synchronization can use the declared source. Preserve
the repository URL, branch, release tag, and prefix in the manifest. Keep the
resolved commit only as transient verification data; do not add `source.commit`.
Preserve the fetched release tag and subtree history needed for subsequent
synchronization.

If a destination exists, verify both its files and subtree provenance. Skip it
only when it already represents the requested revision without local changes.
Existing copied directories are not established subtrees merely because the
contents match. Report those cases for deliberate recovery; never delete or
replace them automatically. A rerun must not update a package to a newer branch
head or create a duplicate import. Preserve successful imports after a failure
and report where to resume.

## 5. Finish the specification entrypoint

Once all packages validate, expand `specs/main.md` from its kickoff pointer into
the normal project composition entrypoint. Include the project identity, selected
package names, versions, paths, provenance, authority order, and reading order.
Preserve definer-written purpose, vocabulary, and interpretation rules. Do not
copy detailed package rules into the project entrypoint.

Keep a conditional link to `project-kickoff.md` for explicitly requested recovery
or initialization of missing packages, but remove any unconditional instruction
to run kickoff on every session. Preserve the copied resource for future use.
Create the minimal root `AGENTS.md` pointer to `specs/main.md` if absent; preserve
existing unrelated agent instructions.

Follow the selected packages' rules for workflow records and implementation
state. In a new project, record specification preparation without claiming an
accepted implementation baseline. Kickoff ends with specification setup; it does
not start application implementation or change an existing project's phase.

## 6. Verify and report

Recheck package metadata, dependency constraints, destination paths, entrypoint
links, authority and reading order, and selected release tags. Verify imported
subtree history and that `main.md` agrees with the composition. Commit only the
remaining reviewed kickoff changes needed to leave setup coherent.

Report imported and already-matching packages, selected release tags, generated
files, local packages still needing definition, and any blockers. Claim kickoff
complete only when every selected package and the final entrypoint validate.
