# iCloud Photos Downloader

[![Build Status](https://travis-ci.org/ndbroadbent/icloud_photos_downloader.svg?branch=master)](https://travis-ci.org/ndbroadbent/icloud_photos_downloader)
[![Coverage Status](https://coveralls.io/repos/github/ndbroadbent/icloud_photos_downloader/badge.svg?branch=master)](https://coveralls.io/github/ndbroadbent/icloud_photos_downloader?branch=master)

* A command-line tool to download all your iCloud photos.
* Works on Mac, Linux, and Windows.
* Run it multiple times to download any new photos.

## Requirements

* Python 2.7, >= 3.4
  * *Python 2.6 is not supported.*
* pip

### How to install Python & pip

#### Windows

[Download Python 3.7.0](https://www.python.org/ftp/python/3.7.0/python-3.7.0.exe)

#### Mac

Install [Homebrew](https://brew.sh/) (if not already installed):

```
which brew > /dev/null 2>&1 || /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

Then install Python:

```
brew install python
```

Alternatively, you can
[download the Python 3.7.0 installer for Mac](https://www.python.org/ftp/python/3.7.0/python-3.7.0-macosx10.9.pkg).


#### Linux (Ubuntu)

```
sudo apt-get update
sudo apt-get install -y python
```


## Install iCloud Photos Downloader

```
sudo pip install icloudpd
```

> Note: You may not need sudo for `pip install` on your system.

## Authentication

If your account has two-factor authentication enabled,
you will be prompted for a code when you run the script.

Two-factor authentication will expire after an interval set by Apple,
at which point you will have to re-authenticate. This interval is currently two months.

You can receive an email notification when two-factor authentication expires by passing the
`--smtp-username` and `--smtp-password` options. Emails will be sent to `--smtp-username` by default,
or you can send to a different email address with `--notification-email`.

If you want to send notification emails using your Gmail account, and you have enabled two-factor authentication, you will need to generate an App Password at https://myaccount.google.com/apppasswords


### System Keyring

You can store your password in the system keyring using the `icloud` command-line tool
(installed with the `pyicloud` dependency):

    $ icloud --username=jappleseed@apple.com
    ICloud Password for jappleseed@apple.com:
    Save password in keyring? (y/N)

If you have stored a password in the keyring, you will not be required to provide a password
when running the script.

If you would like to delete a password stored in your system keyring,
you can clear a stored password using the `--delete-from-keyring` command-line option:

    $ icloud --username=jappleseed@apple.com --delete-from-keyring

## Usage

    $ icloudpd <download_directory>
               --username=<username>
               [--password=<password>]
               [--size=(original|medium|thumb)]
               [--recent <integer>]
               [--until-found <integer>]
               [--skip-videos]
               [--force-size]
               [--auto-delete]
               [--only-print-filenames]
               [--folder-structure=({:%Y/%m/%d})]
               [--set-exif-datetime]
               [--smtp-username <smtp_username>]
               [--smtp-password <smtp_password>]
               [--smtp-host <smtp_host>]
               [--smtp-port <smtp_port>]
               [--smtp-no-tls]
               [--notification-email <notification_email>]

    Options:
        --username <username>           Your iCloud username or email address
        --password <password>           Your iCloud password (default: use PyiCloud
                                        keyring or prompt for password)
        --size [original|medium|thumb]  Image size to download (default: original)
        --recent INTEGER RANGE          Number of recent photos to download
                                        (default: download all photos)
        --until-found INTEGER RANGE     Download most recently added photos until we
                                        find x number of previously downloaded
                                        consecutive photos (default: download all
                                        photos)
        --skip-videos                   Don't download any videos (default: Download
                                        both photos and videos)
        --force-size                    Only download the requested size (default:
                                        download original if size is not available)
        --auto-delete                   Scans the "Recently Deleted" folder and
                                        deletes any files found in there. (If you
                                        restore the photo in iCloud, it will be
                                        downloaded again.)
        --only-print-filenames          Only prints the filenames of all files that
                                        will be downloaded. (Does not download any
                                        files.)
        --folder-structure <folder_structure>
                                        Folder structure (default: {:%Y/%m/%d})
        --set-exif-datetime             Write the DateTimeOriginal exif tag from
                                        file creation date, if it doesn't exist.
        --smtp-username <smtp_username>
                                        Your SMTP username, for sending email
                                        notifications when two-step authentication
                                        expires.
        --smtp-password <smtp_password>
                                        Your SMTP password, for sending email
                                        notifications when two-step authentication
                                        expires.
        --smtp-host <smtp_host>         Your SMTP server host. Defaults to:
                                        smtp.gmail.com
        --smtp-port <smtp_port>         Your SMTP server port. Default: 587 (Gmail)
        --smtp-no-tls                   Pass this flag to disable TLS for SMTP (TLS
                                        is required for Gmail)
        --notification-email <notification_email>
                                        Email address where you would like to
                                        receive email notifications. Default: SMTP
                                        username
        -h, --help                      Show this message and exit.

Example:

    $ icloudpd ./Photos \
        --username=testuser@example.com \
        --password=pass1234 \
        --size=original \
        --recent 500 \
        --auto-delete


## Error on first run

When you run the script for the first time, you might see an error message like this:

```
Bad Request (400)
```

This error often happens because your account hasn't used the iCloud API before, so Apple's servers need to do some work to prepare some information about your photos. This process can take around 5-10 minutes, so please wait a few minutes and try again.

If you are still seeing this message after 30 minutes, then please open an issue on GitHub and post all of the output from the script.


## Run once every 3 hours using Cron

    cp cron_script.sh.example cron_script.sh

* Edit cron_script.sh with your username, password, and other options

* Run `crontab -e`, and add the following line:

```
0 */3 * * * /path/to/icloud_photos_downloader/cron_script.sh
```

## Docker

* Build the image:

```
$ git clone https://github.com/ndbroadbent/icloud_photos_downloader.git
$ cd icloud_photos_downloader.git/docker
$ docker build -t icloud_photos_downloader .
```

* Usage:

```
$ docker run -it --rm --name icloud -v $(pwd)/Photos:/data icloud_photos_downloader ./icloudpd/base.py \
    --username=testuser@example.com \
    --password=pass1234 \
    --size=original \
    --recent 500 \
    --auto-delete \
    /data
```

## Contributing

Install dependencies:

```
sudo pip install -r requirements.txt
sudo pip install -r requirements-test.txt
```

Run tests:

```
pytest
```

## Support Development

<a href='https://ko-fi.com/ndbroadbent' target='_blank'><img height='36' style='border:0px;height:36px;' src='https://az743702.vo.msecnd.net/cdn/kofi4.png?v=0' border='0' alt='Buy Me a Coffee' /></a>

* *Bitcoin (BTC)*: 15eSc6JiwBtxte9zak44ZFhw9bkKWaMGAe
* *Bitcoin Cash (BCH)*: 157a1zxtY7vozdkrom2aSiVu4oZ6JkXpSU
* *Ethereum (ETH)*: 0x080300A117758EC601b7b6c501afE3571E5cA1c3
* *Litecoin (LTC)*: LP7pURasgBVU8FMh4Rx2WW45iWCnMoGDMD
