
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

<a name="ticket-18"></a>
<h1>Ticket #18 — Create Shared Scripts Directory Using Ansible</h1>

<h2>Languages and Utilities Used</h2>
- <b>Bash</b>
- <b>Ansible</b>
- <b>YAML</b>
- <b>vi</b>

<h2>Environments Used</h2>
- <b>CentOS Stream 9</b>
- <b>VMware vSphere (VM console)</b>
<br />
<h2>Project walk-through:</h2>

**Task:** The programmers on the webmasters team want a shared directory for collaboration on scripting knowledge for their division on all development servers. Use Ansible to create it.

**Objective:** Practice infrastructure automation and orchestration using Ansible playbooks.

**Requirements:**
- Successful creation of the directory using Ansible: dev-ansible to dev-app and dev-performance
- Directory path: /opt/scripts/{yourusername}/
- User: {yourusername}
- Group: webmasters
- Permission: 775

<br />
<br />
<br />
<p align="center">
<b>Creating the playbook for the shared scripts directory:</b>
<br/>
<img width="751" height="513" alt="image" src="https://github.com/user-attachments/assets/b6cfd53a-c08d-44b5-8363-3b354a7058f8" />



</p>

<br />
<br />
<br />

<p align="center">
<b>First run: directory created on both servers:</b>
<br/>
<img width="975" height="458" alt="image" src="https://github.com/user-attachments/assets/96a05955-8fcd-42e4-8634-6f73b5de8239" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Second run: no changes needed:</b>
<br/>
<img width="975" height="502" alt="image" src="https://github.com/user-attachments/assets/7212e49a-7185-4bf7-9381-869ed66d510a" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Verifying the directory owner, group and permissions:</b>
<br/>
<img width="975" height="282" alt="image" src="https://github.com/user-attachments/assets/c483a600-7845-495f-b78b-c7aa362a2783" />



</p>

<br />

<b><u>Solution:</u></b>  I wrote an Ansible playbook, rexq-create-dir.yml, that targets my dev-rexq group and uses the ansible.builtin.file module to create /opt/scripts/rquaye with state directory, owner rquaye, group webmasters and mode 0775. My first run failed because I referenced the wrong location for the saved playbook file, and after correcting the path the playbook ran successfully against dev-app and dev-performance. The first run reported a change on both servers, and a second run reported no changes, which confirms the directory already matched the desired state. I verified the result with ls -ld, run through Ansible, which showed drwxrwxr-x (775) with owner rquaye and group webmasters on both servers.

<br />
<br />

<p align="right"><a href="#top">⬆ Back to top</a></p>

---


<br />
<br />


<a name="ticket-50"></a>
<h1>Ticket #50 — Create Tasks Using an Ansible Playbook</h1>

<h2>Languages and Utilities Used</h2>
- <b>Bash</b>
- <b>Ansible</b>
- <b>YAML</b>
- <b>yum</b>
- <b>vi</b>

<h2>Environments Used</h2>
- <b>CentOS Stream 9</b>
- <b>VMware vSphere (VM console)</b>
<br />
<h2>Project walk-through:</h2>

**Task:** Create an Ansible playbook that performs the tasks below on dev-app-[initials].procore.prod1.

**Objective:** Practice infrastructure automation and orchestration using Ansible playbooks.

**Requirements:**
- VM: dev-app
- Ansible playbook that:
  - Creates the local user tfleming
  - Expires tfleming's password
  - Installs tmux

<br />
<br />
<br />
<p align="center">
<b>Creating the playbook file:</b>
<br/><img width="937" height="74" alt="image" src="https://github.com/user-attachments/assets/3bf01189-7025-46cf-bc8b-8bb58a6bc165" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Playbook contents: create user, expire password, install tmux:</b>
<br/>
<img width="896" height="372" alt="image" src="https://github.com/user-attachments/assets/6af3cca8-9aa6-4dd1-9183-da998baa0b7d" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Syntax check passes, first run fails with an unreachable host:</b>
<br/>
<img width="947" height="400" alt="image" src="https://github.com/user-attachments/assets/1d7bd51d-fce5-4ad9-bee5-0a894f720c77" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Run with SSH and sudo prompts: tmux install fails on the mariadb repo:</b>
<br/>
<img width="936" height="475" alt="image" src="https://github.com/user-attachments/assets/908218b9-8b06-4833-bb3c-d63435a5a923" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Updating the playbook to skip the failing mariadb repo:</b>
<br/><img width="884" height="778" alt="image" src="https://github.com/user-attachments/assets/c5b1fcba-c0c9-4a47-8fb2-200c63a57dfd" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Successful playbook run:</b>
<br/><img width="954" height="525" alt="image" src="https://github.com/user-attachments/assets/e5a7d09c-56cd-4cc5-a252-2d954af0b19f" />



