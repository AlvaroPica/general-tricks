# Git tricks

Table of contents:

- [Create key(s)](https://github.com/AlvaroPica/general-tricks/blob/main/general-tricks/git.md#create-keys)
- [Check remote url](https://github.com/AlvaroPica/general-tricks/blob/main/general-tricks/git.md#check-remote-url)
- [Check Agent is working properly](https://github.com/AlvaroPica/general-tricks/blob/main/general-tricks/git.md#check-agent-is-working-properly)
- [Combine Two Git Profiles on One Computer](https://github.com/AlvaroPica/general-tricks/blob/main/general-tricks/git.md#combine-two-git-profiles-on-one-computer)


Create key(s). Imagine you want two profiles:

```bash
ssh-keygen -t ed25519 -C "apicatoste@gmail.com" -f ~/.ssh/id_ed25519
```

Pass content of the key to the clipboard:

```bash
pbcopy < ~/.ssh/id_ed25519.pub
```

- Check remote url:

```bash
git remote -v
git remote get-url origin  
```

## Check Agent is working properly

Check agent and add SSH key to agent:

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_github_ed25519
```

In PowerShell:

```bash
Start-Service ssh-agent
ssh-add $env:USERPROFILE\.ssh\id_github_ed25519
```

If it does not work PowerShell might be using the wrong SSH client. To ensure Git uses the correct one, run:
`git config --global core.sshCommand "C:/Windows/System32/OpenSSH/ssh.exe"`

## 🔀 Combine Two Git Profiles on One Computer

Use different Git identities (personal & work) with separate SSH keys and configs.

### 🛠️ Setup Steps

- **Generate SSH keys** (one per profile):

  ```bash
  ssh-keygen -t ed25519 -C "apicatoste@gmail.com" -f ~/.ssh/id_ed25519_personal
  ssh-keygen -t ed25519 -C "alvaro.picatoste@work.com" -f ~/.ssh/id_ed25519_work

- **Add public keys to GitHub**:
	•	`~/.ssh/id_ed25519_personal.pub` → personal account
	•	`~/.ssh/id_ed25519_work.pub` → work account

- **Configure ~/.ssh/config:**:

```bash
    # Personal profile
    Host github.com-personal
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_personal
    IdentitiesOnly yes

    # Work profile
    Host github.com-work
    HostName github.com
    User git
    IdentityFile ~/.ssh/id_ed25519_work
    IdentitiesOnly yes
```

- **Set Git remote URL to use the correct identity**:

    - **Example: Set Git remote for work repo**

  ```bash
  git remote set-url origin git@github.com-work:alvaro-picatoste-work/repo-name.git
  ```

  - **Example: Set Git repo for personal url**

  ```bash
  git remote set-url origin git@github.com-personal:AlvaroPica/repo-name.git
  ```

- **Example clone a personal repo**  

    ```bash
  git clone git@github.com-personal:AlvaroPica/repo-name.git
  ```

-- **Verify you can login to Github**:
`ssh -T git@github.com-personal`  # should say Hi AlvaroPica!

Now each Git repo can use the right identity just by setting the remote host to the correct alias. By default git uses ` git@github.com` and assigns the first identify it can find or the first file it tries (e.g. id_ed25519)

## Merge a branch and put your commit after all changes

```bash
git merge --no-ff <branch-name>
```