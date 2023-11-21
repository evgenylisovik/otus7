### Установка apache из исходников
  config.vm.provision "shell", inline: <<-SHELL  
#### Устанавливаем необходимые пакеты
  yum install -y \
  redhat-lsb-core \wget \
  rpmdevtools \
  rpm-build \
  createrepo \
  yum-utils \
  gcc
  yum install -y libuuid-devel openldap-devel autoconf \
  lua-devel \
  libxml2-devel \
  openssl-devel pcre-devel zlib-devel libtool doxygen 
  #### Создаем пользователя для сборки
  useradd builder -m
  #### Качаем исходники Apache Portable Runtime Project и httpd
  runuser -l vagrant -c  'wget https://dlcdn.apache.org/httpd/httpd-2.4.58.tar.bz2 --no-check-certificate'
  runuser -l vagrant -c  'wget https://dlcdn.apache.org//apr/apr-1.7.4.tar.bz2 --no-check-certificate'
  #### Создаем из архива rpm.src для Apache Portable Runtime Project
  runuser -l vagrant -c  'rpmbuild -ts apr-1.7.4.tar.bz2'
  #### Устанавливаем исходники
  runuser -l vagrant -c  'rpm -i /home/vagrant/rpmbuild/SRPMS/apr-1.7.4-1.src.rpm'
  runuser -l vagrant -c  'rpmbuild -bb /home/vagrant/rpmbuild/SPECS/apr.spec'
  #### Устанавливаем из сформированного пакета 
  yum localinstall -y /home/vagrant/rpmbuild/RPMS/x86_64/apr-1.7.4-1.x86_64.rpm
  yum localinstall -y /home/vagrant/rpmbuild/RPMS/x86_64/apr-devel-1.7.4-1.x86_64.rpm
  yum localinstall -y /home/vagrant/rpmbuild/RPMS/x86_64/apr-debuginfo-1.7.4-1.x86_64.rpm 
  yum install -y apr-util-devel
  #### Удаляем папку для сборки чтобы она не мешала второй установке
  rm -r /home/vagrant/rpmbuild/ --force
  #### То же самое проделываем для httpd
  runuser -l vagrant -c  'rpmbuild -ts httpd-2.4.58.tar.bz2'
  runuser -l vagrant -c  'rpm -i /home/vagrant/rpmbuild/SRPMS/httpd-2.4.58-1.src.rpm'
  runuser -l vagrant -c  'rpmbuild -bb /home/vagrant/rpmbuild/SPECS/httpd.spec'
  yum localinstall -y /home/vagrant/rpmbuild/RPMS/x86_64/httpd-2.4.58-1.x86_64.rpm
  #### Создаем папку под репозиторий
  mkdir /usr/share/httpd
  mkdir /usr/share/httpd/html
  mkdir /usr/share/httpd/html/repo
  mkdir /usr/share/httpd/html/repo/paket
  #### Копируем в нее пакет
  cp rpmbuild/RPMS/x86_64/httpd-2.4.58-1.x86_64.rpm /usr/share/httpd/html/repo/paket
  #### Создаем репозиторий
  createrepo /usr/share/httpd/html/repo
  #### Создаем ссылку 
  ln -s /usr/share/httpd/html/repo /var/www/html
  #### Стартуем httpd
  systemctl start httpd  
  SHELL
end
