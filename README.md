# nslm-demo

NetApp Service Level Manager deployment with Vagrant / Ansible for demonstration.

1. download NetApp-Service-Level-Manager-1.0RC4.bin from mysupport.netapp.com.
2. copy downloaded file to the same folder as Vagrantfile.
3. vagrant up

- Swagger URL - `https://localhost:8443/`
- Admin UI URL - `https://localhost:8443/admin/`  
  default admin password is in defaults/main.yml

Notes: Cannot invoke APIs from swagger because of access IP address.
