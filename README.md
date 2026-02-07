# ansible
Ansible Playbook

## Initial Ansible Setup

```bash
sudo pacman -S ansible git
```

```bash
ansible-galaxy collection install kewlfft.aur community.general
```

## Conditional use of folders

### Example

```yaml
- name: Create folders only if they do not exist
  ansible.builtin.file:
    path: "{{ item.item }}" # 'item.item' is the original path from the previous loop
    state: directory
  loop: "{{ folder_stats.results }}"
```

Then in the task:

```yaml
    when: 
     - not item.stat.exists
     - item.item == "path/to/folder/from/required_folders"
```

