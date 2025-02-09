---
sidebar_position: 2
---

# Build

<head>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta2/css/all.min.css" />
</head>

Cyclic apps are built and deployed into AWS from code that originates in Github.

To trigger a build and deployment, install the [Cyclic Github app](https://github.com/apps/cyclic-sh) on any public repo. On first installation we will assign your repo to an app and give it a public url such as `random-words.cyclic.app`. We will then build and deploy your app as described below. On future commits to the default branch (for example: merges of PRs or pushes) Github will send a webhook api call to us. We will then perform the same build and deploy steps.

The input of the **build** step is a single commit in a git repo and the output is a zip archive that can be deployed and run inside of an AWS lambda with the Cyclic lambda runtime.

<div style={{textAlign: 'center'}}>

![Cyclic Github app installation API call pattern](/img/diagram/build-process.drawio.png "Cyclic Github app installation")

</div>

## Code

The following is a close approximation of the code we use to take your code repo and turn it into an artifact that can be run inside of AWS Lambda with the Cyclic lambda runtime.

**Note**: we use `npm` version 8.13.1 throughout.

- `npm install`: Install your code's dependencies (including `devDependencies`)
- `npm run build`: Build your app if you have a `build` script defined in `package.json`
- `npm prune`: Prune any `devDependencies` to minimize the size of the output zip
- `zip`: Zip all of your code and installed dependencies (for example `node_modules`) recursively

**Note**: we have limits on the maximum amount of disk space used for code + dependencies while building and on the final bundle size. See [limits page](/overview/limits) for more details.

## Customize the build process

NPM executes several [lifecycle scripts](https://docs.npmjs.com/cli/v8/using-npm/scripts#npm-install) as part of the `npm install` and `npm run build`. According to the documentation these are run in the following order:

**`npm install`**
- `preinstall`
- `install`
- `postinstall`
- `prepublish`
- `preprepare`
- `prepare`
- `postprepare`

**`npm run build`**
- `prebuild`
- `build`
- `postbuild`

If you have particular needs try putting it into the appropriate lifecycle script. If you have needs beyond what these lifecycle scripts can provide or they don't solve for your use case send us an email: <i className="far fa-envelope"></i> hello@cyclic.sh or join us on [<i className="fab fa-discord"></i>  Discord](https://discord.gg/huhcqxXCbE)
