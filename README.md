# `tmp-gh-action-pr-context`

A quick playground to solve the problem of extracting the data from the `.context` of PR-related Github actions once and for all.

## PR1

Testing the initial action.

The hash of `PR1 commit1` is `876f123f51efe07e99c87209fe5f01692b37b099`.

The hash of `PR1 commit2` is `4d9540e555f92800d237d012d732f138f60397b8`.

From the [first action run](https://github.com/dkorolev/tmp-gh-action-pr-context/actions/runs/5938948010/job/16104386303):

```
cat c1.json | jq .eventName
"pull_request"

cat c1.json | jq .payload.number
1

cat c1.json | jq .payload.action
"opened"

cat c1.json | jq .payload.pull_request.head.sha
"876f123f51efe07e99c87209fe5f01692b37b099"
```

From the [second action run](https://github.com/dkorolev/tmp-gh-action-pr-context/actions/runs/5938957144/job/16104411074):

```
cat c2.json | jq .eventName
"pull_request"

cat c2.json | jq .payload.number
1

cat c2.json | jq .payload.action
"synchronize"

cat c2.json | jq .payload.after
"4d9540e555f92800d237d012d732f138f60397b8"
```

Lessons learned from PR1:

1. There are indeed differences between `"opened"` and `"synchronize"`.
2. The `HEAD` commit is totally broken, may be related to (3).
3. Need to upgrade the version of `actions/checkout@v2`, and need to properly do a shallow clone.

# PR2

Testing with `v3` of `actions/checkout`.

The hash of `PR2 commit1` is `2d7a6e2c4970be5cd68f9d9776b46a2e688c0ad8`.

The hash of `PR2 commit2` is `e5d43879a4e32fc39769b765ceb0587719fc705a`.

From the [first action run](https://github.com/dkorolev/tmp-gh-action-pr-context/actions/runs/5939156679/job/16104991679):

```
cat c1.json | jq .eventName
"pull_request"

cat c1.json | jq .payload.number
2

cat c1.json | jq .payload.action
"opened"

cat c1.json | jq .payload.pull_request.head.sha
"2d7a6e2c4970be5cd68f9d9776b46a2e688c0ad8"
```

From the [second action run](https://github.com/dkorolev/tmp-gh-action-pr-context/actions/runs/5939260109/job/16105302587):

```
cat c2.json | jq .eventName
"pull_request"

cat c2.json | jq .payload.number
2

cat c2.json | jq .payload.action
"synchronize"

cat c2.json | jq .payload.after
"e5d43879a4e32fc39769b765ceb0587719fc705a"
```

Lessons learned from PR2:

1. Need to change `s/base/head/` for `"opened"`.
2. The `v3` of the `actions/checkout` action yields no warnings.

## PR3

Testing end-to-end, fingers crossed.

From the [first action run](https://github.com/dkorolev/tmp-gh-action-pr-context/actions/runs/5939380782/job/16105646687), the commit ID is correct: `fb1c24d1d9d474f281886732d84f80397922c733`.

From the [second action run](https://github.com/dkorolev/tmp-gh-action-pr-context/actions/runs/5939400873/job/16105704900), the commit ID is correct: `8ccc473c4f413ba092c303a518a43846bf546e55`.

Lessons learned from PR3:

* This seems to work!

## PR4

Trying the shallow clone for real.

Verdict: `fetch-depth` of `1` is indeed the shallow clone.

The SHA of the HEAD commit is still off, but PRs one to three took care of this.

## PR5

Trying top-level commenting on PRs with a `COMMENTING_GITHUB_TOKEN` Github secret.

Lessons learned from PR5:

* `${{ github.repository_owner }}` is still the best way to get the owner. :-(
* Just "Discussions" is not enough permissions for the PAT token, adding "Pull Requests".
