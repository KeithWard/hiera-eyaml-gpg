hiera-eyaml-gpg
===============

[![Gem Version](https://img.shields.io/gem/v/hiera-eyaml-gpg.svg)](https://rubygems.org/gems/hiera-eyaml-gpg)
[![Gem Downloads](https://img.shields.io/gem/dt/hiera-eyaml-gpg.svg)](https://rubygems.org/gems/hiera-eyaml-gpg)

GPG encryption backend for the [hiera-eyaml](https://github.com/voxpupuli/hiera-eyaml) module.

Motivation
----------

The default PKCS#7 encryption scheme used by hiera-eyaml is perfect if only simple
encryption and decryption is needed.

However, if you are in a sizable team it helps to encrypt and decrypt data with multiple
keys. This means that each team member can hold their own private key and so can the puppetmaster.
Equally, each puppet master can have their own key if desired and when you need to rotate 
keys for either users or puppet masters, re-encrypting your files and changing the key everywhere
does not need to be done in lockstep.

Requirements
------------

You'll need a working GPG setup with your own keypair and a public keyring containing any other
keys that you want to work

To get started, install the hiera-eyaml-gpg gem.

    $ gem install hiera-eyaml-gpg

You will also need to install either the `gpgme` (recommended) or `ruby_gpg` gem:

    $ gem install gpgme

OR

    $ gem install ruby_gpg -v ">=0.3.1"

Note: you will need to use `ruby_gpg` with the Puppet server as it uses JRuby which cannot
make use of native extensions such as `gpgme`.

If you haven't already installed it, this requires and will install the hiera-eyaml gem, which you
should probably acquaint yourself with at https://github.com/TomPoulton/hiera-eyaml.

Note that in order to install the gpgme gem you'll need to have the ruby development package installed
for your distribution.

How to use
----------

### Encrypting and editing encrypted data

Once installed you can create encrypted hiera-eyaml blocks that are encrypted using GPG.

    $ eyaml encrypt -n gpg -s "A secret string to encrypt" --gpg-recipients bob@example.com,hiera@example.com

If you do not have a web of trust (i.e. you normally use --always-trust for gpg signing) then you'll need 
to use the `--gpg-always-trust` option on the command line.

It gets pretty dull to keep on remembering which recipients you should use, so you can put them in a file
and specify that instead.

    $ eyaml encrypt -n gpg -s "A secret string to encrypt" --gpg-recipients-file hiera-eyaml-gpg.recipients

In fact, when editing a file on disk and neither of the --gpg-recipient options are provided it will
automatically look for a `hiera-eyaml-gpg.recipients` file in the same directory as the file being edited 
(or any parent in the tree). The first file discovered will be used allowing different parts of a hiera 
tree to have different recipients if so desired.

Use `eyaml --help` for more details or look at the hiera-eyaml docs.

### Configuring hiera

Assuming you have a working `hiera` and `hiera-eyaml` then the only option you need to add is to
configure `:gpg_gnupghome:` in your hiera.yaml (under the `:eyaml:` section). This should be the
directory that contains the keyring etc for the user that can to decrypt the hiera data. Please note
that the private GPG key must not have a passphrase.

Authors
-------

Thanks to Simon Hildrew for the inital code. Other contributors can be seen at [https://github.com/voxpupuli/hiera-eyaml-gpg/graphs/contributors](https://github.com/voxpupuli/hiera-eyaml-gpg/graphs/contributors)
