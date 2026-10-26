
<a name="top"></a>
<h1>Ansible Patch Automation</h1>

### 📋 Tickets Covered in This Repository

- 🖥️ [**Ticket #16** — Add Dev Servers into Ansible Inventory](#ticket-16)
- 🔄 [**Ticket #17** — Patch Your Dev Servers Using Ansible](#ticket-17)
- 📁 [**Ticket #18** — Create Shared Scripts Directory Using Ansible](#ticket-18)
- 📝 [**Ticket #50** — Create Tasks Using an Ansible Playbook](#ticket-50)
- 🔒 [**Ticket #51** — Close Ports Using an Ansible Playbook](#ticket-51)


<br />
<br />

---

<br />
<br />

<a name="ticket-16"></a>
<h1>Ticket #16 — Add Dev Servers into Ansible Inventory</h1>

<h2>Languages and Utilities Used</h2>
- <b>Bash</b>
- <b>Ansible</b>
- <b>SSH</b>
- <b>vi</b>

<h2>Environments Used</h2>
- <b>CentOS Stream 9</b>
- <b>VMware vSphere (VM console)</b>
<br />
<h2>Project walk-through:</h2>

**Task:** Add the dev-app and dev-performance servers to the Ansible inventory file on dev-ansible, create a custom group, and test connectivity.

**Objective:** Practice infrastructure automation and orchestration using Ansible.

**Requirements:**
- Add the servers to /etc/hosts on dev-ansible
- Create an Ansible group named dev-[initials]
- Add both servers to the group
- Test with the Ansible ping module

<br />
<br />
<br />
<p align="center">
<b>Adding the dev servers to the dev-ansible local hosts file:</b>
<img width="865" height="395" alt="image" src="https://github.com/user-attachments/assets/6bfd5701-a38a-41ca-8199-ca2b32fe2c3d" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Creating the dev-rexq group in the Ansible inventory:</b>
<br/>
<img width="596" height="300" alt="image" src="https://github.com/user-attachments/assets/8ac730fb-1809-48b9-bc1e-ea3aee31a13d" />


</p>

<br />
<br />
<br />

<p align="center">
<b>Successful Ansible ping test:</b>
<br/>
<img width="797" height="379" alt="image" src="https://github.com/user-attachments/assets/871fcf4d-8ea4-4a9b-8339-a3482f2c1da6" />


</p>

<br />

<b><u>Solution:</u></b>  I added the dev-app and dev-performance servers to the local /etc/hosts file on the dev-ansible server, then created a custom group, dev-rexq, in the Ansible inventory file (/etc/ansible/hosts) and added both servers to it. My first test failed for dev-performance because the hostname I had entered in both files was missing the "2" that is in the VM's actual hostname (dev-performance-rexq2). After correcting the hostname in /etc/hosts and in the inventory, an Ansible ping against the group returned SUCCESS and pong for both servers. Ansible also printed a warning about characters in the group name, which did not affect the test.

<br />
<br />

<p align="right"><a href="#top">⬆ Back to top</a></p>

---


<br />
<br />

<a name="ticket-17"></a>
<h1>Ticket #17 — Patch Your Dev Servers Using Ansible</h1>

<h2>Languages and Utilities Used</h2>
- <b>Bash</b>
- <b>Ansible</b>
- <b>YAML</b>
- <b>dnf</b>
- <b>rpm</b>
- <b>vi</b>

<h2>Environments Used</h2>
- <b>CentOS Stream 9</b>
- <b>VMware vSphere (VM console)</b>
<br />
<h2>Project walk-through:</h2>

**Task:** In accordance with company policy, all development servers must be patched regularly. Patch the development servers using Ansible, following the instructions provided under Infrastructure Resources.

**Objective:** Practice infrastructure automation and orchestration using Ansible playbooks.

**Requirements:**
- Successful dev server patching using Ansible: dev-ansible to dev-app and dev-performance

**Information:**
- Ensure the python3 package is installed on the dev servers
- Use the sample patching template at /opt/ansible/patching/dev-patch.yml as a guide for creating your own playbook
- Make sure the servers are patched successfully

<br />
<br />
<br />
<p align="center">
<b>Confirming python3 is installed on the dev servers:</b>
<br/>
<img width="1050" height="403" alt="image" src="https://github.com/user-attachments/assets/10ca1010-d427-4a75-9f6a-f183b15648ea" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Reviewing the sample patching template:</b>
<br/><img width="824" height="446" alt="image" src="https://github.com/user-attachments/assets/8c13ef85-2acd-4dfe-98e0-2651c590f914" />


</p>

<br />
<br />
<br />

<p align="center">
<b>Creating the patching playbook:</b>
<br/><img width="715" height="507" alt="image" src="https://github.com/user-attachments/assets/6f5ef964-c2d0-465f-a65c-f024d175ec67" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Playbook contents:</b>
<br/><img width="800" height="402" alt="image" src="https://github.com/user-attachments/assets/1b0a6efb-6ade-4875-af7e-3165816463ed" />



</p>

<br />
<br />
<br />

<p align="center">
<b>First run: RPM error on dev-app:</b>
<br/><img width="988" height="729" alt="image" src="https://github.com/user-attachments/assets/06a77b0a-d870-46b9-8087-7c2882fc081e" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Successful patching run:</b>
<br/>
<img width="969" height="598" alt="image" src="https://github.com/user-attachments/assets/50004110-fbee-4352-9bac-60c21628e665" />


</p>

<br />

<b><u>Solution:</u></b>  I first confirmed with the Ansible raw module that python3 is installed on both dev servers (Python 3.9 on each). I then reviewed the sample template at /opt/ansible/patching/dev-patch.yml and wrote my own playbook, rexq-dev-patch.yml, that targets my dev-rexq group, uses privilege escalation, and updates all packages to the latest version with the ansible.builtin.dnf module. I ran it with ansible-playbook and --ask-become-pass. The first runs exposed two problems. dev-performance hit an SSL certificate error, which I fixed by reinstalling ca-certificates and running update-ca-trust. dev-app failed with an RPM exception ("package not installed"), which I fixed by rebuilding the RPM database with rpm --rebuilddb and running dnf update. After those fixes, the playbook ran cleanly against both servers with ok=2, failed=0 and unreachable=0 on each.

<br />
<br />

<p align="right"><a href="#top">⬆ Back to top</a></p>

---

<br />
<br />




