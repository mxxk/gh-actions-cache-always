## "Live patching" of `actions/cache@v3` to let it always cache

GitHub's action `actions/cache@v3` only saves data in the cache, if the job succeeds.  There has been much interest in letting `actions/cache` save data in the cache even if the job fails, see [its issue \#92](https://github.com/actions/cache/issues/92)<br />
Note that GitHub has provided [an alternative solution](https://github.com/actions/cache/discussions/1020) by creating two new actions [`actions/cache/save`](https://github.com/actions/cache/tree/main/save) and [`actions/cache/restore`](https://github.com/actions/cache/tree/main/restore) in December 2022, but this requires significant changes to extant workflows which use GitHub's classic [`actions/cache`](https://github.com/actions/cache), plus all three actions continue to be maintained by GitHub (so it was indicated in this "Conclusion"](https://github.com/actions/cache/discussions/1020#discussion-4635717)).

Rather than forking and adapting GitHub's `actions/cache`, which creates a maintenance burden, this repository demonstrates "live patching" of the original `actions/cache@v3` on every job run to accomplish this goal, see [.github//actions/cache-always.yml](https://github.com/mxxk/gh-actions-cache-always/blob/main/.github/actions/cache-always/action.yml).  It does …

- … [check out `actions/cache@v3` to `./.github/.tmp/actions/always-cache`](https://github.com/mxxk/gh-actions-cache-always/blob/main/.github/actions/cache-always/action.yml#L27-L32)
- … [patch `post-if: success()` to `post-if: ${{ success() || failure() }}`](https://github.com/mxxk/gh-actions-cache-always/blob/main/.github/actions/cache-always/action.yml#L34-L35) to let the patched action cache data, even if the job fails, but not when it is cancelled.
- … transparently map the "live patched" action to the patching action `.github/actions/cache-always/action.yml` by passing through all parameters, so the latter can be called.

Ultimately one simply copies [.github//actions/cache-always/action.yml](https://github.com/mxxk/gh-actions-cache-always/blob/main/.github/actions/cache-always.yml) from this repository directly to the same location of one's own repository and uses it in one's own workflows in `.github/workflows`.

For an example of a workflow using the "live patched" `actions/cache@v3` in `.github/actions/cache-always` see [.github/workflows/demo-cache-always.yml](https://github.com/mxxk/gh-actions-cache-always/blob/main/.github/workflows/demo-cache-always.yml).

P.S.: Thanks to user [DrJume](https://github.com/DrJume) for his [enhancements of the original version here](https://github.com/actions/cache/issues/92#issuecomment-1263067512): These have been intergrated and further improved.
