# Palvelinten hallinta - h7: Oma moduuli

Tämä harjoitus on osa Tero Karvisen pitämää Palvelinten hallinta ICT4TN022-3014 -kurssia Haaga-Helia ammattikorkeakoulussa syksyllä 2021.

Tätä raporttia on tehty monessa osassa ja otsikot menevät päivämäärän mukaan.  Koneena oli Lenovo Yoga Slim 7 -kannettava Windows 11 -käyttöjärjestelmällä ja koneelle asennetun Oracle VirtualBox (6.1) -kautta Debian 11 “bullseye”.

## 06.12.2021 Projektin alkusuunnitelma ja ensimmäinen 'raakaversio' <a href="06.12.2021"></a>

Aloitin harjoituksen noin klo 19 aikoihin.

Rakennan moduulin omaan käyttöön. Tarkoitus on luoda moduuli, jolla asennan itselleni tavallisimpia ohjelmia ja konfiguraatioita Ubuntu Desktop 20.04.3 -käyttöjärjestelmään. Olen tekemässävanhasta Windows-kannettavastani Linux-konetta jossain vaiheessa ja haluaisin luoda itselleni moduuli, jota voin käyttää kannettavani alkukongifuraatioiden määrittelemisessä ja mahdollisesti myös myöhemmin muissa koneissa (vaatinee myös moduulin kehittämistä kurssin jälkeen).

Alkusuunnitelma: *(saattaa muuttua projektin edetessä)*

* Bash completion
* Tree
* Net-tools
* Git
* Nanon konfigurointi
* SSH:n konfiguraatiot
* Palomuuriasetukset päälle ja porttien avaukset

### pkgs -moduuli<a href="06.12.2021pkgs"></a>
Aloitin luomalla tilan niille ohjelmille, jotka aion pelkästään asentaa ilman konfiguraatioita:

* Bash completion
* Tree
* Net-tools
* Git

```
# Ensin loin "pkgs" moduulin ja init.sls
$ sudo mkdir /srv/salt/pkgs
$ sudoedit /srv/salt/pkgs/init.sls

# Tämän jälkeen kirjoitin ohjelmien asennuksen
1 default_pkgs:
2   pkg.installed:
3     - pkgs:
4       - bash-completion
5       - tree
6       - net-tools
7       - git

# Sen jälkeen testasin ajamalla paikallisesti masterilla
$ sudo salt-call --local state.apply pkgs

# Ajo onnistui - ohjelmat oli jo asennettuna
local:
----------
          ID: default_pkgs
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 19:52:27.468533
    Duration: 52.616 ms
     Changes:   

Summary for local
------------
Succeeded: 1
Failed:    0
------------
Total states run:     1
Total run time:  52.616 ms
```

### nano -moduuli<a href="06.12.2021nano"></a>

