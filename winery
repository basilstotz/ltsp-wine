#!/bin/sh

if [ $# -ne 2 ]; then
  echo 
  echo "Usage: $(basename $0) \"</path/to/winecontainer>\" \"<drive:/windows/path/to/app.exe>\" "
  echo 
  echo "       example:" 
  echo "       $(basename $0) \"/home/wine/Blitzrechnen/\" \"c:/Program Files/Klett/Blitzrechnen/Blitzrechnen.exe\" "
  echo 
  exit 1
fi
 

MASTERPREFIX="$1"
EXEPATH="$2"

if ! [ -d "${MASTERPREFIX}/drive_c" ]; then
  echo
  echo "Error: '${MASTERPREFIX}' looks not like a wine-container. Exiting..."
  echo 
  exit 1
fi

WINEPREFIX="${HOME}/.$(basename "${MASTERPREFIX}")"
#                   ^ here is a point!!!!!!!!

# remove WINEPREFIX if newer version exists
if [ -d "$WINEPREFIX" ]; then
   echo -n "Tha app is already installed, "
   if [ "$MASTERPREFIX" -nt "$WINEPREFIX" ];  then
     echo -n "but a newer app exists. Removing old..."
     rm -rf "$WINEPREFIX"
     echo "OK"
   else
     echo "The app is the newest version. OK"
   fi
fi

# install WINEPREFIX if missing

if ! [ -d "$WINEPREFIX" ]; then

  # create and populate WINWPREFIX

  echo -n "The app does not exist. Installing..."
  mkdir -p "$WINEPREFIX"
  lndir "$MASTERPREFIX" "$WINEPREFIX" 2>/dev/null/ >/dev/null
  echo "OK"
  
  # empty profiles

  PROFILES="${WINEPREFIX}/drive_c/windows/profiles"
  if [ -d "$PROFILES" ]; then
    rm -r "$PROFILES"
  fi
  mkdir  "$PROFILES"
 
  # remove user.reg if it exists

  if [ -h "${WINEPREFIX}/user.reg" ]; then rm "${WINEPREFIX}/user.reg"; fi 

  # copy read/write files
  #                                 system.reg
  for FILE in ".no_prelaunch_window_flag userdef.reg"; do
    if [ -h "${WINEPREFIX}/${FILE}" ]; then
       rm -f "${WINEPREFIX}/${FILE}"
       cp "${MASTERPREFIX}/${FILE}" "${WINEPREFIX}/${FILE}"
    fi
  done

  # change access mode to privat

  chmod -R og-rwx "${WINEPREFIX}"
fi

#start exe

env WINEPREFIX="${WINEPREFIX}" wine "${WINEPREFIX}/dosdevices/${EXEPATH}"

exit $?
