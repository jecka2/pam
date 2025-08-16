### Задание

Vagrant-стенд c PAM
Цель домашнего задания
Научиться создавать пользователей и добавлять им ограничения
Описание домашнего задания
Ограничить доступ к системе для всех пользователей, кроме группы администраторов, в выходные дни (суббота и воскресенье), за исключением праздничных дней.


#### Решение

C помошью ansible и vagrant  подготавливаем стэнд для работы.
На стэнде будет выполнены следующие вещи:
1) Развертывание с помощью Vagrant экземпляра виртальной машынис использованием VirtualBox  в качестве  провайдера
2) Настройка (provisoning)  создаваемого экземпляра с помощью ansible playbook
3) В плейбуке мы:
  a) Проверим создана ли группа admin ( если нет то создадим)
  b) Настроим sshd  на работус паролями
  с) Перенесем наш скрипт по проверке условий для авторизации
  d) Настроим pam.d/sshd для использования ранее переданного (скопированного) скрипта для авторизации
  e) Создадим пользователя Otusadmin  и добави его в группу admin

  Далее выполним создание и запускстэнда используя vagrant up

  Работа скрипта
  <details><summary><code>Код выполнения</code></summary>
```bash 
  jecka   ~/Documents/git/pam  vagrant up                 
Bringing machine 'pam' up with 'virtualbox' provider...
==> pam: Importing base box 'ubuntu/jammy64'...
==> pam: Matching MAC address for NAT networking...
==> pam: Checking if box 'ubuntu/jammy64' version '20241002.0.0' is up to date...
==> pam: Setting the name of the VM: ubuntu
==> pam: Clearing any previously set network interfaces...
==> pam: Preparing network interfaces based on configuration...
    pam: Adapter 1: nat
    pam: Adapter 2: hostonly
==> pam: Forwarding ports...
    pam: 22 (guest) => 2222 (host) (adapter 1)
==> pam: Running 'pre-boot' VM customizations...
==> pam: Booting VM...
==> pam: Waiting for machine to boot. This may take a few minutes...
    pam: SSH address: 127.0.0.1:2222
    pam: SSH username: vagrant
    pam: SSH auth method: private key
    pam: Warning: Connection reset. Retrying...
    pam: Warning: Remote connection disconnect. Retrying...
    pam: 
    pam: Vagrant insecure key detected. Vagrant will automatically replace
    pam: this with a newly generated keypair for better security.
    pam: 
    pam: Inserting generated public key within guest...
    pam: Removing insecure key from the guest if it's present...
    pam: Key inserted! Disconnecting and reconnecting using new SSH key...
==> pam: Machine booted and ready!
==> pam: Checking for guest additions in VM...
    pam: The guest additions on this VM do not match the installed version of
    pam: VirtualBox! In most cases this is fine, but in rare cases it can
    pam: prevent things such as shared folders from working properly. If you see
    pam: shared folder errors, please make sure the guest additions within the
    pam: virtual machine match the version of VirtualBox you have installed on
    pam: your host and reload your VM.
    pam: 
    pam: Guest Additions Version: 6.0.0 r127566
    pam: VirtualBox Version: 7.1
==> pam: Setting hostname...
==> pam: Configuring and enabling network interfaces...
==> pam: Running provisioner: ansible...
    pam: Running ansible-playbook...

PLAY [Update pam parametrs] ****************************************************

TASK [Gathering Facts] *********************************************************
[WARNING]: Platform linux on host pam is using the discovered Python
interpreter at /usr/bin/python3.10, but future installation of another Python
interpreter could change the meaning of that path. See
https://docs.ansible.com/ansible-
core/2.18/reference_appendices/interpreter_discovery.html for more information.
ok: [pam]

TASK [Create a new system group] ***********************************************
ok: [pam]

TASK [Copy file with owner and permissions] ************************************
changed: [pam]

TASK [ssh enable use password] *************************************************
changed: [pam]

TASK [Remove file (delete file)] ***********************************************
changed: [pam]

TASK [Restart sshd service] ****************************************************
changed: [pam]

TASK [insert pam_wheel requirement because it doesnt exist] ********************
changed: [pam]

TASK [Ensure the 'admin' group exists] *****************************************
ok: [pam]

TASK [Add the user 'otusadmin' with a bash shell, appending the group 'admin' group] ***
changed: [pam]

PLAY RECAP *********************************************************************
pam                        : ok=9    changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  

```
</ul></details>

Попробуем  в выходной день зайти польхователм otusadmin которая входт в группу admin  и увидим, что пользователь успешно авторизуется
![Вход пользователя кторый входит в  admin](https://raw.githubusercontent.com/jecka2/pam/refs/heads/main/otusadmin.png)

Попробуем  в выходной день зайти польхователм vagrant которая не входт в группу admin  и увидим, что пользовательне может авторизоваться
![Вход пользователя котрые не входит в  admin](https://raw.githubusercontent.com/jecka2/pam/refs/heads/main/vagrant.png))

Проверим логи и увидим, что нас не пускает скрипт
![запускаем у сотрудника](https://raw.githubusercontent.com/jecka2/pam/refs/heads/main/vagrant_log.png))