</p>

<br />

<b><u>Solution:</u></b>  I wrote an Ansible playbook, ticket50.yml, with three tasks for dev-app: create the local user tfleming with the user module, expire that user's password with chage -d 0, and install tmux with yum. The syntax check passed, but the first run failed because the host was unreachable and SSH authentication was denied. I re-ran the playbook with --ask-pass and --ask-become-pass so Ansible prompted for the SSH and sudo passwords. The user and password-expiry tasks then worked, but the tmux install failed because yum could not download metadata for the mariadb repository. I added disablerepo: mariadb to the install task so yum skips that repository. The final run completed with ok=4, changed=1, failed=0 and unreachable=0.

<br />
<br />

<p align="right"><a href="#top">⬆ Back to top</a></p>

---


<br />
<br />

<a name="ticket-51"></a>
<h1>Ticket #51 — Close Ports Using an Ansible Playbook</h1>

<h2>Languages and Utilities Used</h2>
- <b>Bash</b>
- <b>Ansible</b>
- <b>YAML</b>
- <b>firewalld</b>
- <b>systemctl</b>

<h2>Environments Used</h2>
- <b>CentOS Stream 9</b>
- <b>VMware vSphere (VM console)</b>
<br />
<h2>Project walk-through:</h2>

**Task:** Create an Ansible playbook to close ports 80 and 443 on dev-app-[initials].procore.prod1.

**Objective:** Practice infrastructure automation and orchestration using Ansible playbooks.

**Requirements:**
- VM: dev-app
- Ansible playbook that closes ports 80 and 443

<br />
<br />
<br />
<p align="center">
<b>Creating the playbook that closes ports 80 and 443:</b>
<br/><img width="754" height="544" alt="image" src="https://github.com/user-attachments/assets/08b669d7-2d52-4385-8077-ae752a36e989" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Playbook contents:</b>
<br/><img width="833" height="396" alt="image" src="https://github.com/user-attachments/assets/8ce4d761-e336-47d6-93e4-13516fd6349f" />



</p>

<br />
<br />
<br />

<p align="center">
<b>First run fails with a firewalld error:</b>
<br/><img width="949" height="864" alt="image" src="https://github.com/user-attachments/assets/cea619c4-3d95-475e-ac86-ca68faffec58" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Restarting firewalld on dev-app:</b>
<br/><img width="939" height="603" alt="image" src="https://github.com/user-attachments/assets/a11127bb-da23-4975-b59a-4b0b10ed3fa6" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Returning to dev-ansible:</b>
<br/>
<img width="933" height="232" alt="image" src="https://github.com/user-attachments/assets/3540cc6c-7af0-443b-b09c-8dde63e2bccd" />



</p>

<br />
<br />
<br />

<p align="center">
<b>Successful playbook run:</b>
<br/><img width="943" height="461" alt="image" src="https://github.com/user-attachments/assets/99966630-c568-43bf-b110-2d10b6deebb0" />



</p>

<br />

<b><u>Solution:</u></b>  I wrote an Ansible playbook, ticket51.yml, with two tasks that use the firewalld module to close ports 80/tcp and 443/tcp on dev-app, with the change applied both permanently and immediately. The first run failed on the first task with a D-Bus "no reply" error from firewalld on dev-app. I connected to dev-app and restarted the firewalld service. I also tried to run the playbook from dev-app by mistake, but Ansible is installed on the dev-ansible control server, not on dev-app, so I went back to dev-ansible and re-ran it there. This time it completed with ok=3, changed=1, failed=0 and unreachable=0. Port 80 was closed by the playbook, and port 443 required no change.

<br />
<br />

<p align="right"><a href="#top">⬆ Back to top</a></p>

<br />
<br />


