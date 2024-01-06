# Bun: CDK Issue Reproduction
Minimal reproduction of CDK issue in Bun introduced after Bun version 1.0.8. This issue occurs when calling any `cdk` command, e.g. `bun cdk synth`.

- Latest known working version: `1.0.8` (`curl -fsSL https://bun.sh/install | bash -s "bun-v1.0.8"`)
- Tested with latest release as of 6th of Jan, 2024: `1.0.21` (and all version in-between)

First clone this repository and install dependencies:

```bash
$ git clone git@github.com:codetalkio/bun-issue-cdk-repro.git
$ cd bun-issue-cdk-repro/app
$ bun --revision
1.0.21+837cbd60d
$ bun install
...
```

The run `bun cdk synth` while still in the `bun-issue-cdk-repro/app` directory:

```bash
$ bun run cdk synth
cdk synth

Subprocess exited with error null
error: script "cdk" exited with code 1 (SIGHUP)
```


## Initial Setup

We simply follow the [CDK Getting Started Guide](https://docs.aws.amazon.com/cdk/latest/guide/getting_started.html) to create a new CDK project.

```bash
$ mkdir app
$ cd app
$ bunx cdk init app --language typescript
```

which creates our scaffold.

We now change `app/cdk.json` to use `bun` instead of `npx` + `ts-node`:

```diff
-  "app": "npx ts-node --prefer-ts-exts bin/app.ts",
+  "app": "bun bin/app.ts",
```

We remove `package-lock.json` and `node_modules`, and reinstall dependencies with `bun`:

```bash
$ rm app/package-lock.json
$ rm -r app/node_modules
$ cd app
$ bun install
```

## Reproduction

You can now run any `cdk` command such as `bun cdk synth` and it will fail with `Subprocess exited with error null`:

```bash
$ cd bun-issue-cdk-repro/app
$ bun --revision
1.0.21+837cbd60d
$ bun run cdk synth
cdk synth

Subprocess exited with error null
error: script "cdk" exited with code 1 (SIGHUP)

$ echo $?
1
```

If you install `bun` version `1.0.8` and run `bun cdk synth` it will work as expected.

```bash
$ cd bun-issue-cdk-repro/app
$ bun --revision
1.0.8+2a405f691e80725fe0b97b93afd3b8cfed13fa5f
$ bun run cdk synth
cdk synth
Resources:
  CDKMetadata:
    Type: AWS::CDK::Metadata
    Properties:
      Analytics: v2:deflate64:H4sIAAAAAAAA/zPSMzS00DNQTCwv1k1OydbNyUzSqw4uSUzO1nFOywtKLc4vLUpOBbGd8/NSMksy8/NqdfLyU1L1sor1y4wM9ECas4ozM3WLSvNKMnNT9YIgNADUwEUzWQAAAA==
    Metadata:
      aws:cdk:path: AppStack/CDKMetadata/Default
    Condition: CDKMetadataAvailable
Conditions:
  CDKMetadataAvailable:
    Fn::Or:
      - Fn::Or:
...

$ echo $?
0
```
