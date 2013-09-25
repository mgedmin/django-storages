SFTP
====


Usage
*****

Useful when you want to upload files to a different server using SFTP, then serve directly from the other server over HTTP.
To use it as a default storage, specify this in your settings module::

  DEFAULT_FILE_STORAGE = 'storages.backends.sftpstorage.SFTPStorage'
  SFTP_STORAGE_HOST = 'media.example.com'
  SFTP_STORAGE_ROOT = '/var/www/media/'
  SFTP_STORAGE_PARAMS = {'username': 'sftp-upload'}
  MEDIA_URL = 'https://media.example.com/'

Then set up SSH public keys so your Django user can connect to the SFTP host, and make sure your known_hosts file has the remote server key.

If you don't want to use SSH public keys, you can specify a password in ``SFTP_STORAGE_PARAMS``.
Or, if you want to be manually prompted for the password, specify ``SFTP_STORAGE_INTERACTIVE = True``, but don't do that if you're using SFTPStorage for user uploads!
Interactive authentication can makes sense for ``STATICFILES_STORAGE``, but not for ``DEFAULT_FILE_STORAGE``.


Settings
--------

.. Based on https://bitbucket.org/david/django-storages/src/default/storages/backends/sftpstorage.py

``SFTP_STORAGE_HOST``

The hostname where you want the files to be saved.

``SFTP_STORAGE_ROOT``

The root directory on the remote host into which files should be placed.
Should work the same way that ``STATIC_ROOT`` works for local files.
Must include a trailing slash.

``SFTP_STORAGE_PARAMS`` (optional)

A dictionary containing connection parameters to be passed as keyword arguments to paramiko.SSHClient().connect().
Do not include hostname here.
See http://www.lag.net/paramiko/docs/paramiko.SSHClient-class.html#connect for details.
A brief list of the most useful options: ``port``, ``username``, ``password``, ``key_filename``, ``timeout``.

``SFTP_STORAGE_INTERACTIVE`` (optional)

A boolean indicating whether to prompt for a password if the connection cannot be made using keys, and there is not already a password in ``SFTP_STORAGE_PARAMS``.
You can set this to ``True`` to enable interactive login when running ``manage.py collectstatic``, for example.

.. warning::

    DO NOT set ``SFTP_STORAGE_INTERACTIVE`` to ``True`` if you are using this storage for files being uploaded to your site by users, because you'll have no way to enter the password when they submit the form.

``SFTP_STORAGE_FILE_MODE`` (optional)

A bitmask for setting permissions on newly-created files.
See http://docs.python.org/library/os.html#os.chmod for acceptable values.

``SFTP_STORAGE_DIR_MODE`` (optional)

A bitmask for setting permissions on newly-created directories.
See http://docs.python.org/library/os.html#os.chmod for acceptable values.

  Hint: if you start the mode number with ``0o`` you can express it in octal just like you would when doing ``chmod 775 myfile`` from bash.

``SFTP_STORAGE_UID`` (optional)

ID of the user account that should be set as owner of the files on the remote host.  You have to connect as root to set this.

.. XXX: must it be numeric or are usernames also acceptable?

``SFTP_STORAGE_GID`` (optional)

ID of the group that should be set on the files on the remote host.
You have to connect as a user that is a member of the group to set this.

.. XXX: must it be numeric or are group names also acceptable?

``SFTP_KNOWN_HOST_FILE`` (optional)

Absolute path of the known hosts file.
If it isn't set ``~/.ssh/known_hosts`` will be used.
