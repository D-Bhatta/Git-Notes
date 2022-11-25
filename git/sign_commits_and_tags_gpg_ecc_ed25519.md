# Git: Sign your commits and tags with GPG: Using ECC Ed25519 keys

## Generate GPG key

- Generate a well secured GPG key with ECC Ed25519 that can be used for signing.
- This key can be a sub key of another key, or a whole new key. A subkey used only for signing is preferred.

## Configure git to use a different GPG executable

- To see what GPG executable is configured for git, use `git config --global gpg.program`. Remove `--global` if you only want to see the settings for a single repository.
- To configure git to use a specific GPG executable, use `git config --global gpg.program "path/to/executable.exe"`. Remove `--global` if you only want to see the settings for a single repository.
- For Windows 10 and Gpg4Win, use `git config --global gpg.program "C:\Program Files (x86)\GnuPG\bin\gpg.exe"`. Remove `--global` if you only want to see the settings for a single repository.

```powershell
~ via  v16.17.1 on ☁️  (us-east-1) took 1m5s
❯ git config --global gpg.program

~ via  v16.17.1 on ☁️  (us-east-1)
❯ git config --global gpg.program "C:\Program Files (x86)\GnuPG\bin\gpg.exe"

~ via  v16.17.1 on ☁️  (us-east-1)
❯ git config --global gpg.program
C:\Program Files (x86)\GnuPG\bin\gpg.exe

~ via  v16.17.1 on ☁️  (us-east-1)
❯
```

## List keys

- List all keys with `gpg --list-secret-keys --keyid-format=long`. Copy the id of the key you want to use.

```powershell
~ via  v16.17.1 on ☁️  (us-east-1)
❯ gpg --list-secret-keys --keyid-format=long
C:\Users\user\AppData\Roaming\gnupg\pubring.kbx
---------------------------------------------
sec   rsa1120/DBB37A2E5E4018A1 2022-10-22 [SC] [expires: 2023-01-20]
      E4C2AA21A12C3F29ED6328AADBB37A2E5E4018A1
uid                 [ultimate] D-Bhatta <dbhatta1232@gmail.com>

sec#  ed25519/F9F681A793C2B7F4 2022-10-26 [C] [expires: 2023-01-24]
      90814C92046939F34DC39ABAF9F681A793C2B7F4
uid                 [ultimate] D-Bhatta <dbhatta1232@gmail.com>
ssb   ed25519/9160E6206D545519 2022-10-26 [S] [expires: 2022-11-23]

```

## Enable signing for all commits and tags

- Enable git to sign all commits with `git config --global commit.gpgSign true`. Remove `--global` if you only want to see the settings for a single repository.
- Enable git to sign all tags with `git config --global tag.gpgSign true`. Remove `--global` if you only want to see the settings for a single repository.

```powershell
~ via  v16.17.1 on ☁️  (us-east-1)
❯ git config --global commit.gpgSign true

~ via  v16.17.1 on ☁️  (us-east-1)
❯ git config --global tag.gpgSign true

~ via  v16.17.1 on ☁️  (us-east-1)
❯
```

## Add a signing key in git

- Copy a key id, and set it as the signing key with `git config --global user.signingkey keyid`, where `keyid` should be substituted with the copied key id. Add a `!` suffix to the `keyid` if using a subkey.

```powershell

~ via  v16.17.1 on ☁️  (us-east-1)
❯ git config --global user.signingkey
DBB37A2E5E4018A1

~ via  v16.17.1 on ☁️  (us-east-1)
❯ git config --global user.signingkey 9160E6206D545519!

~ via  v16.17.1 on ☁️  (us-east-1)
❯
```

## Export public key

- Export public key in ASCII armor format with `gpg --armor --export keyid`,  where `keyid` should be substituted with the copied key id. Add a `!` suffix to the `keyid` if using a subkey.

```powershell
~ via  v16.17.1 on ☁️  (us-east-1)
❯ gpg --armor --export 9160E6206D545519!
-----BEGIN PGP PUBLIC KEY BLOCK-----

...

-----END PGP PUBLIC KEY BLOCK-----

~ via  v16.17.1 on ☁️  (us-east-1)
❯
```

## Add public key to GitHub

- [Add a GPG public key to your GitHub account](https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-gpg-key-to-your-github-account) in the `SSH and GPG keys` section in user [settings](https://github.com/settings/keys).

## See commit signatures

- See commit signatures with `git log --show-signature`

```powershell
Git-Notes on  sign-commits-tags-rsa-4096-eeR0oj8 [?] on ☁️  (us-east-1) 
❯ git log --show-signature
commit 3b6cbfed4dcaca828649bf210fd0fdc205a85b52 (HEAD -> sign-commits-tags-rsa-4096-eeR0oj8, origin/main, origin/HEAD, main)
gpg: Signature made 21-Sep-22 10:28:14 Eastern Daylight Time^M
gpg:                using RSA key 4AEE18F83AFDEB23^M
gpg: Cant check signature: No public key^M
Author: Debabrata Bhattacharya <D-Bhatta@users.noreply.github.com>
Date:   Wed Sep 21 10:28:14 2022 -0400

    Initial commit

Git-Notes on  sign-commits-tags-rsa-4096-eeR0oj8 [?] on ☁️  (us-east-1) 
❯
```

- On github the commits will show up as verified if [vigilant mode](https://docs.github.com/en/authentication/managing-commit-signature-verification/displaying-verification-statuses-for-all-of-your-commits) is turned on.
  ![Image of a pull request on GitHub. There is a pill component beside a commit that says verified. It is called a verified badge. Clicking on the pill component opens a modal and displays committer's username, their full name, and their GPG key ID, as well as a link to know more about vigilant mode. These are vertically arranged.](../static/img/git-hub-signed-commit-signature-verified.png "Verified badge on commit")
