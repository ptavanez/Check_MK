#!/bin/bash
# CMK setup
# V0.1
# Made to work with Virtual Machines or LXD Containers
# Move the above to config aux file
scriptname="1madafucka"
snorlax=7
#user_cmk="cmk_installer"
#secret="NUCNDXM@UBOOVCWMLSKF"
#cmk_agent_1_5_19="http://172.25.150.49/magccme01/check_mk/agents/special/check-mk-agent-1.5.0p19-1.noarch.rpm"
#Should also gather all path and expose them in an informative function like help usage
#
function welcome {
  clear
  echo "***********************************************"
  echo "Welcome to Initial Setup"
  echo "Here we will configure what kind of installation (Distributed or Single Node) and proxies if needed"
  echo "***************** Here we go ! ****************"
  read -p "Are you behind a proxy ?[Y/N/Q]" prx_srv
  case $prx_srv in
    ([Yy])
        read -p "Specify [IP Port] : " prx_ip prx_port
        logeverything proxied_server
        echo "Storing and configuring HTTPX Variables"
        echo "HTTP PROXY" >> $logdir/httpx_vars.log
        export http_proxy=$prx_ip:$prx_port >> $logdir/httpx_vars.log
        echo "HTTP PROXY" >> $logdir/httpx_vars.log
        export https_proxy=$prx_ip:$prx_port >> $logdir/httpx_vars.log
        echo "Stored and Configured"
        cmkdownload
        ;;
    ([Nn])
        echo "Fine, I'll assume internet access or local repo provided (I'll add mount/NFS later)"
        # Needs to ask where is it placed in Master ? in path
        read -p " Will you retrieve file from Master or Path ?" local_source
        case $local_source in
          'Master') cmkdownload_local Master;;
          'Path') cmkdownload_local path;;
        esac
        ;;
    ([Qq]) exit;;
  esac
}

function installer {
  clear
  echo "Welcome to CMK Installer"
  read -p "Distributed or Single node setup? [D/S/Z/M/Quit]" install_type
  case $install_type in
    ([Dd]) distributed_setup;;
    ([Ss]) singlenode_setup;;
    ([Zz]) proxiednetwork;;
    ([Mm]) special_menu;;
    ([Qq]) exit;;
  esac
}
function logeverything () {
  clear
  echo "Creating Log dir"
  mkdir -v -p "/tmp/$scriptname/log/$1"
  logdir="/tmp/$scriptname/log/$1"
  echo "$logdir created"
  sleep $snorlax
  #debug
}

function cmkdownload () {
  # Since we have just specified proxies internet access must be tested and Check_MK download must be done - Attention Enterprise Editions Require Authentication
  clear
  echo "Internet access will be tested in order to proceed with Check_MK Download"
  echo "ATTENTION ENTERPRISE EDITIONS REQUIRES LICENCED ACCOUNT"
  status_code=$(curl --write-out %{http_code} --silent --output /dev/null https://checkmk.com)
  if [[ $status_code -ne 200 ]] ; then
    echo "Connection to Check_MK Website failed with code $status_code"
    exit
  else
    echo "Internet connection passed with code $status_code"
    read -p  "Which Check_MK Version to Download [CRE/CEE/CME] [1.(5)/1.(6)] ?" cmk_edition cmk_version
    case $cmk_edition in
      'CRE') cmk_downloader raw;;
      'CEE') cmk_downloader enterprise;;
      'CME') cmk_downloader managed;;
    esac
    # cmk_downloader $cmk_version $cmk_edition
  fi
}

function cmkdownload_local () {
  echo "local download and install"
  echo $1
  if [[ $1 == 'Master' ]] ; then
    read -p "Master Instance IP ? " master_ip
    echo $master_ip
  else
    read -p "Full Path ? " full_path
    echo $full_path
  fi
}

function cmk_downloader () {
    echo "Downloading Check_MK $cmk_edition AKA $1 || $cmk_version"
    sleep $snorlax
    #https://checkmk.com/support/1.6.0p2/check-mk-raw-1.6.0p2-el7-38.x86_64.rpm
    #https://checkmk.com/support/1.6.0p2/check-mk-enterprise-1.6.0p2-el7-38.x86_64.rpm
    #https://checkmk.com/support/1.6.0p2/check-mk-managed-1.6.0p2-el7-38.x86_64.rpm
}

function debug {
  #Proxy Debug
  #Lacks debug dir to echo stuff - CREATED NOW POINT STUFF
  clear
  echo "DEBUG MODE"
  echo "DEBUG LOGDIR"
  echo $logdir
  mkdir "$logdir/debug/"
  touch "$logdir/debug/debug.log"
  log_debug="$logdir/debug/debug.log"
  echo "Proxy Debug" >> $log_debug
  echo "START" >> $log_debug
  echo $http_proxy >> $log_debug
  echo $https_proxy >> $log_debug
  env | grep http* >> $log_debug
  echo "END" >> $log_debug
}
function distributed_setup {
  #TEST Master <---> Slave (6556/6557)
  mkdir -v -p "$logdir/distributed_setup"
  dst_setup_log="$logdir/distributed_setup/"
  logeverything dst_setup

}
function singlenode_setup {
  echo "s NODE"
}
#Maybe could be sourced, it will have python stuff
function special_menu {
echo "Register Slave in Master"
echo "install MK Agent (Web/local)"
}
welcome
