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

### Example: Single File or Folder Exists

Definition

```yaml
- name: Check if dotfiles directory exists
    stat:
      path: "{{ dotfiles_path }}"
    register: repo_status
```
      
Consuming

```yaml
- name: Clone dotfiles repository
    become: no
    git:
      repo: "{{ dotfiles_repo }}"
      dest: "{{ dotfiles_path }}"
      update: yes
    when: not repo_status.stat.exists
```

### Example: Loop through files / folders

Definition

```yaml
- name: Check existence of all dotfiles_folders listed above
  stat:
    path: "{{ item }}"
  loop: "{{ dotfiles_folders }}"
  register: dotfiles_folder_stats
```

Consuming

```yaml
- name: Symlink configuration directories to ~/.config from dotfiles
  become: no
  file:
    src: "{{ dotfiles_path }}/.config/{{ item.item | basename }}"
    dest: "~/.config/{{ item.item | basename }}"
    state: link
  loop: "{{ dotfiles_folder_stats.results }}"
  when: not item.stat.exists
```
