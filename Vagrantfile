Vagrant.configure("2") do |config|
  app_name = "yourapp"
  app_dir = "/#{app_name}"
  host_ip = "192.168.56.10"
  host_port = 8080

  config.vm.box = "bento/ubuntu-24.04"
  config.vm.hostname = "#{app_name}.local"
  config.vm.synced_folder ".", app_dir
  config.vm.network "forwarded_port", guest: host_port, host: host_port, auto_correct: true
  config.vm.network "private_network", ip: host_ip

  config.vm.provider "virtualbox" do |vb|
    vb.memory = 4096
    vb.cpus = 2
  end

  config.vm.provision "shell", privileged: true, binary: "/bin/bash", inline: <<-SHELL
    #!/usr/bin/env bash
    set -euo pipefail

    export DEBIAN_FRONTEND=noninteractive

    apt-get update
    apt-get install -y build-essential libssl-dev libffi-dev libpq-dev python3-dev python3-pip python3-venv postgresql postgresql-contrib

    cd #{app_dir}

    if [ ! -d .venv ]; then
      python3 -m venv .venv
    fi

    . .venv/bin/activate
    pip install --upgrade pip
    pip install --upgrade psycopg2-binary coderedcms

    if [ ! -f manage.py ]; then
      mv README.md INSTALL.md 2>/dev/null || true
      coderedcms start #{app_name} ./ --template pro --sitename "#{app_name}" --domain "#{app_name}.local"
    fi

    if [ -f requirements-dev.txt ]; then
      pip install -r requirements-dev.txt
    fi

    # Ensure database exists and credentials are set
    sudo -u postgres psql -tc "SELECT 1 FROM pg_database WHERE datname='#{app_name}'" | grep -q 1 || sudo -u postgres createdb #{app_name}
    sudo -u postgres psql -c "ALTER USER postgres WITH PASSWORD 'admin';"
  SHELL
end
