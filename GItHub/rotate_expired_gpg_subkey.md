# Rotate an expired GPG signing subkey in GitHub

## Sections

- [Rotate an expired GPG signing subkey in GitHub](#rotate-an-expired-gpg-signing-subkey-in-github)
  - [Sections](#sections)
  - [Generate GPG subkey](#generate-gpg-subkey)
  - [List keys](#list-keys)
  - [Update the signing key in git](#update-the-signing-key-in-git)
  - [Export public key](#export-public-key)
  - [Add public key to GitHub](#add-public-key-to-github)

## Generate GPG subkey

- Do **NOT** delete the old subkeys or primary keys.
- Generate a well secured GPG subkey with ECC Ed25519 that can be used for signing.
- This key should be a sub key of another key. A subkey used only for signing is preferred.

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

## Update the signing key in git

- Copy a key id, and set it as the signing key with `git config --global user.signingkey keyid!`, where `keyid` should be substituted with the copied key id. Add a `!` suffix to the `keyid` when using a subkey.
- This will overwrite the old signing subkey for git globally. Remove `--global` if you only want to see the settings for a single repository.

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

- Export public key in ASCII armor format with `gpg --armor --export keyid`,  where `keyid` should be substituted with the copied key id.
- Do **not** use `gpg --armor --export keyid!`, where `keyid` should be substituted with the copied key id of the signing key and a `!` suffix is added to signify a subkey. This will only export the public keys of the primary key and only one subkey. This would be fine under other circumstances, but in this case you want the public keys of the old expired subkeys as well, so leave off the `!`. This will allow GitHub to verify commits signed with older subkeys as well.

```powershell
~ via  v16.17.1 on ☁️  (us-east-1)
❯ gpg --armor --export 9160E6206D545519
-----BEGIN PGP PUBLIC KEY BLOCK-----

...

-----END PGP PUBLIC KEY BLOCK-----

~ via  v16.17.1 on ☁️  (us-east-1)
❯
```

## Add public key to GitHub

- Go to `SSH and GPG keys` section in user [settings](https://github.com/settings/keys "GitHub user SSH and GPG keys settings").
- Copy the title of the old subkey, and delete the old subkey by clicking on the `Delete key` button.
- [Add a GPG public key to your GitHub account](https://docs.github.com/en/authentication/managing-commit-signature-verification/adding-a-gpg-key-to-your-github-account "GitHub documentation: Adding a GPG key to your account"). Paste the ASCII armor formatted text of the newly generated and exported subkey.
- You should see several expired subkeys, as well the newly generated active subkeys now.
- On github the commits will show up as verified if [vigilant mode](https://docs.github.com/en/authentication/managing-commit-signature-verification/displaying-verification-statuses-for-all-of-your-commits "GitHub documentation: Displaying verification statuses for all of your commits") is turned on.
- All previous commits on GitHub will show up as verified, and signed by an expired key. All new commits will show up as verified and signed by an active key.
  ![Image of a pull request on GitHub. There is a pill component in green beside a commit that says verified. It is called a verified badge. Clicking on the pill component opens a modal and displays committer's username, their full name, and their GPG key ID, as well as a link to know more about vigilant mode. There is also information that the key expired after the commit was signed. There is a pill component in blue below that says Expired. These are vertically arranged.](../static/img/git-hub-signed-commit-with-expired-subkey.png "Verified badge on commit signed with expired subkey")
