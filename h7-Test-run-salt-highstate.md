```shell
vagrant@master:~/2024k-ph-teht-salt$ sudo salt '*' state.highstate
minion:
----------
          ID: leos
    Function: group.present
      Result: True
     Comment: New group leos created
     Started: 12:00:05.336241
    Duration: 53.506 ms
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
     Started: 12:00:05.392552
    Duration: 169.892 ms
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
     Started: 12:00:07.849012
    Duration: 13623.916 ms
     Changes:
              ----------
              git:
                  ----------
                  new:
                      1:2.30.2-1+deb11u2
                  old:
              git-man:
                  ----------
                  new:
                      1:2.30.2-1+deb11u2
                  old:
              liberror-perl:
                  ----------
                  new:
                      0.17029-1
                  old:
              patch:
                  ----------
                  new:
                      2.7.6-7
                  old:

Summary for minion
------------
Succeeded: 3 (changed=3)
Failed:    0
------------
Total states run:     3
Total run time:  13.847 s
win10:
----------
          ID: leos
    Function: user.present
      Result: True
     Comment: New user leos created
     Started: 15:00:12.169829
    Duration: 609.18 ms
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
                  2024-05-15 15:00:12
              password_never_expires:
                  False
              profile:
                  None
              successful_logon_attempts:
                  0
              uid:
                  S-1-5-21-506423446-3976816029-3590346600-1002
----------
          ID: git
    Function: pkg.installed
      Result: True
     Comment: The following packages were installed/updated: git
     Started: 15:00:20.794455
    Duration: 42296.434 ms
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
Total run time:  42.906 s
vagrant@master:~/2024k-ph-teht-salt$
```