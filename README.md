# Run `actions/cache@v3` so it always saves cache

The GitHub action `actions/cache@v3` only saves the cache if the job succeeds, and there has been interest in making it save the cache even if the job fails: 
https://github.com/actions/cache/issues/92

Rather than forking https://github.com/actions/cache, this repository demonstrates another way to accomplish the goal:

- Check out `actions/cache@v3` during the workflow run.
- Delete the condition `post-if: success()` (which prevents the `post` command from running if the job fails).
- Run the modified `actions/cache@v3` from its checked-out local path, rather than the original repository.

```yml
- name: Checkout actions/cache@v3
  uses: actions/checkout@v3
  with:
    repository: actions/cache
    ref: v3
    path: .tmp/actions/cache
- name: Make actions/cache@v3 run always, not only when job succeeds
  # Tweak `action.yml` of `actions/cache@v3` to remove its `post-if`
  # condition, making it default to `post-if: always()`.
  run: |
    sed -i -e '/ post-if: /d' .tmp/actions/cache/action.yml
- name: Cache data
  id: cache
  uses: ./.tmp/actions/cache
  with:
    ...
```

See `.github/workflows/cache-always.yml` for the full workflow file.
