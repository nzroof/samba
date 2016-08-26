[![logo](https://raw.githubusercontent.com/nzroof/samba/master/logo.jpg)](https://www.samba.org)

# Samba

Samba docker container

# Release Tags
0.1 26/08/2016 - Experimental

# What is Samba?

Since 1992, Samba has provided secure, stable and fast file and print services
for all clients using the SMB/CIFS protocol, such as all versions of DOS and
Windows, OS/2, Linux and many others.

# How to use this image

By default there are no shares configured, additional ones can be added.

## Hosting a Samba instance

    sudo docker run -it -p 139:139 -p 445:445 -d roofnz/samba

OR set local storage:

    sudo docker volume create files
    sudo docker run -it --name samba -p 139:139 -p 445:445 \
                -v files:/mount \
                -d roofnz/samba

## Configuration

These options are for the entrypoint and must appear after roofnz/samba

    sudo docker run -it --rm roofnz/samba -h
    Usage: samba.sh [-opt] [command]
    Options (fields in '[]' are optional, '<>' are required):
        -h          This help
        -g \"<group>\"  Add a group
                    Only necessary if adding groups to shares with no user assigned
                    Otherwise they will be created automatically with user
                    NOTE: Must start with smb
        -i "<path>" Import smbpassword
                    required arg: "<path>" - full file path in container
        -n          Start the 'nmbd' daemon to advertise the shares
        -p          Set ownership and permissions on the shares
        -s "<name;/path>[;browsable;readonly;guest;users]" Configure a share
                    required arg: "<name>;<comment>;</path>"
                    <name> is how it's called for clients
                    <path> path to share
                    NOTE: for the default values, just leave blank
                    [browsable] default:'yes' or 'no'
                    [readonly] default:'yes' or 'no'
                    [guest] allowed default:'yes' or 'no'
                    [users] allowed default:'all' or list of allowed users or groups
                    [admins] allowed default:'none' or list of admin users or groups
        -t ""       Configure timezone
                    possible arg: "[timezone]" - zoneinfo timezone for container
        -u "<username;password>"       Add a user
                    required arg: "<username>;<passwd>"
                    <username> for user
                    <password> for user
        -w "<workgroup>"       Configure the workgroup (domain) samba should use
                    required arg: "<workgroup>"
                    <workgroup> for samba

    The 'command' (if provided and valid) will be run instead of samba

ENVIRONMENT VARIABLES (only available with `docker run`)

 * `NMBD` - As above, enable nmbd
 * `TZ` - As above, set a zoneinfo timezone, IE `EST5EDT`
 * `WORKGROUP` - As above, set workgroup

**NOTE**: if you enable nmbd (via `-n` or the `NMBD` environment variable), you
will also want to expose port 137 with `-p 137:137`.

## Examples

Any of the commands can be run at creation with `docker run` or later with
`docker exec -it samba.sh` (as of version 1.3 of docker).

### Setting the Timezone

    sudo docker run -it -p 139:139 -p 445:445 -d roofnz/samba -t EST5EDT

OR using `environment variables`

    sudo docker run -it -e TZ=EST5EDT -p 139:139 -p 445:445 -d roofnz/samba

Will get you the same settings as

    sudo docker run -it --name samba -p 139:139 -p 445:445 -d roofnz/samba
    sudo docker exec -it samba samba.sh -t EST5EDT ls -AlF /etc/localtime
    sudo docker restart samba

### Start an instance creating users and shares:

    sudo docker run -it -p 139:139 -p 445:445 -d dperson/samba \
                -u "example1;badpass;" \
                -u "example2;badpass;smbaccounts,smboregon" \
                -g "smbadmin"
                -g "smbmexico"
                -s "public;/share" \
                -s "users;/srv;no;no;no;example1,example2,@smbmexico;@smbadmins" \
                -s "example1 private;/example1;no;no;no;example1" \
                -s "example2 private;/example2;no;no;no;example2"

# User Feedback

## Issues

If you have any problems with or questions about this image, please contact me
through a [GitHub issue](https://github.com/nzroof/samba/issues).

## History

This image was forked from dperson/samba to add group support and to authenticate users with Azure Active Directory.