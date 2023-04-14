VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/xenial64"

  config.vm.provider :virtualbox do |vb|
    vb.gui = true
    vb.customize [
      'modifyvm', :id,
      '--natdnshostresolver1', 'on',
      '--memory', '512',
      '--cpus', '2'
    ]
  end

  config.vm.define "web1" do |web1|
    web1.vm.network :private_network, ip: "192.168.33.11"

    web1.vm.provision "shell", inline: <<-SHELL
      hostname web1
      echo "127.0.1.1  web1" >> /etc/hosts
      apt-get update
      apt-get install -y apache2
      echo "Hola mundo" > /var/www/html/index.html
    SHELL
  end

  config.vm.define "web2" do |web2|
    web2.vm.network :private_network, ip: "192.168.33.12"

    web2.vm.provider :virtualbox do |vb|
      vb.customize [
        'modifyvm', :id,
        '--natdnshostresolver1', 'on',
        '--memory', '1024',
        '--cpus', '1'
      ]
    end

    web2.vm.provision "shell", inline: <<-SHELL
      hostname web2
      echo "127.0.1.1  web2" >> /etc/hosts
      apt-get update
      apt-get install -y nginx
      echo "Hello world" > /var/www/html/index.html
    SHELL
  end

  config.vm.define "hap" do |hap|
    hap.vm.network :private_network, ip: "192.168.33.10"

    hap.vm.provision "file", source: "haproxy.cfg", destination: "/tmp/haproxy.cfg"

    hap.vm.provision "shell", inline: <<-SHELL
      hostname hap
      echo "127.0.1.1  hap" >> /etc/hosts
      apt-get update
      apt-get install -y haproxy
      cp /tmp/haproxy.cfg /etc/haproxy/haproxy.cfg
      service haproxy restart
    SHELL
  end
end
