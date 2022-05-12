This project demonstrates a problem with the latest `cimg/node:lts-browsers`
image.

The bottom line is that sometime on May 11, 2022, the image was updated and the
current setup got broken. The details are:
- the latest "good" image is
`cimg/node@sha256:c8ea4df07599f2c676c81f6dfecb22237da28428997589b28e8afa0afee8e96f`
- the current "bad" image is `cimg/node@sha256:c74edbc4f04bab52e47ca8a77e5a44cfb6241a13a87687262e554a345ac5359d`

## Reproduction

### Good old image

```sh
docker run -ti --rm -v `pwd`:/home/circleci/project cimg/node@sha256:c8ea4df07599f2c676c81f6dfecb22237da28428997589b28e8afa0afee8e96f
# Inside the image
bash
npm i
npm run test
# Result: the command succeeds and outputs `SUCCESS!`
```

Overall output should be as follows:

```sh
 $ docker run -ti --rm -v `pwd`:/home/circleci/project cimg/node@sha256:c8ea4df07599f2c676c81f6dfecb22237da28428997589b28e8afa0afee8e96f
$ _XSERVTransmkdir: ERROR: euid != 0,directory /tmp/.X11-unix will not be created.

$ bash
circleci@cea7ca1f3f90:~/project$ npm i

up to date, audited 3 packages in 454ms

found 0 vulnerabilities
circleci@cea7ca1f3f90:~/project$ npm run test

> @korya/min-circleci-setup@1.0.0 test
> npm --prefix packages/sub-project test


> @korya/sub-project@1.0.0 test
> ./test.sh

v16.15.0
SUCCESS!
npm notice
npm notice New minor version of npm available! 8.5.5 -> 8.10.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v8.10.0
npm notice Run npm install -g npm@8.10.0 to update!
npm notice
npm notice
npm notice New minor version of npm available! 8.5.5 -> 8.10.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v8.10.0
npm notice Run npm install -g npm@8.10.0 to update!
npm notice
```

### Bad new image

```sh
docker run -ti --rm -v `pwd`:/home/circleci/project cimg/node@sha256:c74edbc4f04bab52e47ca8a77e5a44cfb6241a13a87687262e554a345ac5359d
# Inside the image
bash
npm i
npm run test
# Expected result: the command succeeds and outputs `SUCCESS!`
# Actual result: the command fails with
#     /usr/bin/env: ‘node’: No such file or directory
```

Overall output I get is:

```sh
$ docker run -ti --rm -v `pwd`:/home/circleci/project cimg/node@sha256:c74edbc4f04bab52e47ca8a77e5a44cfb6241a13a87687262e554a345ac5359d
$ _XSERVTransmkdir: ERROR: euid != 0,directory /tmp/.X11-unix will not be created.
bash
circleci@406d4bbc3b6c:~/project$ npm i

up to date, audited 3 packages in 467ms

found 0 vulnerabilities
circleci@406d4bbc3b6c:~/project$ npm run test

> @korya/min-circleci-setup@1.0.0 test
> npm --prefix packages/sub-project test


> @korya/sub-project@1.0.0 test
> ./test.sh

nvm is not compatible with the "npm_config_prefix" environment variable: currently set to "/home/circleci/project/packages/sub-project"
Run `unset npm_config_prefix` to unset it.
./test.sh: line 5: node: command not found
/usr/bin/env: ‘node’: No such file or directory
npm notice
npm notice New minor version of npm available! 8.5.5 -> 8.10.0
npm notice Changelog: https://github.com/npm/cli/releases/tag/v8.10.0
npm notice Run npm install -g npm@8.10.0 to update!
npm notice
```

### Latest image

At this moment, 2022-05-12 08:00 ET, the latest `cimg/node@lts-browsers` is
broken.

```sh
docker run -ti --rm -v `pwd`:/home/circleci/project cimg/node:lts-browsers
# Inside the image
bash
npm i
npm run test
# Expected result: the command succeeds and outputs `SUCCESS!`
# Actual result: the command fails with
#     /usr/bin/env: ‘node’: No such file or directory
```
