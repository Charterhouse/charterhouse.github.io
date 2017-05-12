---
layout: "post"
title: "Running Truffle Unit Tests in IntellIJ IDEA"
date: "2017-05-12"
author: Stefan van den Oord
---

Today I succeeded in running my Truffle unit tests inside IntellIJ IDEA. Let me
tell you how I did it, so that you can use it yourself. Warning: it does involve
some hacky workarounds. I'll close this post with a request to the Truffle
people about how running the tests would work ideally.

Please note that I'm using [TruffleDevEnv][1] for my Truffle development. I'm
one of the maintainers of it, and we added the IntellIJ run configuration to
TruffleDevEnv. Below I describe the steps you need to take if you want to do
this yourself in some other context.

### Creating The Run Configuration

When you run `truffle test`, a few things happen:
- the smart contracts are compiled and deployed, so that the tests can use them;
- your tests are 'surrounded' by some code that does proper setup and teardown,
  and makes some test utilities available;
- Mocha is used to run the tests.

So if we want IntellIJ IDEA to be able to understand the output of the test run,
we need to create a run configuration of the type 'Mocha'. In that run
configuration, you configure your node interpreter, the working directory,
the actual node module directory that contains Mocha, and a few other things.
IntellIJ IDEA will use all that information to compose a shell command that runs
the tests. What I did is to configure the run configuration in such a way that
IntellIJ IDEA creates a shell command that does what we need to do.

#### Node Options

As node options, I used: `-r truffle/cli truffle test`. This will cause the
Truffle cli running code to be included, and then `truffle test` is passed as
command line arguments to the CLI, just like they normally are.

#### Configure Test Files
If you run the tests now, you will see that the contracts are compiled, but
the tests are not actually run. The problem is that IntellIJ IDEA adds a few
more command line options (ui and reporter), which are now passed as input
to Truffle, causing it to misunderstand them as a specification of the tests
that need to be run. The only way I found to fix that, is to configure the
test files explicitly in the run configuration.

Ideally, you'd want to type `test/**/*` or something along those lines, but
as it turns out, the wildcards are not expanded. So I had to manually put in
all test source code files there. Definitely not what you want, but I couldn't
find a better way so far.

The final run configuration looks like this:

![Truffle Run Configuration](/images/2017/05/TruffleRunConfiguration.png)

#### Configuring The Reporter
The shell command that IntellIJ IDEA creates, contains a 'reporter' parameter.
That is normally used by Mocha and will cause it to generate output in the
format that IntellIJ IDEA expects and can parse. Because of our changes, this
parameter no longer ends up in the Mocha options.

Fortunately, Truffle allows (some) customization of Mocha by adding a section
for it in `truffle.js`. I put the following code in there.

```
let truffleOptions = {
  networks: {
    ropsten: {
      host: 'localhost',
      port: 8546,
      network_id: 3,
      gas: 4000000
    },
    development: {
      host: 'localhost',
      port: 8545,
      network_id: '*' // Match any network id
    }
  }
}

let reporterArg = process.argv.indexOf('--reporter')               (1)
if (reporterArg >= 0) {                                            (2)
  truffleOptions['mocha'] = {
    reporter: process.argv[reporterArg + 1]                        (3)
  }
}

module.exports = truffleOptions
```

This causes Truffle to detect that there is a command line parameter
`--reporter` (1). If it is found (2), then the reporter is added to the
Truffle options (3).

Success!!

### Closing Note

What's causing all this headache is the fact that on the one hand IntellIJ IDEA
expects to be able to call mocha and pass command line parameters to it.
Whereas on the other hand Truffle requires tests to be run using its own
command line utility `truffle test`.

I'm by no means a mocha expert, but I have also used it like this:

```
mocha --compilers js:babel-register
```

This causes Babel to be used to process the test sources before the tests are
run. I don't know enough about it to be sure, but I would hope this mechanism
to be powerful enough to allow the Truffle logic to be added as well. So maybe
something like this:

```
mocha --compilers sol:truffle js:truffle
```

It would be awesome if the Truffle developers could look into this and maybe
fix it a much better way!


[1]: https://github.com/Charterhouse/TruffleDevEnv
