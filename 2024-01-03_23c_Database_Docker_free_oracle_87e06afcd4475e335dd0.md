<!--
title:   Docker で Oracle Database 23c Free (developer edition) を実行してみた
tags:    23c,Database,Docker,free,oracle
id:      87e06afcd4475e335dd0
private: false
-->
# ORACLE23CをDOCKERで実行する。
参考：https://container-registry.oracle.com/ords/ocr/ba/database/free

1. イメージのダウンロード
   `shell
   docker pull container-registry.oracle.com/database/free:latest
   `


2. 起動
   `shell
   docker run --name <ContainerName> \
       -p <HostPort>:1521  \
       -e ORACLE_PWD=<YourDatabasePasswords> \
       -e ORACLE_CHARACTERSET=<YourCharacterSet> \
       -v [<HostMountPoint - Database Persistence Volume>:]/opt/oracle/oradata \
       -v [<HostMountPoint - Running Scripts On Startup>:]/opt/oracle/scripts/startup \
       -v [<HostMountPoint - Running Scripts After Setup>:]/opt/oracle/scripts/setup \
          container-registry.oracle.com/database/free:latest
   `


3. 起動パラメータ
   | パラメータ名 | 説明 |
   |:-|:-|
   | ContainerName | コンテナの名前。 |
   | YourDatabasePasswords | SYS, SYSTEM and PDBADMINのパスワード。デフォルト値はランダムに生成。  |
   | YourCharacterSet | データベースの文字セット。デフォルト値は AL32UTF8。 |
   | HostMountPoint - Database Persistence Volume | データベースの永続化ディレクトリ。 |
   | HostMountPoint - Running Scripts After Setup | セットアップ後の実行スクリプト配置場所。 |
   | HostMountPoint - Running Scripts On Startup | 起動時の実行スクリプト配置場所。 |


4. 起動例
   `shell
   docker run -d -it --name oracle23c                                      \
       --restart=unless-stopped                                            \
       -p 1521:1521                                                        \
       -e ORACLE_PWD=manager                                               \
       -v /opt/oracle/oradata:/opt/oracle/oradata                          \
          container-registry.oracle.com/database/free:latest
   `



5. その他
   1. 実行権限
      /opt/oracle/oradata のディレクトリの権限に注意する必要あり。
      oracle:oinstall(UID=54321:GID=54321)で実行される。

      <details><summary>passwdファイル</summary>

      `shell
      docker exec -it  oracle23c cat /etc/passwd
      `

      `shell:/etc/passwd
      root:x:0:0:root:/root:/bin/bash
      bin:x:1:1:bin:/bin:/sbin/nologin
      daemon:x:2:2:daemon:/sbin:/sbin/nologin
      adm:x:3:4:adm:/var/adm:/sbin/nologin
      lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin
      sync:x:5:0:sync:/sbin:/bin/sync
      shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown
      halt:x:7:0:halt:/sbin:/sbin/halt
      mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
      operator:x:11:0:operator:/root:/sbin/nologin
      games:x:12:100:games:/usr/games:/sbin/nologin
      ftp:x:14:50:FTP User:/var/ftp:/sbin/nologin
      nobody:x:65534:65534:Kernel Overflow User:/:/sbin/nologin
      tss:x:59:59:Account used for TPM access:/dev/null:/sbin/nologin
      dbus:x:81:81:System message bus:/:/sbin/nologin
      systemd-coredump:x:999:996:systemd Core Dumper:/:/sbin/nologin
      systemd-resolve:x:193:193:systemd Resolver:/:/sbin/nologin
      sshd:x:74:74:Privilege-separated SSH:/var/empty/sshd:/sbin/nologin
      rpc:x:32:32:Rpcbind Daemon:/var/lib/rpcbind:/sbin/nologin
      rpcuser:x:29:29:RPC Service User:/var/lib/nfs:/sbin/nologin
      oracle:x:54321:54321::/home/oracle:/bin/bash
      `
      </details>

      <details><summary>groupファイル</summary>

      `shell
      docker exec -it  oracle23c cat /etc/group
      `

      `shell:/etc/group
      root:x:0:
      bin:x:1:
      daemon:x:2:
      sys:x:3:
      adm:x:4:
      tty:x:5:
      disk:x:6:
      lp:x:7:
      mem:x:8:
      kmem:x:9:
      wheel:x:10:
      cdrom:x:11:
      mail:x:12:
      man:x:15:
      dialout:x:18:
      floppy:x:19:
      games:x:20:
      tape:x:33:
      video:x:39:
      ftp:x:50:
      lock:x:54:
      audio:x:63:
      users:x:100:
      nobody:x:65534:
      utmp:x:22:
      utempter:x:35:
      tss:x:59:
      ssh_keys:x:999:
      dbus:x:81:
      input:x:998:
      kvm:x:36:
      render:x:997:
      systemd-journal:x:190:
      systemd-coredump:x:996:
      systemd-resolve:x:193:
      sshd:x:74:
      rpc:x:32:
      rpcuser:x:29:
      oinstall:x:54321:oracle
      dba:x:54322:oracle
      oper:x:54323:oracle
      backupdba:x:54324:oracle
      dgdba:x:54325:oracle
      kmdba:x:54326:oracle
      racdba:x:54330:oracle
      `
      </details>