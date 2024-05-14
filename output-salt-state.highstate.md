```yaml
vagrant@master:/srv/salt$ sudo salt '*' state.highstate
minion:
----------
          ID: leos
    Function: group.present
      Result: True
     Comment: New group leos created
     Started: 11:46:49.461195
    Duration: 33.232 ms
     Changes:
              ----------
              gid:
                  1011
              members:
              name:
                  leos
              passwd:
                  x
----------
          ID: leos
    Function: user.present
      Result: True
     Comment: New user leos created
     Started: 11:46:49.496096
    Duration: 111.625 ms
     Changes:
              ----------
              fullname:
                  Leo S
              gid:
                  1011
              groups:
                  - leos
                  - users
              home:
                  /home/leos
              homephone:
              name:
                  leos
              other:
              passwd:
                  x
              roomnumber:
              shell:
                  /bin/bash
              uid:
                  1011
              workphone:
----------
          ID: git
    Function: pkg.installed
      Result: True
     Comment: The following packages were installed/updated: git
     Started: 11:46:51.492040
    Duration: 6334.905 ms
     Changes:
              ----------
              git:
                  ----------
                  new:
                      1:2.30.2-1+deb11u2
                  old:

Summary for minion
------------
Succeeded: 3 (changed=3)
Failed:    0
------------
Total states run:     3
Total run time:   6.480 s
win10:
----------
          ID: leos
    Function: user.present
      Result: True
     Comment: New user leos created
     Started: 14:46:55.235158
    Duration: 484.763 ms
     Changes:
              ----------
              account_disabled:
                  False
              account_locked:
                  False
              active:
                  True
              comment:
              description:
              disallow_change_password:
                  False
              expiration_date:
                  Never
              expired:
                  False
              failed_logon_attempts:
                  0
              fullname:
                  Leo S
              gid:
              groups:
                  - Administrators
              home:
              homedrive:
              last_logon:
                  Never
              logonscript:
              name:
                  leos
              passwd:
                  XXX-REDACTED-XXX
              password_changed:
                  2024-05-14 14:46:55
              password_never_expires:
                  False
              profile:
                  None
              successful_logon_attempts:
                  0
              uid:
                  S-1-5-21-3728580876-183366217-657147675-1007
----------
          ID: git
    Function: pkg.installed
      Result: True
     Comment: The following packages were installed/updated: git
     Started: 14:47:01.238520
    Duration: 53434.998 ms
     Changes:
              ----------
              git:
                  ----------
                  new:
                      2.45.0
                  old:

Summary for win10
------------
Succeeded: 2 (changed=2)
Failed:    0
------------
Total states run:     2
Total run time:  53.920 s
vagrant@master:/srv/salt$
```