Sen jälkeen loin konfiguraation Nanolle. Koska olen tekemässä itselleni tarpeellista konfiguraatiota, minulta löytyi jo valmiiksi masterilta omiin tarpeisiini sopiva Nanon konfiguraatiotiedosto. Päätin kopioida sen masterilta Saltin juureen samalla siivoen ylimääräiset pois, kuten tein [harjoituksessa 5 kohdassa a)](https://github.com/tuuli-huhtanen/palvelintenhallinta/blob/main/h5-demonit.md#a).

Omiin konfiguraatioihin kuuluu default-asetuksista poiketen: 

* `set linenumbers` = rivinumerot näkyviin
* `set softwrap` = wrappaa tekstin ikkunassa

```
# Ensin loin "nano" moduulin ja init.sls
$ sudo mkdir /srv/salt/nano
$ sudoedit mkdir /srv/salt/nano/init.sls

# Loin init.sls tiedostoon ohjelmoinnin, joka vie Nanon konfiguraatiotiedoston nanorc sen oikeaan polkuun /etc -hakemistoon ja hakee halutun tiedoston Saltin juuresta
 1 /etc/nanorc:
 2   file.managed:
 3     - source: salt://nano/nanorc

# Hain toimivan nanorc -tiedoston masterilta ja siivosiin siitä samalla ylimääräiset pois
$ cat /etc/nanorc |grep -v ^#|grep -v ^$| sudo tee /srv/salt/nano/nanorc

# Tiedosto sisälsi seuraavat asetukset:
$ cat /srv/salt/nano/nanorc 
set linenumbers 
set softwrap
set historylog
set locking
set stateflags
set suspendable
include "/usr/share/nano/*.nanorc"

# Ajoin testinä paikallisesti masterilla:
$ sudo salt-call --local state.apply nano

#Ajo onnistui, muutoksia tuli, koska olin siivonnut tiedostoa:
local:
----------
          ID: /etc/nanorc
    Function: file.managed
      Result: True
     Comment: File /etc/nanorc updated
     Started: 20:28:11.947910
    Duration: 31.525 ms
     Changes:   
              ----------
              diff:
                  --- 
                  +++ 
                  @@ -1,298 +1,7 @@
                   set linenumbers
                   set softwrap
                  -
                  -## Sample initialization file for GNU nano.
                  -##
                  -## For the options that take parameters, the default value is shown.
                  -## Other options are unset by default.  To make sure that an option
                  -## is disabled, you can use "unset <option>".
                  -##
                  -## Characters that are special in a shell should not be escaped here.
                  -## Inside string parameters, quotes should not be escaped -- the last
                  -## double quote on the line will be seen as the closing quote.
                  -
                  -## Make 'nextword' (Ctrl+Right) and 'chopwordright' (Ctrl+Delete)
                  -## stop at word ends instead of at beginnings.
                  -# set afterends
                  -
                  -## When soft line wrapping is enabled, make it wrap lines at blanks
                  -## (tabs and spaces) instead of always at the edge of the screen.
                  -# set atblanks
                  -
                  -## Automatically indent a newly created line to the same number of
                  -## tabs and/or spaces as the preceding line -- or as the next line
                  -## if the preceding line is the beginning of a paragraph.
                  -# set autoindent
                  -
                  -## Back up files to the current filename plus a tilde.
                  -# set backup
                  -
                  -## The directory to put unique backup files in.
                  -# set backupdir ""
                  -
                  -## Use bold text instead of reverse video text.
                  -# set boldtext
                  -
                  -## Treat any line with leading whitespace as the beginning of a paragraph.
                  -#set bookstyle
                  -
                  -## The characters treated as closing brackets when justifying paragraphs.
                  -## This may not include any blank characters.  Only closing punctuation,
                  -## optionally followed by these closing brackets, can end sentences.
                  -# set brackets ""')>]}"
                  -
                  -## Automatically hard-wrap the current line when it becomes overlong.
                  -# set breaklonglines
                  -
                  -## Do case-sensitive searches by default.
                  -# set casesensitive
                  -
                  -## Constantly display the cursor position in the status bar.  Note that
                  -## this overrides "quickblank".
                  -# set constantshow
                  -
                  -## Use cut-from-cursor-to-end-of-line by default.
                  -# set cutfromcursor
                  -
                  -## Do not use the line below the title bar, leaving it entirely blank.
                  -# set emptyline
                  -
                  -## Set the target width for automatic hard-wrapping and for justifying
                  -## paragraphs.  If the specified value is 0 or less, the wrapping point
                  -## will be the terminal's width minus this number.
                  -# set fill -8
                  -
                  -## Remember the used search/replace strings for the next session.
                   set historylog
                  -
                  -## Display a "scrollbar" on the righthand side of the edit window.
                  -# set indicator
                  -
                  -## Scroll the buffer contents per half-screen instead of per line.
                  -# set jumpyscrolling
                  -
                  -## Display line numbers to the left (and any anchors in the margin).
                  -# set linenumbers
                  -
                  -## Enable vim-style lock-files.  This is just to let a vim user know you
                  -## are editing a file [s]he is trying to edit and vice versa.  There are
                  -## no plans to implement vim-style undo state in these files.
                   set locking
                  -
                  -## Fall back to slow libmagic to try and determine an applicable syntax.
                  -# set magic
                  -
                  -## The opening and closing brackets that can be found by bracket
                  -## searches.  They cannot contain blank characters.  The former set must
                  -## come before the latter set, and both must be in the same order.
                  -# set matchbrackets "(<[{)>]}"
                  -
                  -## Enable mouse support, if available for your system.  When enabled,
                  -## mouse clicks can be used to place the cursor, set the mark (with a
                  -## double click), and execute shortcuts.  The mouse will work in the X
                  -## Window System, and on the console when gpm is running.
                  -# set mouse
                  -
                  -## Switch on multiple file buffers (inserting a file will put it into
                  -## a separate buffer).
                  -# set multibuffer
                  -
                  -## Don't convert files from DOS/Mac format.
                  -# set noconvert
                  -
                  -## Don't display the helpful shortcut lists at the bottom of the screen.
                  -# set nohelp
                  -
                  -## Don't automatically add a newline when a file does not end with one.
                  -# set nonewlines
                  -
                  -## Set operating directory.  nano will not read or write files outside
                  -## this directory and its subdirectories.  Also, the current directory
                  -## is changed to here, so any files are inserted from this dir.  A blank
                  -## string means the operating-directory feature is turned off.
                  -# set operatingdir ""
                  -
                  -## Remember the cursor position in each file for the next editing session.
                  -# set positionlog
                  -
                  -## Preserve the XON and XOFF keys (^Q and ^S).
                  -# set preserve
                  -
                  -## The characters treated as closing punctuation when justifying
                  -## paragraphs.  They cannot contain blank characters.  Only closing
                  -## punctuation, optionally followed by closing brackets, can end
                  -## sentences.
                  -# set punct "!.?"
                  -
                  -## Do quick status-bar blanking.  Status-bar messages will disappear after
                  -## 1 keystroke instead of 26.  Note that "constantshow" overrides this.
                  -# set quickblank
                  -
                  -## The regular expression that matches quoting characters in email
                  -## or line-comment introducers in source code.  The default is:
                  -# set quotestr "^([ 	]*([!#%:;>|}]|//))+"
                  -
                  -## Try to work around a mismatching terminfo terminal description.
                  -# set rawsequences
                  -
                  -## Fix Backspace/Delete confusion problem.
                  -# set rebinddelete
                  -
                  -## Do regular-expression searches by default.
                  -## Regular expressions are of the extended type (ERE).
                  -# set regexp
                  -
                  -## Save a changed buffer automatically on exit; don't prompt.
                  -# set saveonexit
                  -## (The old form of this option, 'set tempfile', is deprecated.)
                  -
                  -## Put the cursor on the highlighted item in the file browser, and show
                  -## the cursor in the help viewer; useful for people who use a braille
                  -## display and people with poor vision.
                  -# set showcursor
                  -
                  -## Make the Home key smarter.  When Home is pressed anywhere but at the
                  -## very beginning of non-whitespace characters on a line, the cursor
                  -## will jump to that beginning (either forwards or backwards).  If the
                  -## cursor is already at that position, it will jump to the true
                  -## beginning of the line.
                  -# set smarthome
                  -
                  -## Spread overlong lines over multiple screen lines.
                  -# set softwrap
                  -
                  -## Use this spelling checker instead of the internal one.  This option
                  -## does not have a default value.
                  -# set speller "aspell -x -c"
                  -
                  -## Use the end of the title bar for some state flags: I = auto-indenting,
                  -## M = mark, L = hard-wrapping long lines, R = recording, S = soft-wrapping.
                   set stateflags
                  -
                  -## Allow nano to be suspended (with ^Z by default).
                   set suspendable
                  -## (The old form of this option, 'set suspend', is deprecated.)
                  -
                  -## Use this tab size instead of the default; it must be greater than 0.
                  -# set tabsize 8
                  -
                  -## Convert typed tabs to spaces.
                  -# set tabstospaces
                  -
                  -## Snip whitespace at the end of lines when justifying or hard-wrapping.
                  -# set trimblanks
                  -
                  -## The two single-column characters used to display the first characters
                  -## of tabs and spaces.  187 in ISO 8859-1 (0000BB in Unicode) and 183 in
                  -## ISO-8859-1 (0000B7 in Unicode) seem to be good values for these.
                  -## The default when in a UTF-8 locale:
                  -# set whitespace "»·"
                  -## The default otherwise:
                  -# set whitespace ">."
                  -
                  -## Detect word boundaries differently by treating punctuation
                  -## characters as parts of words.
                  -# set wordbounds
                  -
                  -## The characters (besides alphanumeric ones) that should be considered
                  -## as parts of words.  This option does not have a default value.  When
                  -## set, it overrides option 'set wordbounds'.
                  -# set wordchars "<_>."
                  -
                  -## Let an unmodified Backspace or Delete erase the marked region (instead
                  -## of a single character, and without affecting the cutbuffer).
                  -# set zap
                  -
                  -## Paint the interface elements of nano.  These are examples;
                  -## by default there are no colors, except for errorcolor.
                  -# set titlecolor bold,lightwhite,blue
                  -# set statuscolor bold,lightwhite,green
                  -# set errorcolor bold,lightwhite,red
                  -# set selectedcolor lightwhite,magenta
                  -# set stripecolor ,yellow
                  -# set scrollercolor cyan
                  -# set numbercolor cyan
                  -# set keycolor cyan
                  -# set functioncolor green
                  -
                  -## In root's .nanorc you might want to use:
                  -# set titlecolor bold,lightwhite,magenta
                  -# set statuscolor bold,lightwhite,magenta
                  -# set errorcolor bold,lightwhite,red
                  -# set selectedcolor lightwhite,cyan
                  -# set stripecolor ,yellow
                  -# set scrollercolor magenta
                  -# set numbercolor magenta
                  -# set keycolor lightmagenta
                  -# set functioncolor magenta
                  -
                  -
                  -## === Syntax coloring ===
                  -## For all details, see 'man nanorc', section SYNTAX HIGHLIGHTING.
                  -
                  -## To include most of the existing syntax definitions, you can do:
                   include "/usr/share/nano/*.nanorc"
                  -
                  -## Or you can select just the ones you need.  For example:
                  -# include "/usr/share/nano/html.nanorc"
                  -# include "/usr/share/nano/python.nanorc"
                  -# include "/usr/share/nano/sh.nanorc"
                  -
                  -## In /usr/share/nano/extra/ you can find some syntaxes that are
                  -## specific for certain distros or for some less common languages.
                  -
                  -
                  -## If <Tab> should always produce four spaces when editing a Python file,
                  -## independent of the settings of 'tabsize' and 'tabstospaces':
                  -# extendsyntax python tabgives "    "
                  -
                  -## If <Tab> should always produce an actual TAB when editing a Makefile:
                  -# extendsyntax makefile tabgives "	"
                  -
                  -
                  -## === Key bindings ===
                  -## For all details, see 'man nanorc', section REBINDING KEYS.
                  -
                  -## The <Ctrl+Delete> keystroke deletes the word to the right of the cursor.
                  -## On some terminals the <Ctrl+Backspace> keystroke produces ^H, which is
                  -## the ASCII character for backspace, so it is bound by default to the
                  -## backspace function.  The <Backspace> key itself produces a different
                  -## keycode, which is hard-bound to the backspace function.  So, if you
                  -## normally use <Backspace> for backspacing and not ^H, you can make
                  -## <Ctrl+Backspace> delete the word to the left of the cursor with:
                  -# bind ^H chopwordleft main
                  -
                  -## If you would like nano to have keybindings that are more "usual",
                  -## such as ^O for Open, ^F for Find, ^H for Help, and ^Q for Quit,
                  -## then uncomment these:
                  -#bind ^Q exit all
                  -#bind ^S savefile main
                  -#bind ^W writeout main
                  -#bind ^O insert main
                  -#bind ^H help all
                  -#bind ^H exit help
                  -#bind ^F whereis all
                  -#bind ^G findnext all
                  -#bind ^B wherewas all
                  -#bind ^D findprevious all
                  -#bind ^R replace main
                  -#bind M-X flipnewbuffer all
                  -#bind ^X cut all
                  -#bind ^C copy main
                  -#bind ^V paste all
                  -#bind ^P location main
                  -#bind ^A mark main
                  -#unbind ^K main
                  -#unbind ^U all
                  -#unbind ^N main
                  -#unbind ^Y all
                  -#unbind M-J main
                  -#unbind M-T main
                  -#bind ^T gotoline main
                  -#bind ^T gotodir browser
                  -#bind ^Y speller main
                  -#bind M-U undo main
                  -#bind M-R redo main
                  -#bind ^U undo main
                  -#bind ^E redo main
                  -#set multibuffer

Summary for local
------------
Succeeded: 1 (changed=1)
Failed:    0
------------
Total states run:     1
Total run time:  31.525 ms
```

### sshd -moduuli<a href="06.12.2021sshd"></a>

Seuraavaksi tein ssh-demonin ja sille tilan konfiguraatioineen. Olen tehnyt aikaisemmin [harjoituksessa 5 kohdassa a)](https://github.com/tuuli-huhtanen/palvelintenhallinta/blob/main/h5-demonit.md#a). Kopioin sshd-konfiguraatio tiedoston Saltin juureen ja määritän portiksi oletusportin 22.

Lopetin tähän noin klo 21. Jatkoin klo 22.

Minulla oli jo aikaisemmin luotu hakemisto `/srv/salt/ssh` -hakemisto, jossa oli luotuna init.sls -tiedosto ja oletusasetuksineen oleva `sshd_config`-tiedosto, joka on haettu `/etc/ssh`-hakemistosta. Asetin portin 22 asetustiedostoon ja siivosin sen ylimääräisestä:

```
# init.sls -tiedoston sisältö oli seuraava:

$ cat /srv/salt/ssh/init.sls 
openssh-server:
  pkg.installed

/etc/ssh/sshd_config:
  file.managed:
    - source: salt://ssh/sshd_config

sshd:
  service.running:
    - enable: True
    - watch:
      - file: /etc/ssh/sshd_config

# Siivosin sshd_config -tiedoston ylimääräisestä:

$ cd /srv/salt/ssh; cat sshd_config |grep -v ^#|grep -v ^$| sudo tee sshd_configtmp sshd_configtmp sshd_config

# Tarkistin:

/srv/salt/ssh$ cat sshd_config
Include /etc/ssh/sshd_config.d/*.conf
Port 22
ChallengeResponseAuthentication no
UsePAM yes
X11Forwarding yes
PrintMotd no
AcceptEnv LANG LC_*
Subsystem	sftp	/usr/lib/openssh/sftp-server

# Poistin ylimääräisen sshd_configtmp -tiedoston:

$ sudo rm /srv/salt/ssh/sshd_configtmp 

# Tarkistin tiedostorakenteen vielä:

$ tree /srv/salt/ssh
/srv/salt/ssh
├── init.sls
└── sshd_config

# Ajoin paikallisesti testiksi ja testi meni läpi muutoksineen:

$ sudo salt-call --local state.apply ssh
local:
----------
          ID: openssh-server
    Function: pkg.installed
      Result: True
     Comment: All specified packages are already installed
     Started: 22:43:23.899389
    Duration: 48.723 ms
     Changes:   
----------
          ID: /etc/ssh/sshd_config
    Function: file.managed
      Result: True
     Comment: File /etc/ssh/sshd_config updated
     Started: 22:43:23.950050
    Duration: 20.774 ms
     Changes:   
              ----------
              diff:
                  --- 
                  +++ 
                  @@ -1,123 +1,8 @@
                  -#	$OpenBSD: sshd_config,v 1.103 2018/04/09 20:41:22 tj Exp $
                  -
                  -# This is the sshd server system-wide configuration file.  See
                  -# sshd_config(5) for more information.
                  -
                  -# This sshd was compiled with PATH=/usr/bin:/bin:/usr/sbin:/sbin
                  -
                  -# The strategy used for options in the default sshd_config shipped with
                  -# OpenSSH is to specify options with their default value where
                  -# possible, but leave them commented.  Uncommented options override the
                  -# default value.
                  -
                   Include /etc/ssh/sshd_config.d/*.conf
                  -
                   Port 22
                  -#AddressFamily any
                  -#ListenAddress 0.0.0.0
                  -#ListenAddress ::
                  -
                  -#HostKey /etc/ssh/ssh_host_rsa_key
                  -#HostKey /etc/ssh/ssh_host_ecdsa_key
                  -#HostKey /etc/ssh/ssh_host_ed25519_key
                  -
                  -# Ciphers and keying
                  -#RekeyLimit default none
                  -
                  -# Logging
                  -#SyslogFacility AUTH
                  -#LogLevel INFO
                  -
                  -# Authentication:
                  -
                  -#LoginGraceTime 2m
                  -#PermitRootLogin prohibit-password
                  -#StrictModes yes
                  -#MaxAuthTries 6
                  -#MaxSessions 10
                  -
                  -#PubkeyAuthentication yes
                  -
                  -# Expect .ssh/authorized_keys2 to be disregarded by default in future.
                  -#AuthorizedKeysFile	.ssh/authorized_keys .ssh/authorized_keys2
                  -
                  -#AuthorizedPrincipalsFile none
                  -
                  -#AuthorizedKeysCommand none
                  -#AuthorizedKeysCommandUser nobody
                  -
                  -# For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
                  -#HostbasedAuthentication no
                  -# Change to yes if you don't trust ~/.ssh/known_hosts for
                  -# HostbasedAuthentication
                  -#IgnoreUserKnownHosts no
                  -# Don't read the user's ~/.rhosts and ~/.shosts files
                  -#IgnoreRhosts yes
                  -
                  -# To disable tunneled clear text passwords, change to no here!
                  -#PasswordAuthentication yes
                  -#PermitEmptyPasswords no
                  -
                  -# Change to yes to enable challenge-response passwords (beware issues with
                  -# some PAM modules and threads)
                   ChallengeResponseAuthentication no
                  -
                  -# Kerberos options
                  -#KerberosAuthentication no
                  -#KerberosOrLocalPasswd yes
                  -#KerberosTicketCleanup yes
                  -#KerberosGetAFSToken no
                  -
                  -# GSSAPI options
                  -#GSSAPIAuthentication no
                  -#GSSAPICleanupCredentials yes
                  -#GSSAPIStrictAcceptorCheck yes
                  -#GSSAPIKeyExchange no
                  -
                  -# Set this to 'yes' to enable PAM authentication, account processing,
                  -# and session processing. If this is enabled, PAM authentication will
                  -# be allowed through the ChallengeResponseAuthentication and
                  -# PasswordAuthentication.  Depending on your PAM configuration,
                  -# PAM authentication via ChallengeResponseAuthentication may bypass
                  -# the setting of "PermitRootLogin without-password".
                  -# If you just want the PAM account and session checks to run without
                  -# PAM authentication, then enable this but set PasswordAuthentication
                  -# and ChallengeResponseAuthentication to 'no'.
                   UsePAM yes
                  -
                  -#AllowAgentForwarding yes
                  -#AllowTcpForwarding yes
                  -#GatewayPorts no
                   X11Forwarding yes
                  -#X11DisplayOffset 10
                  -#X11UseLocalhost yes
                  -#PermitTTY yes
                   PrintMotd no
                  -#PrintLastLog yes
                  -#TCPKeepAlive yes
                  -#PermitUserEnvironment no
                  -#Compression delayed
                  -#ClientAliveInterval 0
                  -#ClientAliveCountMax 3
                  -#UseDNS no
                  -#PidFile /var/run/sshd.pid
                  -#MaxStartups 10:30:100
                  -#PermitTunnel no
                  -#ChrootDirectory none
                  -#VersionAddendum none
                  -
                  -# no default banner path
                  -#Banner /etc/ssh/sshd-banner
                  -
                  -# Allow client to pass locale environment variables
                   AcceptEnv LANG LC_*
                  -
                  -# override default of no subsystems
                   Subsystem	sftp	/usr/lib/openssh/sftp-server
                  -
                  -# Example of overriding settings on a per-user basis
                  -#Match User anoncvs
                  -#	X11Forwarding no
                  -#	AllowTcpForwarding no
                  -#	PermitTTY no
                  -#	ForceCommand cvs server
----------
          ID: sshd
    Function: service.running
      Result: True
     Comment: Service restarted
     Started: 22:43:23.991151
    Duration: 48.467 ms
     Changes:   
              ----------
              sshd:
                  True

Summary for local
------------
Succeeded: 3 (changed=2)
Failed:    0
------------
Total states run:     3
Total run time: 117.964 ms

```

Lopetin harjoituksen noin klo 23:00

### ufw -moduuli<a href="06.12.2021nano"></a>

Olin [harjoituksessa 5 kohdassa a)](https://github.com/tuuli-huhtanen/palvelintenhallinta/blob/main/h5-demonit.md#a) jo aloittanut palomuuritilan tekemisen, mutten ollut onnistunut, joten yritän saada sen nyt onnistumaan.

