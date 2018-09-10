---
title: "Improved AppPasswords in Nextcloud 14"
date: "2018-09-05"
tags:
  - Nextcloud
  - AppPasswords
  - Encryption
---

The *app passwords* have been available in Nextcloud for some time now. They were
first introduced when we added second factor authentication to Nextcloud, as
you still want to have a way to connect your mobile and desktop clients to
your account. In the early days this was all manual labor.

In the last year we have added support for *app passwords* to our mobile clients
and the desktop client is following soon. This means that you authenticate
when you setup your account using a normal login (with second factor
authentication if required). A long random (72 character) *app password* is
generated for you.

*App passwords* have several advantages. For example your real password is never
stored on your devices. Also you can revoke access to a specific device in
your security settings of your Nextcloud, blocking your lost phone from
accessing your data. All pretty sweet.

## AppPasswords original design

Now on to explaining how *app passwords* used to work.

Nextcloud never stores your password in plain text. And this means we
also never store your *app password* in plain text. Roughly what we stored are
three things:

1. your username
2. a hash of your *app password*
3. your password symmetrically encrypted with the *app password*

This means that if you authenticate with your `(userName, appPassword)` we do the
following:

1. `appPasswordHash = hash(appPassword)`
2. Lookup `(userName, appPasswordHash)` in the table
3. `password = decrypt_symmetric(encryptedPassword, appPassword)`
4. validate your password against your user back-end

## Improvements

The main draw back of our first implementation is that when a user changed their
password this means that all their *app passwords* are invalid. This is caused by
the password being encrypted by the *app password* and only the hash being stored in
the database.

So to overcome this issue we needed to find a solution to update the encrypted
password without knowing the actual *app passwords*. The answer to this is public key
cryptography.

This means a few changes as a first step we generate a RSA key pair. then
we store the following:

1. your username
2. a hash of your *app password*
3. your private key symmetrically encrypted with your *app password*
4. your public key
5. your password encrypted with your public key

Now the flow when your login with `(userName, appPassword)` changes slightly.

1. `appPasswordHash = hash(appPassword)`
2. Lookup `(userName, appPasswordHash)` in the table
3. `privateKey = decrypt_symmetric(encryptedPrivateKey, appPassword)`
4. `password = decrypt(EncryptedPassword, privateKey)`
5. validate your password against your user back-end

Now to update your password we simply fetch all the *app passwords* for a user. And
for each *app password* encrypt the new password with their public key.

> Note: we don't actually get the *app passwords* form the database just the
> rows with hashed *app passwords*. However, for this step we don't rely on the
> actual *app password*.

### Migration

To make things as smooth as possible we made sure that migration to the new
*app passwords* is transparent to the user. Your old *app passwords* will not be
invalidated. Instead the first time you use them they will automatically be
converted. So from a user perspective nothing changes.

## Future work

We feel that this improvement brings *app passwords* to a new level. Not having
to re-authenticate your devices when you change your password makes the user
experience a lot better.

Of course there is room for improvement. Currently the *app passwords* are only
updated when you use our default user back-end. If you use for example LDAP then Nextcloud never gets told the new password. We are already working on improving
this in Nextcloud 15! So stay tuned!
