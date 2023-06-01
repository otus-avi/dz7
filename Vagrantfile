# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :"dz7" => {
        :box_name => "generic/centos8"
      }
}

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

      config.vm.define boxname do |box|

          box.vm.box = boxconfig[:box_name]
          box.vm.host_name = boxname.to_s
    
	  box.vm.provision :shell, :inline => "setenforce 0", run: "always"
          box.vm.provision "shell", inline: <<-SHELL
                        sudo su
                        cd
                        yum install -y redhat-lsb-core wget rpmdevtools rpm-build createrepo yum-utils gcc
                        wget https://nginx.org/packages/centos/8/SRPMS/nginx-1.24.0-1.el8.ngx.src.rpm
                        rpm -i nginx-1.*
                        wget https://www.openssl.org/source/openssl-1.1.1u.tar.gz
                        tar -xvf openssl-1.1.1u.tar.gz
                        yum-builddep -y rpmbuild/SPECS/nginx.spec
                        sed -i 's/--with-debug/--with-openssl=\/root\/rpmbuild\/openssl-1.1.1c/g' /root/rpmbuild/SPECS/nginx.spec
                        rpmbuild -bb rpmbuild/SPECS/nginx.spec
                        yum localinstall -y rpmbuild/RPMS/x86_64/nginx-1.24.0-1.el8.ngx.x86_64.rpm
                        systemctl start nginx
                        mkdir /usr/share/nginx/html/repo
                        cp rpmbuild/RPMS/x86_64/nginx-1.24.0-1.el8.ngx.x86_64.rpm /usr/share/nginx/html/repo/
                        wget https://downloads.percona.com/downloads/percona-distribution-mysql-ps/percona-distribution-mysql-ps-8.0.28/binary/redhat/8/x86_64/percona-orchestrator-3.2.6-2.el8.x86_64.rpm -O /usr/share/nginx/html/repo/percona-orchestrator-3.2.6-2.el8.x86_64.rpm
                        createrepo /usr/share/nginx/html/repo/
                        sed -i '/index  index.html index.htm;/s/$/ \n\tautoindex on;/' /etc/nginx/conf.d/default.conf
                        nginx -s reload
                        cat >> /etc/yum.repos.d/otus.repo << EOF
[otus]
name=otus-linux
baseurl=http://localhost/repo
gpgcheck=0
enabled=1
EOF
                        
                        
          SHELL

      end
  end
end
