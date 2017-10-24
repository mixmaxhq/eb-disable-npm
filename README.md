# eb-disable-npm

This package is the successor to https://github.com/mixmaxhq/eb-fix-npm/.
Even with that package's optimizations, we at Mixmax still found npm to be too
slow and memory intensive on Elastic Beanstalk, actually forcing us to use more
expensive EC2 instance types than our application would otherwise require.

So, we use this package's `.ebextensions` to prevent Elastic Beanstalk from
installing/rebuilding modules altogether. Instead, we deploy our Node modules
along with our application source from our CI server. What makes this possible is
that the CI server already installs the modules for the purposes of running our
tests, and uses the same CPU architecture as Elastic Beanstalk's EC2 instances.

This package has been tested against Elastic Beanstalk platform versions
[3.1.0 and 2.1.3](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/platform-history-nodejs.html).

## Installation

1. `npm install eb-disable-npm --save-dev` (see [here](https://github.com/mixmaxhq/install-files/blob/master/README.md#installation) for why `--save-dev`)
2. Commit the `.ebextensions` file it creates.

## Usage

`npm install` and deploy on a machine with the same architecture as your EC2
instances. If you use the [EB CLI](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3.html)
to deploy, either commit your Node modules or do

```sh
git add node_modules/ --force
eb deploy --staged
```

## Warning

You will not be able to change an Elastic Beanstalk's Node version in place,
since the EC2 instances won't rebuild their modules after the configuration
changes. Instead, you will have to clone the environment, change the version
(at which point the environment will become unhealthy), deploy modules built for
that version to that environment (healing the environment) and then swap the
environment URLs.

## Modifying the `.ebextensions` file

This package will overwrite the file if/when it is updated.

Pull requests are welcome if you have some generally-useful modifications to
suggest.

If you'd like to make modifications specific to your use case, you should uninstall
this package after installing the `.ebextensions` file. Uninstallation won't take
the file with it.

## Release History

* 1.0.0 Initial commit
