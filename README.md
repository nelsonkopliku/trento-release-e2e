# trento-release-e2e

Test repository for end-to-end tests of the trento-project/.github release
(`git-release.yaml`) and OBS sync (`obs-sync.yaml`) workflows. Nothing here is
a real release.

## Branches, tags and releases

- `baseline`, the default branch, holds the workflows and the files that every case starts from.
- `main` and `release` hold the state of the last case. Each release case resets `main` to `baseline` and deletes `release`.
- The tags and the GitHub releases of every case stay. The version names the case and the workflow: the major number is the case (R1 to R6), and the minor number is 10 for v1.11.0 and 20 for [#56](https://github.com/trento-project/.github/pull/56). For example, `1.20.0` is case R1 with #56, and `5.20.1` is the hotfix release of case R5 with #56.

## Release cases

These cases test [trento-project/.github#56](https://github.com/trento-project/.github/pull/56). Each case runs a full or hotfix release through one of two workflows:

- `release-v1.11.0.yaml`: the current workflow, [`trento-project/.github@94dac57`](https://github.com/trento-project/.github/blob/94dac57211d792dda649383e7f23326b24a9087b/.github/workflows/git-release.yaml) (v1.11.0).
- `release-pr56.yaml`: the workflow of [#56](https://github.com/trento-project/.github/pull/56), from the [`e2e/ours`](https://github.com/nelsonkopliku/trento-dot-github/tree/e2e/ours) branch of a fork. That branch merges #56 with [#57](https://github.com/trento-project/.github/pull/57).

Most cases change `assets/package-lock.txt`, a small lock file with one package on each line. It recalls web 3.2.0, where a backport that existed only on `release` leaked into the package lock of the release. "main's tree" means that the tagged tree is identical to `main`, and that `main` and `release` end on the tag. "diff to main" compares `main` before the release with the release: it must show no changed files.

The table below comes from the previous run, with the earlier `e2e/release-script` branch at `e99be81`. A new run is in progress.

| Case | Scenario | Real-world example | v1.11.0 | [#56](https://github.com/trento-project/.github/pull/56) |
|---|---|---|---|---|
| R1 | Backport only on `release`; `main` changes the same file elsewhere | web 3.2.0: backports [web#4768](https://github.com/trento-project/web/pull/4768) and [web#4756](https://github.com/trento-project/web/pull/4756) leaked ([web#4945](https://github.com/trento-project/web/pull/4945)) | ❌ [leaks](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39610498435) | ✅ [main's tree](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39919568652) |
| R2 | The same line changed on both branches | A backport that conflicts with a later change on `main` | ✅ [main's tree](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39611125447) | ✅ [main's tree](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39919683038) |
| R3 | A file changed on `release` and deleted on `main` | A backported file that `main` later removes | ❌ [release fails](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39611693309) | ✅ [main's tree](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39919794881) |
| R4 | First release, no `release` branch | The first release of a new repository | ✅ [main's tree](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39612243679) | ✅ [main's tree](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39919919806) |
| R5 | Full release, hotfix release, next full release | A hotfix such as web 3.1.6, then 3.2.0 | ✅ [full](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39612822424), [hotfix](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39612960078), [next](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39613154147) | ✅ [full](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39920043793), [hotfix](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39920145806), [next](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39920241079) |
| R6 | Release deleted, then the same run re-run | The web 3.2.0 recreation | ✅ [run and re-run](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39614493814) | ✅ [run and re-run](https://github.com/nelsonkopliku/trento-release-e2e/actions/runs/39920370679) |

An earlier version of #56 built the merge commit with `git commit-tree`. It passed the same cases. Review asked for a command that is easier to read, so #56 uses `-s ours`.

## Container sync cases

The pull requests of this repository are the cases for the OBS sync of [trento-project/.github#57](https://github.com/trento-project/.github/pull/57). Pull requests #17 to #24 are the final run.
