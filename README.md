# aws-assume-role
A simplistic github action that opens a session using aws sts assume-role, and exports its creds to the GITHUB_ENV of the current session. No tricks, no shtics, no dark magic.

The action is of type [*Composite*](https://docs.github.com/en/actions/creating-actions/about-custom-actions#composite-actions), and as such it assumes `aws-cli v2` and `jq` are present, for example - like they are for jobs that `runs-on: ubuntu-latest`.

## inputs:
| parameter | type | default | details |
|-|-|-|-|
| `target-role-name` | string | empty | when left empty - it is parsed from `target-role-arn` |
| `target-role-arn` | string | empty | when left empty - it is composed from AWS account as observed on current caller identity, and `target-role-arn` |
| `duration-seconds` | string expressing a number | '900' | used for `aws sts assume-role --duration-seconds` |
| `session-name` | string | empty | when left emtpy - composed as `{current-identity}-{commit-sha}@{target-role-name}` |
| `debug` | string | `'false'` | Increases verbosity. Any value other than `false` will cause increased verbosity. | 


# Usage

All examples require `AWS_ACCESS_KEY_ID`,  `AWS_SECRET_ACCESS_KEY` and `AWS_DEFAULT_REGION`, for a user or role that can impersonate.

You can provide them globally or on the job level, but it's adviced to pull secrets only to steps that need them, and the examples here will follow this rule to help copy-paste.

## providing role name
* account ID in this case is taken from the current caller-identity.

```
      - name:   AWS - assume infra role
        uses:   osher/aws-assume-role@v1
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_DEFAULT_REGION: us-east-1
        with:
          target-role-name: infra
```

## providing explicit arn

```
      - name:   AWS - assume infra role
        uses:   osher/aws-assume-role@v1
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_DEFAULT_REGION: us-east-1
        with:
          target-role-arn: arn:aws:iam::111222333444:role/infra
```
* this causes to ignore `target-role-name`

## define different session duration

```
      - name:   AWS - assume infra role
        uses:   osher/aws-assume-role@v1
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_DEFAULT_REGION: us-east-1
        with:
          target-role-name: infra
          duration-seconds: 3600
```

## provide explicit session name

* build it however you like. Make sure it's unique to avoid collisions.

```
      - name:   AWS - assume infra role
        uses:   osher/aws-assume-role@v1
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_DEFAULT_REGION: us-east-1
        with:
          target-role-name: infra
          session-name: my-special-job-${{ github.sha }}@infra
```

## increase verbosity with debug prints

* build it however you like...

```
      - name:   AWS - assume infra role
        uses:   osher/aws-assume-role@v1
        env:
          AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
          AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          AWS_DEFAULT_REGION: us-east-1
        with:
          target-role-name: infra
          debug: true
```

# alternatives
 - https://github.com/nordcloud/aws-assume-role


# contribution
 - gladly. feel free to propose - think generic and simplistic. :wink:
