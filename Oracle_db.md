oracle database 19c installation

1.Set the correct hostname on following files
# vi /etc/hosts
# vi /etc/hostname
# vi /etc/sysconfig/network

2.Create the new user and set password
# useradd oracle
# passwd oracle

3.Set secure Linux to permissive by editing the "/etc/selinux/config" file, making sure the
SELINUX flag is set as follows and then reboot the system.
#SELINUX=permissive

4.Switch to oracle root and create groups and add user
# groupadd oinstall
# usermod -g oinstll oracle
# id oracle
# groupadd oper
# groupadd dba
# gpasswd -a oracle oper
# gpasswd -a oracle dba
# id oracle

5.Change ulimit values
# ulimit -a
# ulimit -s 10240
# ulimit -n 65536

6. Use the "oracle-database-preinstall-19c" package to perform all your prerequisite setup,
issue the following command.
# yum install -y oracle-database-preinstall-19c

7.Once the change is complete, restart the server or run the following command.
# setenforce Permissive
# reboot

8.Create the directories in which the Oracle software will be installed.
# mkdir -p /u01/app/oracle/product/19.0.0/dbhome_1
# mkdir -p /u02/oradata
# chown -R oracle:oinstall /u01 /u02
# chmod -R 775 /u01 /u02

9.Create a "scripts" directory.
# mkdir /home/oracle/scripts

10.reate an environment file called "setEnv.sh". The "$"
characters are escaped using "\". If you are not creating the file
with the cat command, you will need to remove the escape
characters.

cat > /home/oracle/scripts/setEnv.sh <<EOF
# Oracle Settings
export TMP=/tmp
export TMPDIR=\$TMP
export ORACLE_HOSTNAME=ol7-19.localdomain
export ORACLE_UNQNAME=cdb1
export ORACLE_BASE=/u01/app/oracle
export ORACLE_HOME=\$ORACLE_BASE/product/19.0.0/dbhome_1
export ORA_INVENTORY=/u01/app/oraInventory
export ORACLE_SID=cdb1
export PDB_NAME=pdb1
export DATA_DIR=/u02/oradata
export PATH=/usr/sbin:/usr/local/bin:\$PATH
export PATH=\$ORACLE_HOME/bin:\$PATH
export LD_LIBRARY_PATH=\$ORACLE_HOME/lib:/lib:/usr/lib
export CLASSPATH=\$ORACLE_HOME/jlib:\$ORACLE_HOME/rdbms/jlib
EOF

11.Add a reference to the "setEnv.sh" file at the end of the
"/home/oracle/.bash_profile" file.
# echo ". /home/oracle/scripts/setEnv.sh" >> /home/oracle/.bash_profile

12.Create a "start_all.sh" and "stop_all.sh" script that can
be called from a startup/shutdown service. Make sure the
ownership and permissions are correct.

cat > /home/oracle/scripts/start_all.sh <<EOF
#!/bin/bash
. /home/oracle/scripts/setEnv.sh
export ORAENV_ASK=NO
. oraenv
export ORAENV_ASK=YES
dbstart \$ORACLE_HOME
EOF

cat > /home/oracle/scripts/stop_all.sh <<EOF
#!/bin/bash
. /home/oracle/scripts/setEnv.sh
export ORAENV_ASK=NO
. oraenv
export ORAENV_ASK=YES
dbshut \$ORACLE_HOME
EOF

13.execute the commands
# chown -R oracle:oinstall /home/oracle/scripts
# chmod u+x /home/oracle/scripts/*.sh

14.Switch to oracle “/home/oracle”
cd script
vi setEnv.sh
change the file lines
export ORACLE_HOSTNAME=hostname
make comment for line 5 and edit line 9
#export ORACLE_UNQNAME=cdb1
export ORACLE_SID=orcl
reboot

15.Switch to the “cd $ORACLE_HOME“directory, unzip the file and run
./runInstaller
