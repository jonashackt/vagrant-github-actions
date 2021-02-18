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
        vb.customize ["modifyvm", :id, "--nictype1", "Am79C973"]
        vb.customize ['modifyvm', :id, '--cableconnected1', 'on']
    end
end