# vagrant-github-actions
[![Build Status](https://github.com/jonashackt/vagrant-github-actions/workflows/vagrant-up/badge.svg)](https://github.com/jonashackt/vagrant-github-actions/actions)

Example project showing how to run a Vagrant box on GitHub Actions


I have some history in this topic on how to run Vagrant on Cloud CI systems for OpenSource projects :) See https://stackoverflow.com/a/60615201/4964553 & https://stackoverflow.com/a/60380518/4964553 - since until somewhere in 2020 the only possible way was TravisCI with libvrt: https://github.com/jonashackt/vagrant-travisci-libvrt

What didn't work was plain VirtualBox on Travis: https://github.com/jonashackt/vagrant-travisci and also other CI platforms also did't work like AppVeyor https://github.com/jonashackt/vagrant-ansible-on-appveyor ([I didn't even try the others because of this](https://stackoverflow.com/questions/31828555/using-vagrant-on-cloud-ci-services)).

__BUT__ then the end of 2020 came and Travis defacto ended broad support for OpenSource (see https://github.com/codecentric/cxf-spring-boot-starter/issues/93) and I started to migrate all my repositories to [GitHub Actions](https://github.com/features/actions). I even blogged about it (tba when released).

And I finally found out, that [there's Vagrant activated (incl. nested virtualization) on GitHub Action MacOS environment](https://github.com/actions/virtual-environments/issues/433) ([not Linux](https://github.com/actions/virtual-environments/issues/183) or Windows currently).

### How to run Vagrant on GitHub Actions

So let's first add a [Vagrantfile](Vagrantfile) like this:

```
Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/focal64"

    config.vm.define 'ubuntu'

    # Vagrant boot needs more time on GitHub Actions (https://github.com/jonashackt/vagrant-github-actions/runs/1926207530?check_suite_focus=true)
    config.vm.boot_timeout = 1800

    # Prevent SharedFoldersEnableSymlinksCreate errors
    config.vm.synced_folder ".", "/vagrant", disabled: true

    config.vm.provider :virtualbox do |vb|
        vb.name = 'ubuntu'
        vb.memory = 128
        vb.cpus = 1
    end
end
```

Now add a MacOS environment powered GitHub Actions `vagrant-up.yml`:

```yaml
name: vagrant-up

on: [push]

jobs:
  build:
    runs-on: macos-10.15

    steps:
      - uses: actions/checkout@v2

      - run: |
          vagrant --version
          vagrant up
          vagrant ssh -c "echo 'hello world!'"

```


And since there's no stable release of VirtualBox for Big Sur (v11), well go with MacOS version 10.15 (which is currently also the `macos-latest` [environment on GitHub Actions](https://github.com/actions/virtual-environments))

