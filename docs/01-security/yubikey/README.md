# Using your YubiKey

Notes on installing and setting up your *YubiKey 4* for various platforms and applications.

## Introduction

The YubiKey is a hardware device manufactured by Yubico that provides a hardware "second factor" enabling true two-factor authentication: something you know (your password) and something you have (your YubiKey). It enables you to easily and securely log in to accounts by emitting one-time passwords or using a FIDO-based public/private key pair generated by the device.

### Operation

* Simply plug it into an unused USB port.
* During certain types of authentication you will be prompted on screen to press the inset copper button marked with and (often lit) "Y".

### Security Hints

* If you trust your environment (like at home) you can keep the YubiKey near or even plugged into your computer.
* In low trust environments (coffee shops, hotel rooms, etc.) keep your YubiKey with you at all times (in a pocket or purse). If your computer is compromised, it won't be accessible without the YubiKey that you have on you.
* Do not use SMS text messages for two-factor authentication.

## Enable YubiKey TFA for applications

*See also: [How to Secure Your Google, Dropbox, and GitHub Accounts With a U2F Key](http://www.howtogeek.com/232360/how-to-secure-your-google-dropbox-and-github-accounts-with-a-u2f-key/)*

### Lastpass

This requires a Yubikey token (cover the button for approximately one second) on laptop/desktop to unlock LastPass.

* My Vault -> Account Settings -> Multifactor Options
  * Set up one free option (e.g., [Google Authenticator](https://support.google.com/accounts/answer/1066447?hl=en)) - this is a useful backup
  * YubiKey (an easier option) is available when using [LastPass Premium](https://lastpass.com/yubico/) ($12/year)
    * Select the YubiKey option.
    * Insert the YubiKey device into a USB port on your computer.
    * Focus your cursor on the "YubiKey #1" field.
    * Press the button on the YubiKey device.
    * A long string of dots should appear in the YubiKey #1 field.
    * Change the "YubiKey Authentication" status to "Enabled"
    * Set "Enabled" ==> "Yes"
    * Set "Permit Offline Access" ==> "Disallow"
    * Press the Update button
    * Enter your LastPass master password and press Confirm.
    * YubiKey is now enabled for your LastPass account.
* If you have a *YubiKey Neo* (Bixal Solutions uses the *YubiKey 4* model) and your phone supports NFC, you can touch the Neo against your phone to unlock on mobile.

### Google

For each Google account you have:

* Visit <https://accounts.google.com/b/0/SmsAuthSettings#devices>
* Enable TFA, and complete the phone verification process (phone will act as backup TFA).
* Click on "Security Keys" and follow instructions to add Yubikey.
* Return to the main page and add a second phone and/or print backup codes.
* As long as you have a backup, you can also install the Yubikey Authenticator app, and configure your account to use that for the backup TFA instead of SMS/phone - this is the same as the Google Authenticator app, except that it stores the credentials on your Yubikey instead of the phone.
* If you have funky devices/apps that don't support TFA, you can set an application specific password using that tab. This includes sending E-mail from your personal Gmail account using your bixal.com IMAP, for instance.

### Github

* Visit <https://github.com/settings/two_factor_authentication/configure>
* Enable TFA, and complete the phone verification process (phone will act as backup TFA).
* Then you can "Register new device" in the "Security keys" section

### AWS Root Account

For each AWS account you have:

* Visit <https://console.aws.amazon.com/iam/home?region=us-east-1#security_credential>
* Under MFA, add a Virtual MFA device.
* Use Yubikey Authenticator app to scan the QR code, and enter the reponse code, then close and reopen the app and enter the second response code.

### AWS IAM Account

* Visit <https://console.aws.amazon.com/iam/home?region=us-east-1#users>
* Choose your user name
* Click on Manage your MFA device
* Use Google Authenticator app to scan the QR code, and enter the reponse code
* then close and reopen the app and enter the second response code.
* *using YubiKey untested - don't have Yubikey Authenticator set up*

## Securing your Laptop

Your laptop should lock (require a password to resume) on screen close and after 15 minutes idle time.

* [GNU/Linux specific instructions](linux.md)
* [Mac OS X specific instructions](macosx.md)

## YubiKey Neo U2F Setup

Some older Yubikeys (like the Neo) need to have U2F (Universal Second Factor) enabled before use. *(If you are unsure, ask any IT staff member or on the [#general](https://civicactions.slack.com/messages/general) slack channel.)*

For these Yubikeys, you may need to install and configure some software that "personalizes" your YubiKey. *Note: newer Yubikeys may not require this step.*

### Install Packages

#### Arch

*See also: <https://wiki.archlinux.org/index.php/yubikey>*

```
pacaur -S perl-net-ldap-server    # this is a prerequisite
pacaur -S yubikey-neo-manager-git
```

#### Fedora

*See also: <https://fedoraproject.org/wiki/Using_Yubikeys_with_Fedora>*

```
dnf copr enable jjelen/yubikey-neo-manager
dnf copr enable spartacus06/yubikey-utils
dnf install yubikey-neo-manager yubioath-desktop yubikey-personalization-gui
```

#### Ubuntu, Xubuntu

*See also: <https://askubuntu.com/questions/720314/how-to-install-yubikey-personalization-tool-on-ubuntu>*

```
sudo add-apt-repository ppa:yubico/stable
sudo apt-get update
sudo apt-get install yubikey-neo-manager yubikey-personalization yubikey-personalization-gui
```

#### Mac OS X

Download and install the [YubiKey Personalization Tool](https://itunes.apple.com/us/app/yubikey-personalization-tool/id638161122?mt=12) from the Mac App Store at [https://itunes.apple.com/us/app/yubikey-personalization-tool](https://itunes.apple.com/us/app/yubikey-personalization-tool/id638161122?mt=12)

### Personalize your YubiKey

This allows you to use your Yubikey with Google TFA (new fangled U2F), as well as LastPass (which uses the OTP application).

#### GNU/Linux command line

```
$ neoman
# Enable OTP, U2F, CCID checkboxes if needed, follow instructions to add and remove key.

​$ ykpersonalize -2 -ochal-resp -ochal-hmac -ohmac-lt64 -oserial-api-visible
```

#### Mac OSX YubiKey tool

*This should be straightforward, but waiting for a pull request that clearly explains how to:*

* Enable `OTP`, `U2F` & `CCID`
* Personalize **Configuration Slot 2** with options:
  * `chal-resp` (Set challenge-response mode)
  * `chal-hmac` (Generate HMAC-SHA1 challenge responses)
  * `hmac-lt64` (Calculate HMAC on less than 64 bytes input)
  * `serial-api-visible` (Allow serial number to be read using an API call)
