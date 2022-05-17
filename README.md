### Prepare to launch terraform + ansible
1) You need install yc and create key.json
   - [manual](https://cloud.yandex.com/en/docs/cli/operations/install-cli) cli 
   - [manual](https://cloud.yandex.com/en/docs/cli/operations/authentication/user) set TOKEN and create service account
   - [manual](https://cloud.yandex.com/en/docs/iam/quickstart-sa) create key.json and put in terraform folder
2) Set your variable in terraform/main.tf
   ```terraform
   provider "yandex" {
   service_account_key_file = "key.json"
   zone      = "ru-central1-a"
   folder_id = "b1g7evdi2gkc7jqre2af"
   }
   ```
3) If you need change distrib version, set that variables in playbook/group_vars
    ```yaml
    vector_version: "0.21.1"
    clickhouse_version: "22.3.3.44"
    ```
4) Use tag vector if you need only install [vector,clickhouse,lighthouse] in terraform/ansible.tf
   ```bash
   $ ansible-playbook --tags [vector,clickhouse,lighthouse] -i ../playbook/inventory/prod.yml ../playbook/site.yml
   ```
5) Go to terraform folder, init terrafrom, create workspace, plan and apply
    ```bash
    $ cd terraform
    $ terraform init
    $ terraform workspace new prod
    $ terraform plan
    $ terraform apply
    ```
### Terraform actions
- Terraform create network and 3 VM centos7 [vector clickhouse lighthouse] name - (locals.tf) , config - (main.tf)
- Terrafrom create inventory file in playbook/inventory/prod.yml
- Terraform create vector config in playbook/group_vars/vector/vector.toml
- and then play ansible playbook

### Ansible actions
1) First tasks "Install Clickhouse"
   1) Download clickhouse_packages:
      - clickhouse-client
      - clickhouse-server
      - clickhouse-common-static
   2) Install clickhouse packages
   3) Send config clickhouse in VM
   4) Start clickhouse service
   5) Create database 'logs'
   6) Create table 'access_logs'
2) Second tasks "Install Vector"
   1) Download vector distrib
   2) Create directory for Vector
   3) Install unzip and tar unarchive
   4) Extract vector
   5) Create a symbolic link
   6) Handlers Check is all done
   7) Send config vector in VM
   8) Start vector config file
   9) Send test data in table 'access_logs' [My Test Message]
3) Install NGINX in lighthouse node
4) Install Lighthouse
   1) Install git
   2) Recreate standard folder nginx
   3) Checkout rep lighthouse
   4) Restart nginx