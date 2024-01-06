# Bun: CDK Issue Reproduction
Reproduction of CDK issue in Bun introduced after Bun version 1.0.8.

## Initial Setup

We simply follow the [CDK Getting Started Guide](https://docs.aws.amazon.com/cdk/latest/guide/getting_started.html) to create a new CDK project.

```
$ mkdir app
$ cd app
$ bunx cdk init app --language typescript
```

which creates our scaffold.

We now change `app/cdk.json` to use `bun` instead of `npx` + `ts-node`:
