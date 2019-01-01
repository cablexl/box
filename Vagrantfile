Vagrant.configure("2") do |config|
  config.vm.box = "centos/7"
  config.vm.network "public_network"
  config.vm.box_check_update = false
  config.vm.provider "hyperv" do |h|
	  h.memory = 4096
	  h.cpus = 4
  end

  config.vm.provision "shell", inline: <<-SHELL
# update centos 7 to latest kernel
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
yum --enablerepo=elrepo-kernel install -y kernel-ml
grub2-set-default 0
grub2-mkconfig -o /boot/grub2/grub.cfg
SHELL


config.vm.provision :reload


config.vm.provision "shell", inline: <<-SHELL
# delete old kernel(s)
yum install -y yum-utils
package-cleanup --oldkernels

# setup docker
yum remove -y docker \
              docker-client \
              docker-client-latest \
              docker-common \
              docker-latest \
              docker-latest-logrotate \
              docker-logrotate \
              docker-selinux \
              docker-engine-selinux \
              docker-engine

yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# BLEEDING EDGE ENABLE: yum-config-manager --enable docker-ce-edge

yum install -y docker-ce
systemctl start docker
usermod -aG docker vagrant

# neovim
yum -y install epel-release
curl -o /etc/yum.repos.d/dperson-neovim-epel-7.repo https://copr.fedorainfracloud.org/coprs/dperson/neovim/repo/epel-7/dperson-neovim-epel-7.repo
yum -y install neovim

# jdk 11
yum -y install java-11-openjdk

# lein
curl -o /usr/bin/lein https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein
chmod a+x /usr/bin/lein

runuser -l vagrant -c '/usr/bin/lein'
yes | lein upgrade
SHELL
end
