# coding: utf-8
USE_EXTERNAL_DISK=ENV['USE_EXTERNAL_DISK'] || false

Vagrant.configure("2") do |config|
  config.vm.box = "bento/centos-7"
  config.vm.network "forwarded_port", guest: 8443, host: 8443
  config.vm.network "public_network", type: "dhcp"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"
    vb.cpus = 2

    if USE_EXTERNAL_DISK
      #
      # /opt/netapp 用にディスクを一つ追加する
      # VirtualBoxによっては --storagectl の Controllerが異なる可能性があります
      # 必ず初期化する実装になっていることと
      # 同じファイル名で仮想ディスクを複数持つ事がVirtualBoxで出来ないため
      # 2つ目のインスタンスをあげようとしたり同じ名前の仮想ディスクがあるときに注意
      #
      vb.customize [
       'createmedium', 'disk',
       '--filename', "slm_opt_netapp.vdi",
       '--format', 'VDI',
       '--size', 50 * 1024]
      vb.customize [
        'storageattach', :id,
        '--storagectl', 'SATA Controller',
        '--port', 1,
        '--device', 0,
        '--type', 'hdd',
        '--medium', "slm_opt_netapp.vdi"]
    end
  end

  if USE_EXTERNAL_DISK
    #
    # /opt/netapp フォルダを作成して /dev/sdbをxfsで初期化する
    #
    config.vm.provision "shell", inline: <<-SHELL
      mkdir /opt/netapp
      mkfs.xfs /dev/sdb
      mount -t xfs /dev/sdb /opt/netapp
    SHELL
  end

  #
  # install ansible.
  #
  config.vm.provision "shell", inline: <<-SHELL
    #
    # enable EPEL
    #
    yum -y install epel-release

    #
    # install ansible
    #
    yum -y install ansible
  SHELL

  #
  # install netapp service level manager 1.0RC3
  #
  config.vm.provision "ansible_local" do |ansible|
    ansible.playbook = "site.yml"
    ansible.inventory_path = "localhost"
    ansible.limit = "all"
  end
end
