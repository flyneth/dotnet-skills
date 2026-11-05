# Publishing the dsh bundle to npm

The `dotnet-skills` package in this repository is the DeepSeek Harness (dsh) bundle
described in `README.md`. It is published by
[`.github/workflows/publish-npm.yml`](../.github/workflows/publish-npm.yml) whenever a
GitHub release is published, using
[npm trusted publishing](https://docs.npmjs.com/trusted-publishers). No npm token is
stored in the repository or in GitHub secrets: the workflow exchanges a GitHub OIDC
token for a short-lived publish credential, and npm attaches provenance automatically.

## One-time setup on npmjs.com

Do this once, and only from the `flyneth/dotnet-skills` repository. Merge
`publish-npm.yml` to the default branch first: npm only accepts a workflow filename
that exists in the repository, and it validates the publisher when the first publish
runs, not when the settings are saved.

1. Sign in to npmjs.com as a package maintainer of `dotnet-skills`.
2. Open **Packages → dotnet-skills → Settings → Trusted publishing** and choose
   **GitHub Actions**. Every field is case-sensitive and must match exactly:
   - **Organization or user**: `flyneth`
   - **Repository**: `dotnet-skills`
   - **Workflow filename**: `publish-npm.yml` — the file name only, with its `.yml`
     extension, not a path.
   - **Environment name**: leave empty. The workflow does not declare an environment.
     If you later add protection rules, add the same name to the workflow's `publish`
     job.
3. Save, then immediately perform one release (see below). A new trusted publisher
   configuration **expires after 2 days** if its first publish does not succeed.
4. After the first OIDC publish succeeds, open **Settings → Publishing access** and
   select **Require two-factor authentication and disallow tokens**. Trusted
   publishing keeps working; manually stored write tokens stop working.

## Release checklist

1. Bump `version` in the repository root `package.json` to the version being released
   (for example `0.2.5`) and merge that change to `main`.
2. Create a GitHub release from that commit whose tag is `v<version>` — `v0.2.5` for
   version `0.2.5`. Publishing a draft release triggers the workflow.
3. Watch the `publish-npm` run. It validates the tag against `package.json`, packs the
   bundle, installs that tarball into a throwaway project and checks that it registers
   the same number of skills this repository ships, publishes, and then confirms the
   version is readable from the registry.

The workflow is idempotent: if the version is already on the registry it exits
successfully without publishing, so re-running it after a partial failure is safe.
A release tag that does not match `package.json` fails instead of guessing.

Prerelease versions (anything with a `-`, for example `0.3.0-rc.1`) are published
under the `next` dist-tag so they never move `latest`.

## Re-running a publish

Use **Actions → publish-npm → Run workflow** and enter the release tag (for example
`v0.2.5`). The run publishes that tag's `package.json` version, or reports that it is
already published.

npm does not allow overwriting a published version. If a published version is broken,
deprecate it (`npm deprecate dotnet-skills@0.2.5 "…"`) and release a new patch
version through the normal flow.

## Troubleshooting

| Symptom | Cause | Fix |
| --- | --- | --- |
| `ENEEDAUTH`, or npm reports it cannot authenticate | Trusted publisher fields do not match the run | Compare organization, repository and workflow filename character by character; confirm the job kept `id-token: write` and runs on a GitHub-hosted runner |
| `npm <version> cannot authenticate with trusted publishing` | Runner npm is older than 11.5.1 / Node older than 22.14.0 | Move the workflow's `node-version` forward |
| `Release tag 'vX' does not match package.json version 'Y'` | Version bump was not merged, or the tag is wrong | Bump `package.json`, merge, re-tag |
| `EPUBLISHCONFLICT` / `cannot publish over the previously published versions` | That version already exists | Release a new version; never re-tag an existing one |
| Trusted publisher configuration expired | No successful publish within 2 days of creating it | Delete the connection, create it again, publish within 2 days |
| No provenance attestation on the package page | Repository is private, or publishing used a token | Provenance requires a public repository plus OIDC publishing |

## Renaming or transferring the repository

Three places encode the repository identity and must change together: the `if:`
condition on the `publish` job in the workflow, the `repository.url` field in
`package.json` (npm requires it to match the GitHub repository exactly), and the
trusted publisher connection on npmjs.com. Existing connections cannot be edited;
delete and recreate them.
