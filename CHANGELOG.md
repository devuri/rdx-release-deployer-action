# Changelog

## [0.3.2](https://github.com/devuri/rdx-release-deployer-action/compare/v0.3.1...v0.3.2) (2024-07-17)


### Bug Fixes

* only check `inputs.use-remote-install` Update action.yml ([a01ac9b](https://github.com/devuri/rdx-release-deployer-action/commit/a01ac9bacccb9e6183458d576313e1b5dd22e3ca))

## [0.3.1](https://github.com/devuri/rdx-release-deployer-action/compare/v0.3.0...v0.3.1) (2024-07-16)


### Bug Fixes

* `include-component-in-tag` set to `false` Update release-please-config.json ([2a8fa91](https://github.com/devuri/rdx-release-deployer-action/commit/2a8fa913ad0d418ce353cf181e49ab725c57e033))

## [0.3.0](https://github.com/devuri/rdx-release-deployer-action/compare/v0.2.1...v0.3.0) (2024-07-15)


### ⚠ BREAKING CHANGES

* `use-ssh-updates` is now `use-remote-install` and `default false`

### Features

* `use-ssh-updates` is now `use-remote-install` and `default false` ([51965db](https://github.com/devuri/rdx-release-deployer-action/commit/51965dbf5b3f55a2c6c8c91984c7e659f57e38e4))
* adds `upload-artifact` with `actions/upload-artifact@v4` ([84868bc](https://github.com/devuri/rdx-release-deployer-action/commit/84868bc468de81aa290d9345a7a8b2576b4313fe))
* adds cache to speed up the workflow. ([b04c447](https://github.com/devuri/rdx-release-deployer-action/commit/b04c4474c736d1aad6045bd8969999d2482afed4))
* adds url in pull request ([53deaba](https://github.com/devuri/rdx-release-deployer-action/commit/53deaba02b5b328a37753d8ada88087d926c0ec0))


### Bug Fixes

* cache keys Update action.yml ([c8735e7](https://github.com/devuri/rdx-release-deployer-action/commit/c8735e7c92e7928106a6b689820dbf1e0bb1e8aa))
* make cache optional ([22ff136](https://github.com/devuri/rdx-release-deployer-action/commit/22ff1369f8f9aee50d62185866db9c88172938d5))
* make sure site url is linked ([1d694cf](https://github.com/devuri/rdx-release-deployer-action/commit/1d694cfd79c8bd8936451cc5dd73dac6136599be))

## [0.2.1](https://github.com/devuri/rdx-release-deployer-action/compare/v0.1.8...v0.2.1) (2024-07-13)


### Bug Fixes

* update release ([a05982f](https://github.com/devuri/rdx-release-deployer-action/commit/a05982f1f0ff66acb696f543ee6f6e54500f995d))

## [0.1.8](https://github.com/devuri/rdx-release-deployer-action/compare/v0.1.7...v0.1.8) (2024-07-13)


### Features

* updates optionals for more flexibility ([3415b08](https://github.com/devuri/rdx-release-deployer-action/commit/3415b08f8933719ee8c0a98ed31bafc1669063de))


### Bug Fixes

* add `shell: bash` ([67f5c02](https://github.com/devuri/rdx-release-deployer-action/commit/67f5c02c42d766b43e5c4351058bcbe0d66489ed))
* add name ([2425bf2](https://github.com/devuri/rdx-release-deployer-action/commit/2425bf2d2552573e9f72d5b7fd2e89938433319c))
* add tag name to readme ([43e5fb3](https://github.com/devuri/rdx-release-deployer-action/commit/43e5fb3593f7bdacc681ccbf3c738cd1a41859be))
* bash 80 - 83 ([d9c199a](https://github.com/devuri/rdx-release-deployer-action/commit/d9c199a34f4a0d76cb045c1426f200af1540e4e2))
* bash indent ([fde47a5](https://github.com/devuri/rdx-release-deployer-action/commit/fde47a5b0279d97201c531886d2cda512621d9e9))
* do not use `--delete` NOT RECOMMENDED ([2ac10ec](https://github.com/devuri/rdx-release-deployer-action/commit/2ac10ec8e05f85eeb943526b89c19ebec38802b7))
* minor fix update action.yml ([fb256b1](https://github.com/devuri/rdx-release-deployer-action/commit/fb256b1de0ca10611d250fba6f858217e132d5c8))
* rel type `node` ([d76d117](https://github.com/devuri/rdx-release-deployer-action/commit/d76d117acb7e1ba5c4be8e2aed34487f1823b3aa))
* release-type": "javascript" ([c26e18b](https://github.com/devuri/rdx-release-deployer-action/commit/c26e18b6f5fb9e645d780135328bd189d99b3d32))
* remove `--delete` ([0a0584a](https://github.com/devuri/rdx-release-deployer-action/commit/0a0584ac35c4925507a52ddafdd16e7056b7f3cc))
* removes `--delete` too dangerous to be the default value Update action.yml ([8a36d58](https://github.com/devuri/rdx-release-deployer-action/commit/8a36d58b0b035f49569e13a5db617b4c37e51c82))
* table structure in updated README.md ([d6bdc51](https://github.com/devuri/rdx-release-deployer-action/commit/d6bdc51effa7c6c8b2f5829cec3e5a97f8538d62))
* Update action.yml ([915fe3b](https://github.com/devuri/rdx-release-deployer-action/commit/915fe3bb1b12490d02c922286458e9377a0ce455))
* update readme ([3f627b2](https://github.com/devuri/rdx-release-deployer-action/commit/3f627b29ded49a0a6269385e04a728367be831e1))
* Update README.md add basic example ([900f767](https://github.com/devuri/rdx-release-deployer-action/commit/900f7675c5a670d6e78eeb33b4221ae91f08b95d))
* Update README.md examples ([75fc6be](https://github.com/devuri/rdx-release-deployer-action/commit/75fc6be29ebe34632fc2a495c9703fd309c2f3ee))
* update repo name in files ([20cb671](https://github.com/devuri/rdx-release-deployer-action/commit/20cb6712ef387a1d9ff0c101cccbe5d8b61a460d))
* update shell: bash ([edf231a](https://github.com/devuri/rdx-release-deployer-action/commit/edf231ae27e6b42a73d96bae37b9387b37c44294))

## [0.1.7](https://github.com/devuri/rdx-release-deployer-action/compare/rdx-release-deployer-action-v0.1.6...rdx-release-deployer-action-v0.1.7) (2024-07-13)


### Bug Fixes

* Update action.yml ([915fe3b](https://github.com/devuri/rdx-release-deployer-action/commit/915fe3bb1b12490d02c922286458e9377a0ce455))

## [0.1.6](https://github.com/devuri/rdx-release-deployer-action/compare/rdx-release-deployer-action-v0.1.5...rdx-release-deployer-action-v0.1.6) (2024-07-13)


### Features

* updates optionals for more flexibility ([3415b08](https://github.com/devuri/rdx-release-deployer-action/commit/3415b08f8933719ee8c0a98ed31bafc1669063de))


### Bug Fixes

* minor fix update action.yml ([fb256b1](https://github.com/devuri/rdx-release-deployer-action/commit/fb256b1de0ca10611d250fba6f858217e132d5c8))
* removes `--delete` too dangerous to be the default value Update action.yml ([8a36d58](https://github.com/devuri/rdx-release-deployer-action/commit/8a36d58b0b035f49569e13a5db617b4c37e51c82))
* table structure in updated README.md ([d6bdc51](https://github.com/devuri/rdx-release-deployer-action/commit/d6bdc51effa7c6c8b2f5829cec3e5a97f8538d62))
* Update README.md examples ([75fc6be](https://github.com/devuri/rdx-release-deployer-action/commit/75fc6be29ebe34632fc2a495c9703fd309c2f3ee))

## [0.1.5](https://github.com/devuri/rd-release-deployer-action/compare/rdx-release-deployer-action-v0.1.4...rdx-release-deployer-action-v0.1.5) (2024-07-13)


### Bug Fixes

* add `shell: bash` ([67f5c02](https://github.com/devuri/rd-release-deployer-action/commit/67f5c02c42d766b43e5c4351058bcbe0d66489ed))
* add name ([2425bf2](https://github.com/devuri/rd-release-deployer-action/commit/2425bf2d2552573e9f72d5b7fd2e89938433319c))
* add tag name to readme ([43e5fb3](https://github.com/devuri/rd-release-deployer-action/commit/43e5fb3593f7bdacc681ccbf3c738cd1a41859be))
* bash 80 - 83 ([d9c199a](https://github.com/devuri/rd-release-deployer-action/commit/d9c199a34f4a0d76cb045c1426f200af1540e4e2))
* bash indent ([fde47a5](https://github.com/devuri/rd-release-deployer-action/commit/fde47a5b0279d97201c531886d2cda512621d9e9))
* do not use `--delete` NOT RECOMMENDED ([2ac10ec](https://github.com/devuri/rd-release-deployer-action/commit/2ac10ec8e05f85eeb943526b89c19ebec38802b7))
* rel type `node` ([d76d117](https://github.com/devuri/rd-release-deployer-action/commit/d76d117acb7e1ba5c4be8e2aed34487f1823b3aa))
* release-type": "javascript" ([c26e18b](https://github.com/devuri/rd-release-deployer-action/commit/c26e18b6f5fb9e645d780135328bd189d99b3d32))
* remove `--delete` ([0a0584a](https://github.com/devuri/rd-release-deployer-action/commit/0a0584ac35c4925507a52ddafdd16e7056b7f3cc))
* update readme ([3f627b2](https://github.com/devuri/rd-release-deployer-action/commit/3f627b29ded49a0a6269385e04a728367be831e1))
* update repo name in files ([20cb671](https://github.com/devuri/rd-release-deployer-action/commit/20cb6712ef387a1d9ff0c101cccbe5d8b61a460d))

## [0.1.4](https://github.com/devuri/rd-web-app-release-deployer-action/compare/rd-web-app-release-deployer-action-v0.1.3...rd-web-app-release-deployer-action-v0.1.4) (2024-07-12)


### Bug Fixes

* add tag name to readme ([43e5fb3](https://github.com/devuri/rd-web-app-release-deployer-action/commit/43e5fb3593f7bdacc681ccbf3c738cd1a41859be))
* do not use `--delete` NOT RECOMMENDED ([2ac10ec](https://github.com/devuri/rd-web-app-release-deployer-action/commit/2ac10ec8e05f85eeb943526b89c19ebec38802b7))
* remove `--delete` ([0a0584a](https://github.com/devuri/rd-web-app-release-deployer-action/commit/0a0584ac35c4925507a52ddafdd16e7056b7f3cc))

## [0.1.3](https://github.com/devuri/rd-web-app-release-deployer-action/compare/rd-web-app-release-deployer-action-v0.1.2...rd-web-app-release-deployer-action-v0.1.3) (2024-07-12)


### Bug Fixes

* add `shell: bash` ([67f5c02](https://github.com/devuri/rd-web-app-release-deployer-action/commit/67f5c02c42d766b43e5c4351058bcbe0d66489ed))
* bash 80 - 83 ([d9c199a](https://github.com/devuri/rd-web-app-release-deployer-action/commit/d9c199a34f4a0d76cb045c1426f200af1540e4e2))
* bash indent ([fde47a5](https://github.com/devuri/rd-web-app-release-deployer-action/commit/fde47a5b0279d97201c531886d2cda512621d9e9))
* update readme ([3f627b2](https://github.com/devuri/rd-web-app-release-deployer-action/commit/3f627b29ded49a0a6269385e04a728367be831e1))

## [0.1.2](https://github.com/devuri/rd-web-app-release-deployer-action/compare/rd-web-app-release-deployer-action-v0.1.1...rd-web-app-release-deployer-action-v0.1.2) (2024-07-11)


### Bug Fixes

* add name ([2425bf2](https://github.com/devuri/rd-web-app-release-deployer-action/commit/2425bf2d2552573e9f72d5b7fd2e89938433319c))
* rel type `node` ([d76d117](https://github.com/devuri/rd-web-app-release-deployer-action/commit/d76d117acb7e1ba5c4be8e2aed34487f1823b3aa))
* release-type": "javascript" ([c26e18b](https://github.com/devuri/rd-web-app-release-deployer-action/commit/c26e18b6f5fb9e645d780135328bd189d99b3d32))
