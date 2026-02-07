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

Check to see if the folder exists, and store the result as a variable called "folder_exists_status"

```yaml
- name: Check if a folder exists
  stat:
    path: "/path/to/folder"
  register: folder_exists_status
```
      
To conditionally do something when the folder does not exist

```yaml
- name: Copy folder if destination does not exist
  when: not folder_exists_status.stat.exists
  become: yes 
  copy:
    src: "/path/to/folder"
    dest: "/path/to/destination"
    mode: '0755'
```

### Example: Loop through files / folders

- Check each file or folder and store the results in parent_folder_stats

- Each item's result is named to subfolder_path

- "{{ subfolders }}" is a var that looks like this:

```yaml
vars:
    subfolders:
        - name1
        - name2
```

Checking each subfolder with a loop:

```yaml
- name: Check existence of all subfolders listed above
  stat:
    path: "{{ subfolder_path }}"
  loop: "{{ subfolders }}"
  loop_control:
    loop_var: subfolder_path
  register: parent_folder_statuses
```

Looping and consuming

```yaml
- name: Example: Symlinking
  when: not subfolder_status.stat.exists
  become: no
  file:
    src: "{{ parent_folder_path }}/{{ subfolder_status.subfolder_path | basename }}"
    dest: "path/to/destination/{{ subfolder_status.subfolder_path | basename }}"
    state: link
  loop: "{{ parent_folder_statuses.results }}"
  loop_control:
    loop_var: subfolder_status
    label: "{{subfolder_status.subfolder_path }}" # Cleans up terminal output
```
